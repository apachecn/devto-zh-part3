# 杂项 C#异步提示

> 原文：<https://dev.to/stuartblang/miscellaneous-c-async-tips-3o47>

有很多很棒的异步技巧，这里是我在过去几年里收集的一些，我没有看到太多关于它们的文章。

### 用弃用`Task.Run`为火而忘

这是一点小小的指导，但却是我最喜欢的指导之一。如果您使用`Task.Run`以一劳永逸的方式启动`Task`，代码的其他读者可能会想“他们是不是忘了在这里`await`返回的任务？”。

你可以用注释来表达你的意图，但是一个非常简洁明了的方式来表明这就是你的意图，那就是使用 C# 7.0 中的丢弃特性 :

```
Task.Run(() => SomeBackgroundWork()); // Fire and forget

// becomes

_ = Task.Run(() => SomeBackgroundWork()); 
```

当然，如果你需要澄清什么，就注释，但是在这个例子中，我认为省略注释是有意义的，因为它留下的噪音更少。

## 替换`Task.Factory.StartNew`

在`Task.Run`出现之前,`Task.Factory.StartNew`经常被使用，当你处理异步代码时,`StartNew`可能会有点误导，因为它代表了异步委托的初始*同步*部分，因为它们没有使用`Func<Task<T>>`的重载。

斯蒂芬·克利里甚至[甚至说它是危险的](https://blog.stephencleary.com/2013/08/startnew-is-dangerous.html)，并且只有很少的有效用例，包括能够为将阻塞的工作启动`LongRunning`任务，以防止阻塞您宝贵的线程池线程。巴尔·阿农[指出，这几乎不能与异步代码](http://blog.i3arnon.com/2015/07/02/task-run-long-running/)很好地融合。简而言之,`StartNew`很少有用，重要的是会引起混乱。

我的第一个建议是，无论你在哪里看到以下内容

```
Task.Factory.StartNew(/* Something */).Unwrap() 
```

为了让代码的其他读者更熟悉它，您可以将其替换为:

```
Task.Run(/* Something */) 
```

如果你看到没有`Unwrap()`的用法，有可能它没有做你认为它在做的事情。或者，如果您使用它来启动带有自定义`TaskCreationOptions`的任务，请确保它是您的场景所必需的。为了清楚起见，这里有一个罕见的场景是有意义的:

```
Task.Factory.StartNew(() =>
{
    while (true)
    {
        Thread.Sleep(1000); // Some blocking work you wouldn't want happening on your threadpool threads.
        DoSomething();
    }
}, TaskCreationOptions.LongRunning); 
```

### 异步&懒惰

人们在将同步代码迁移到异步代码时遇到的一个常见问题是，他们有惰性启动的属性或单例，现在需要异步初始化。在同步世界中，你的工具箱中有一个很好的工具来处理这个问题，那就是`Lazy<T>`，但是，我们如何将它用于异步和任务呢？

默认情况下，`Lazy<T>`使用设置`LazyThreadSafetyMode.ExecutionAndPublication`，这意味着它是线程安全的，如果多个并发线程试图访问该值，只有一个线程触发创建，其他线程都等待该值变得可用。

有趣的是，这就是`Task<T>`等待的方式。如果一个线程创建了一个`Task`实例，其他线程在它完成之前等待它，它们等待该值变得可用，而不额外执行相同的工作。然而，我们需要一种线程安全的方式来创建`Task`实例，所以我们可以像这样将它与`Lazy<T>`结合起来:

```
readonly Lazy<Task<string>> _lazyAccessToken = new Lazy<Task<string>>(async () => {
    return await RetrieveAccessTokenAsync();
});

public Task<string> AccessToken => _lazyAccessToken.Value; 
```

为了清楚起见，我写得很冗长，但是我们可以通过将语句变成 lambda，省略 await，并用如下的方法组替换对`RetrieveAccessTokenAsync`的调用来减少这种情况

```
readonly Lazy<Task<string>> _lazyAccessToken = new Lazy<Task<string>>(RetrieveAccessTokenAsync);

public Task<string> AccessToken => _lazyAccessToken.Value; 
```

请注意，推迟访问`.Value`很重要。

我们可以封装成这样的类型:

```
internal sealed class AsyncLazy<T> : Lazy<Task<T>>
{
    public AsyncLazy(Func<Task<T>> taskFactory) :
    base(taskFactory) { }
} 
```

或者我们可以使用来自微软的伟大的 [vs-threading](https://github.com/Microsoft/vs-threading/) 库(如果你能越过 VisualStudio 名称空间的话)，它包含了许多精心设计的异步原语，比如你几乎期望包含在框架中的`AsyncLazy<T>`和它的一部分。净标准。

同样，值得一提的是斯蒂芬·克利里在`Nito.AsyncEx.Coordination`中定义的[不对称](https://github.com/StephenCleary/AsyncEx/blob/daa22bf7721abd182201661cf7414e7d614ffde6/src/Nito.AsyncEx.Coordination/AsyncLazy.cs)。

如果你不想让它变得懒惰，另一种策略是在构造函数中急切地给一个字段或属性分配一个任务，这个任务已经开始但没有等待，就像这样:

```
class ApiClient
{
    public ApiClient()
    {
        AccessToken = RetrieveAccessTokenAsync();
    }

    public Task<string> AccessToken { get; }
    // here AccessToken is a hot Task, that may or may not be complete by the time it's first accessed 
```

### 异步&锁

在迁移同步代码时，另一个令人困惑的事情是处理`lock`块，异步代码不能去那里。首先我说，你想达到什么目的？如果是资源或单例的线程安全初始化，那么使用上面提到的 Lazy/AsyncLazy，你的代码可能会更干净，更好地表达你的意图。

如果您确实需要包含异步代码的`lock`块，请尝试使用`SemaphoreSlim`。`SemaphoreSlim`做的比`Monitor`和`lock`块多一点，但是你可以用同样的方式使用它。通过将`SemaphoreSlim`初始化为初始值 1 和最大值 1，我们可以使用它来获得对某些代码区域的线程安全门控访问，从而保证独占执行。

```
_semaphore = new SemaphoreSlim(1,1) // initial: 1, max: 1 

async Task MyMethod(CancellationToken cancellationToken = default)
{
    await _semaphore.WaitAsync(cancellationToken);
    try
    {
        // the following line will only ever be called by one thread at any time
        await SomethingAsync();
    }
    finally
    {
        _semaphore.Release();
    }
} 
```

同样酷的是，`SemaphoreSlim`除了`WaitAsync`还有一个同步的`Wait`方法，所以我们可以在既需要同步访问又需要异步访问的场景中使用它(当然要小心)。

从`Nito.AsyncEx.Coordination`中找出`AsyncLock`也是值得的。

就这些了，我知道这是一种随意的搭配。

**更新:**感谢 Thomas Levesque 在评论中指出`SemaphoreSlim`不同于`Monitor` / `lock`，因为它不可重入。这意味着从已经获得锁的线程那里获得锁是没有问题的，而使用`SemaphoreSlim`在同样的情况下，您将最终死锁自己。

odinserj 在评论中的另一个警告反馈是。NET Framework 中，线程中止异常可能会使信号量永远耗尽，所以它不是防弹的。