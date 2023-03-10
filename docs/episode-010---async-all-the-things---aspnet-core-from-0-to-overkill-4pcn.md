# 第 010 集-异步所有的事情-ASP.NET 核心:从 0 到矫枉过正

> 原文：<https://dev.to/joaofbantunes/episode-010---async-all-the-things---aspnet-core-from-0-to-overkill-4pcn>

在这一集里，我们来看看在 async 内核中使用 async await，为什么它很重要，快乐的道路和一些悲伤的道路，以及一些有趣的事情。对于演练，您可以查看接下来的几个视频，但是如果您喜欢快速阅读，请跳到书面综合。

主视频:
[https://www.youtube.com/embed/CGi1bQgaqwg](https://www.youtube.com/embed/CGi1bQgaqwg)

后来加了一个简短的补遗:
[https://www.youtube.com/embed/bWyB0VFKeKA](https://www.youtube.com/embed/bWyB0VFKeKA)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

让我们以不同于其他帖子的方式开始这篇帖子。我们为什么不直接去做 async/await 之类的事情呢？
当我们不再为这些`async`、`await`、`Task`等等烦恼时，生活变得更加轻松😛

从 web 应用程序的角度来看，所有这些对于提高可伸缩性都很重要，因为不会无缘无故地阻塞大量资源(线程)(其他类型的应用程序可能有不同的原因，比如桌面应用程序不会阻塞 UI 线程)。

甚至在引入带有 TPL ( [任务并行库](https://docs.microsoft.com/en-us/dotnet/standard/parallel-programming/task-parallel-library-tpl%5D))的`Task`以及随后出现的 TAP ( [基于任务的异步模式](https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap))之前，就有其他方法来实现这一点，即使用 APM ( [异步编程模型](https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm?view=netframework-4.7.2))，但是做起来并不那么简单。就像 JS 最近引入 async/await 来简化 promises 的用法一样。NET 不久前也做了同样的事情。

我将在这里放几张图，从很高的层面概述非异步场景和异步场景中的请求处理行为。如果你想对这些图表有更全面的解释，请查看我在帖子开头链接/嵌入的第一个视频。

**不使用异步/等待(或类似方法)**

[![pre-async](img/9ae9cc808548dc4a13f52d1bc706afab.png)](https://thepracticaldev.s3.amazonaws.com/i/0354pdhc87zj1eh7j8pv.jpg)

**使用异步/等待(或类似方法)**

[![async](img/6f4159cda080735f99a78e577ffb641a.png)](https://thepracticaldev.s3.amazonaws.com/i/39q9nwf5xpxkamkre0vp.jpg)

总之，在 async 内核中使用 async/await 允许我们通过释放资源来处理其他任务，例如，在进行数据库访问或外部服务调用时，简化更具可伸缩性的代码的编写，特别是在 IO 方面。

## 使服务异步

既然我们对为什么关心这些有了更好的理解(不管怎样，可能您已经有了)，让我们开始将我们现有的代码更改为异步的，这是有意义的，为用实际的数据库替换内存组“持久性”做准备。

### 适配界面

回顾组服务的当前同步接口，我们有:

`IGroupsService.cs`

```
public interface IGroupsService
{
    IReadOnlyCollection<Group> GetAll();

    Group GetById(long id);

    Group Update(Group group);

    Group Add(Group group);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将把所有的方法签名改为异步签名，因为我们希望它们都执行 IO。如果一个方法不需要异步，那么就不要使用它🙂

根据经验，每个方法都应该返回一个`Task<T>`(或者如果无效的话返回一个`Task`)，并以`Async`作为后缀(尽管有些人不喜欢使用这个后缀)。

**注意:**还有一些其他的选项，我们不会在这篇文章中讨论

*   有`async void`方法，但这在其他类型的应用中可能有用，比如 WinForms 或 Xamarin，但在 ASP.NET 核心中不太有用，[在那里它可能只会带来问题](https://msdn.microsoft.com/en-us/magazine/jj991977.aspx)
*   返回`ValueTask<T>`(或`ValueTask`)而不是`Task`，这是最近增加的功能，对于高性能非常重要的场景特别有用，避免分配会带来很大的不同

接口的异步版本如下所示:

`IGroupsService.cs`

```
public interface IGroupsService
{
    Task<IReadOnlyCollection<Group>> GetAllAsync();

    Task<Group> GetByIdAsync(long id);

    Task<Group> UpdateAsync(Group group);

    Task<Group> AddAsync(Group group);
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，通过改变接口，我们也必须改变调用代码，在本例中是在`GroupsController`中。我们将只看一下(之前命名的)`Index`方法，因为其他方法遵循相同的方法(但是你可以在 [GitHub](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode010) 上查看所有代码)。

`GroupsController.cs`

```
public class GroupsController : Controller
{
    private readonly IGroupsService _groupsService;

    public GroupsController(IGroupsService groupsService)
    {
        _groupsService = groupsService;
    }

    [HttpGet]
    [Route("")]
    public async Task<IActionResult> IndexAsync()
    {
        var result = await _groupsService.GetAllAsync();
        return View("Index", result.ToViewModel());
    }

    //...

} 
```

Enter fullscreen mode Exit fullscreen mode

因此，考虑到我们已经讨论过的内容，`Index`变成了`IndexAsync`，尽管在这种情况下，我认为将它留在`Index`可能是一个简化一些事情的好选择，因为这迫使视图名称(映射到`Index.cshtml`)在调用`View`方法时作为参数传递，因为默认情况下 te 动作和视图应该具有相同的名称(这可能会在[ASP.NET 核心 3.0](https://github.com/aspnet/Mvc/issues/6723) 中改变)。

除此之外，方法签名现在有了一个`async`修饰符，表示实现中将有一个`await`，返回类型现在是`Task<IActionResult>`。

在方法实现中，现在当调用`_groupsService.GetAllAsync()`时，我们`await`它的结果。

### 实现服务

随着接口和客户端代码的更新，我们需要更新我们的`InMemoryGroupsService`。因为我们实际上没有异步的事情要做，也就是说，我们还没有进入数据库，因为我们已经将组数据存储在内存中，所以服务的实现将伪造异步部分。在下一篇文章中，当我们将实体框架核心添加到组合中时，我们将再次调整服务。

让我们再来看看非异步版本:

`InMemoryGroupsService.cs`

```
public class InMemoryGroupsService : IGroupsService
{
    private readonly List<Group> _groups = new List<Group>();
    private long _currentId = 0;

    public IReadOnlyCollection<Group> GetAll()
    {
        return _groups.AsReadOnly();
    }

    public Group GetById(long id)
    {
        return _groups.SingleOrDefault(g => g.Id == id);
    }

    public Group Update(Group group)
    {
        var toUpdate = _groups.SingleOrDefault(g => g.Id == group.Id);

        if (toUpdate == null)
        {
            return null;
        }

        toUpdate.Name = group.Name;
        return toUpdate;
    }

    public Group Add(Group group)
    {
        group.Id = ++_currentId;
        _groups.Add(group);
        return group;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

将它移到(假的)异步版本，我们所做的就是改变所有方法的签名，以匹配接口，而不是直接返回结果，我们通过使用`Task.FromResult`方法将它包装在一个完成的任务中。

`InMemoryGroupsService.cs`

```
public class InMemoryGroupsService : IGroupsService
{
    private static readonly Random RandomGenerator = new Random();
    private readonly List<Group> _groups = new List<Group>();
    private long _currentId = 0;

    public Task<IReadOnlyCollection<Group>> GetAllAsync(CancellationToken ct)
    {
        return Task.FromResult<IReadOnlyCollection<Group>>(_groups.AsReadOnly());
    }

    public Task<Group> GetByIdAsync(long id, CancellationToken ct)
    {
        return Task.FromResult(_groups.SingleOrDefault(g => g.Id == id));
    }

    public Task<Group> UpdateAsync(Group group, CancellationToken ct)
    {
        var toUpdate = _groups.SingleOrDefault(g => g.Id == group.Id);

        if (toUpdate == null)
        {
            return Task.FromResult(null);
        }

        toUpdate.Name = group.Name;
        return Task.FromResult(toUpdate);
    }

    public Task<Group> AddAsync(Group group, CancellationToken ct)
    {
        group.Id = ++_currentId;
        _groups.Add(group);
        return Task.FromResult(group);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当实现一个异步接口，但其中没有异步要做的事情时，`Task.FromResult`是返回`Task<T>`的方法，`Task.CompletedTask`是返回`Task`的方法。

### 无需任务。奔跑

看着上面的`InMemoryGroupsService`实现，有人可能会问，为什么不像下面这样做呢:

`InMemoryGroupsService.cs`

```
public class InMemoryGroupsService : IGroupsService
{
    //...

    public async Task<IReadOnlyCollection<Group>> GetAllAsync(CancellationToken ct)
    {
        return await Task.Run(() => _groups.AsReadOnly());
    }

    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

这也实现了异步接口，但不是返回已完成的任务，而是要求线程池运行传递给另一个线程中的`Task.Run`的代码，然后我们等待它的结果。嗯，我会说这只是在浪费资源，我们已经准备好了，在另一个线程中运行它只是为了让它异步是浪费的。

让我们来看一个不太明显的场景。假设我们已经在进行数据库调用，但是我们使用的提供者由于某种原因不支持异步。我们可以这样做:

`InMemoryGroupsService.cs`

```
public class InMemoryGroupsService : IGroupsService
{
    //...

    public async Task<IReadOnlyCollection<Group>> GetAllAsync(CancellationToken ct)
    {
        return await Task.Run(() => GetAllUsingSyncDbProvider());
    }

    private IReadOnlyCollection<Group> GetAllUsingSyncDbProvider()
    {
        //...
    }

    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，这似乎是一个好主意，我们通过让它在另一个线程中运行，使同步数据库访问成为异步的。我认为，这又是浪费资源。我们要求另一个线程阻塞，而不是阻塞处理请求的线程。阻塞从来都不是好事，但是因为没有其他方法，我会说在请求处理线程中做就好了，不需要上下文切换开销。

`Task.Run`应该用来做 CPU 绑定的工作，最终并行做一些事情。请记住，我在这里谈论的是 ASP.NET 核心，例如在桌面和 Xamarin 应用程序中，使用`Task.Run`来确保 UI 线程不被阻塞可能很重要(即使是在异步方法上，因为即使是异步方法也可能有同步运行的部分)。

如果我在这一点上弄错了，并且你觉得在我遗漏的这些场景中使用`Task.Run`有优势，请联系我，我很感激🙂

你可以点击查看更多关于`Task.Run` [的信息。](https://blog.stephencleary.com/2013/11/taskrun-etiquette-examples-dont-use.html)

PS: `Task.Run`也可以用来做一些同步上下文的恶作剧，但这在 ASP.NET 核心中并不真正有用(除此之外，基本上是一个黑客)。

### 使用注销令牌

您可以添加到我们的异步方法中的一件好事是取消正在进行的操作的可能性。这是使用`CancellationToken` s 完成的。

取消标记应该作为参数传递给异步函数(按照惯例，是最后一个参数)。这将使组服务接口看起来像这样:

`IGroupsService.cs`

```
public interface IGroupsService
{
    Task<IReadOnlyCollection<Group>> GetAllAsync(CancellationToken ct);

    Task<Group> GetByIdAsync(long id, CancellationToken ct);

    Task<Group> UpdateAsync(Group group, CancellationToken ct);

    Task<Group> AddAsync(Group group, CancellationToken ct);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在实现中，我们可以将它传递给异步方法调用。因为当前的实现伪造了异步部分，所以我将添加一个延迟来伪造一个使用取消令牌的 IO 操作(为了简单起见，我只包含了一个方法作为示例)。

`InMemoryGroupsService.cs`

```
public class InMemoryGroupsService : IGroupsService
{
    //...

    public async Task<IReadOnlyCollection<Group>> GetAllAsync(CancellationToken ct)
    {
        await Task.Delay(5000, ct);
        return _groups.AsReadOnly();
    }

    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个，如果在这 5 秒内请求被取消，就会抛出一个`OperationCancelledException`，停止当前正在执行的代码。导致该异常的另一种方法是调用`ct.ThrowIfCancellationRequested()`。如果我们在一个长时间运行的操作场景中，并且我们想要检查我们是否应该继续，这是很有用的。

现在我们有了准备被取消的代码，它是如何发生的呢？在 ASP.NET 核心中，很容易很好地利用这一点，我们在控制器上添加一个取消令牌到动作方法中，框架会将它传入。

`GroupsController.cs`

```
public class GroupsController : Controller
{
    private readonly IGroupsService _groupsService;

    public GroupsController(IGroupsService groupsService)
    {
        _groupsService = groupsService;
    }

    [HttpGet]
    [Route("")]
    public async Task<IActionResult> IndexAsync(CancellationToken ct)
    {
        var result = await _groupsService.GetAllAsync(ct);
        return View("Index", result.ToViewModel());
    }

    //...

} 
```

Enter fullscreen mode Exit fullscreen mode

要看到这一点，我们可以到浏览器，提出一个请求，并在它完成之前点击取消按钮。现在，如果我们查看日志，我们会看到抛出的异常。当然，有些事情可能不会被取消，例如，如果一个有副作用的操作完成了，也许不应该突然停止，但是在其他情况下，这种模式工作得很好(即使总是传递`CancellationToken`参数可能有点烦人)。

## 同步调用它(但不要)

我们应该尽可能地避免同步调用异步方法，但是我们知道有时候，由于一些奇怪的原因，我们就是不能。在这种情况下，我们至少可以尽力摆脱困境。

同步获得`Task<T>`结果的通常方式是通过访问`Result`属性。关于这一点的一件事是，如果一个异常发生了，我们得到的不是预期的异常，而是一个`AggregateException`，它包装了异步方法中抛出的任何异常。当我们等待任务时，这不会发生，因为它打开了异常。除了`Result`，我们还可以做`DoSomethingAsync().GetAwaiter().GetResult()`，因为这将处理异常的解包。这并不能解决我们阻塞线程等待结果的问题，也不能解决我们在某些情况下可能会死锁的问题(例如在 ASP.NET 预核中)，但至少我们得到了正确的异常。

## configurewait(假)

继续这个主题(但不是唯一的)，为什么在异步方法上阻塞会导致死锁(在某些情况下，但不是在 ASP.NET 核心中)的原因是有一个`SynchronizationContext`被阻塞的线程持有，当异步方法完成时，试图获得该上下文但不能，因为另一个线程拥有它。

这个上下文是相关的，例如，在桌面和 Xamarin 应用程序中，异步延续在 UI 线程上运行以做出一些可见的改变是很重要的。因为 ASP.NET 核心没有这些种类的需求，`SynchronizationContext`被一起移除了。

即使知道这在 ASP.NET 核心中不是问题，如果我们写了一个我们最终想要共享的库，我们也不知道什么样的应用程序会使用它。所以当开发这样的库时，当代码不依赖于这个上下文时，通常的做法是在等待任务时使用`ConfigureAwait(false)`。这意味着`await`之后的代码不需要上下文，所以不需要尝试和获取，这意味着表现不佳的客户端不会出现死锁(可能还有其他好处，比如小的性能提升，在这里阅读更多信息[)。](https://msdn.microsoft.com/en-us/magazine/jj991977.aspx)

## 不等待返回任务

如果您在中使用了异步/等待。NET 中，你可能遇到过这样的情况，你有一个异步方法，在这个异步方法中，它唯一异步做的事情是最后一行，那一行的输出就是它将返回的内容。大概是:

```
public async Task<bool> CheckSomethingAsync()
{
    return await InnerCheckSomethingAsync();
}

private async Task<bool> InnerCheckSomethingAsync()
{
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，难道我们不能忽略 async/await 部分，只返回另一个方法的结果吗？

```
public Task<bool> CheckSomethingAsync()
{
    return InnerCheckSomethingAsync();
}

//... 
```

Enter fullscreen mode Exit fullscreen mode

和其他许多情况一样，这要看情况。在类似上面的场景中，这可能是可以接受的，但是我们必须记住，如果发生异常，堆栈跟踪将会不同，就好像根本没有调用`CheckSomethingAsync`。让我们来看几个示例堆栈跟踪。

带异步/等待:

```
 at Program.<InnerCheckSomethingAsync>d__3.MoveNext() in d:\Windows\Temp\kw03shrz.0.cs:line 28
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter`1.GetResult()
   at Program.<CheckSomethingAsync>d__0.MoveNext() in d:\Windows\Temp\kw03shrz.0.cs:line 22
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter`1.GetResult()
   at Program.Main() in d:\Windows\Temp\kw03shrz.0.cs:line 12 
```

Enter fullscreen mode Exit fullscreen mode

立即返回任务:

```
 at Program.<InnerCheckSomethingAsync>d__0.MoveNext() in d:\Windows\Temp\uh2ndf5e.0.cs:line 28
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter`1.GetResult()
   at Program.Main() in d:\Windows\Temp\uh2ndf5e.0.cs:line 12 
```

Enter fullscreen mode Exit fullscreen mode

这是否可以接受要看情况。通过立即返回任务，我们丢失了一些信息，但获得了一些性能，因为我们绕过了在`CheckSomethingAsync`中创建异步状态机。视情况而定，一个可能比另一个更重要。

除此之外，至少还有一些其他的事情要处理。如果对`InnerCheckSomethingAsync`的调用实际上是在一个 try/catch 或 using 块中(也许还有其他我现在不记得的块)，我们不能立即返回，因为一些代码可能在块不再在作用域中之后运行。

一个例子

```
public static Task<bool> CheckSomethingAsync()
{
    try 
    {
        return InnerCheckSomethingAsync();
    } 
    catch(Exception ex)
    {
        //act on exception
    }
}

private static async Task<bool> InnerCheckSomethingAsync()
{
    await Task.Delay(1000);
    throw new Exception("Sample error");
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，异常不会被捕获，因为`CheckSomethingAsync`已经将`InnerCheckSomethingAsync`任务返回给它的调用者。

## 并行发出请求

### 如何

async 的本质，更具体地说，在发出请求时使用 async 使得并行化它们变得更加容易，而不需要弄乱线程(至少显式地)。

让我们以一种方法`CallExternalServiceAsync`为例。假设我们想向它发出几个不相关的请求，而不是立即等待它们，我们可以调用该方法并将返回的`Task`存储在变量中，稍后再等待它们，允许它们并行进行。如果我们立即等待它们，请求将被序列化，而不是并行。示例代码:

`InMemoryGroupsService.cs`

```
public class InMemoryGroupsService : IGroupsService
{
    private static readonly Random RandomGenerator = new Random();

    //...

    public async Task<Group> GetByIdAsync(long id, CancellationToken ct)
    {
        var extResult1Task = CallExternalServiceAsync(ct);
        var extResult2Task = CallExternalServiceAsync(ct);

        var result1 = await extResult1Task;
        var result2 = await extResult2Task;

        return _groups.SingleOrDefault(g => g.Id == id);
    }

    //...

    private async Task<int> CallExternalServiceAsync(int multiplier, CancellationToken ct)
    {
        await Task.Delay(1000);
        return RandomGenerator.Next();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过以这种方式执行“请求”,而不是像我们做`await CallExternalServiceAsync(ct)`,序列化代码执行那样需要大约 2 秒来运行代码，它只需要大约 1 秒，因为一旦代码的同步部分允许，我们就开始两个异步操作。

请注意，我们只能在目标代码允许的情况下这样做，例如，如果使用相同的服务类实例是线程安全的。举几个例子:

*   当使用一个`HttpClient`来发出请求时，我们可以毫无问题地做到这一点，只要我们只使用线程安全的方法(在编写时:`DeleteAsync`、`GetAsync`、`GetByteArrayAsync`、`GetStreamAsync`、`GetStringAsync`、`PostAsync`、`PutAsync`和`SendAsync`——见这里的)
*   当使用实体框架核心时，`DbContext`不是线程安全的，所以在同一个实例上并行调用操作不是一个好主意

另一件要记住的事情是，当调用另一个服务时，要小心不要滥用它，因为我们可能会无意中导致拒绝服务(尽管有一些保护措施来避免对同一主机的太多请求，请在此阅读更多)。

### 等待所有请求完成

关于等待任务完成，上面的方法是最简单的，但是我们有其他的选择，比如`Task.WhenAll`和`Task.WhenAny`。

`Task.WhenAll`将基本上与上面的代码一样，但是可以得到一个`Task`集合来等待，而不是必须单独`await`每个。例如，如果我们想要将返回的任务分组到一个集合中，这也会很有用。通过修改上面的例子，我们得到:

`InMemoryGroupsService.cs`

```
public class InMemoryGroupsService : IGroupsService
{
    //...

    public async Task<Group> GetByIdAsync(long id, CancellationToken ct)
    {
        await Task.Delay(1000, ct);
        var extResult1Task = CallExternalServiceAsync(1, ct);
        var extResult2Task = CallExternalServiceAsync(2, ct);

        await Task.WhenAll(extResult1Task, extResult2Task);

        return _groups.SingleOrDefault(g => g.Id == id);
    }

    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个选择是`Task.WhenAny`，尽管目标不同。顾名思义，它会等待任务，就像`WhenAll`一样，但是在这种情况下，只要其中一个任务完成，代码就会继续执行。

```
public class InMemoryGroupsService : IGroupsService
{
    //...

    public async Task<Group> GetByIdAsync(long id, CancellationToken ct)
    {
        var extResult1Task = CallExternalServiceAsync(1, ct);
        var extResult2Task = CallExternalServiceAsync(2, ct);

        await Task.WhenAny(extResult1Task, extResult2Task);

        return _groups.SingleOrDefault(g => g.Id == id);
    }

    //...

    private async Task<int> CallExternalServiceAsync(int multiplier, CancellationToken ct)
    {
        await Task.Delay(1000 * multiplier);
        return RandomGenerator.Next();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，`Task.WhenAll`将等待大约 2 秒钟，因为这是运行时间最长的呼叫。`Task.WhenAny`另一方面，大约需要 1 秒钟，因为这是最快通话的持续时间。

## 任务完成来源

虽然我要离开 async/await 的“基础”领域，但我认为我们要讨论的最后几个主题是很有趣的，即使不是经常需要的(当然取决于我们正在开发什么)。

从`TaskCompletionSource`开始，它允许我们创建一种“手动”完成`Task`的方法，因为我们习惯于简单地等待它们，由一些已经存在的实现提供，如`HttpClient`或`DbContext`。为了展示这是如何工作，让我们创建一种带有控制器的队列。

### 创建 TaskCompletionSource 并设置结果

`DemoNotRecommendedQueueController.cs`

```
[Route("queue")]
public class DemoNotRecommendedQueueController : Controller
{
    private static readonly ConcurrentQueue<TaskCompletionSource<int>> TaskCompletionSourceQueue 
        = new ConcurrentQueue<TaskCompletionSource<int>>();

    [Route("ask")]
    public async Task<IActionResult> AskAsync()
    {
        var tcs = new TaskCompletionSource<int>();
        TaskCompletionSourceQueue.Enqueue(tcs);
        var result = await tcs.Task;
        return Content(result.ToString());
    }

    [Route("tell/{value}")]
    public IActionResult Tell(int value)
    {
        if (TaskCompletionSourceQueue.TryDequeue(out var tcs))
        {
            if (!tcs.TrySetResult(value))
            {
                return StatusCode(500);        
            }

            return NoContent();
        }

        return NotFound();
    } 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们使用一个`ConcurrentQueue`来存储`TaskCompletionSource<int>`实例。当一个请求到达`/queue/ask`时，一个`TaskCompletionSource<int>`被创建并存储在队列中，然后我们可以在提供的`Task`上`await`。然后我们可以发出一个请求`/queue/tell/1`，它将尝试设置队列中第一个`TaskCompletionSource`的结果，有效地使`AskAsync`动作恢复执行。

需要记住的重要事情是，大卫·福勒[发推文](https://twitter.com/davidfowl/status/1026736063836876800)提醒我们，当我们在`TaskCompletionSource`上设置结果时，默认情况下，等待`Task`的延续会在设置它的线程上立即执行，如果我们没有意识到这一点，这会带来问题。在这些情况下，一个很好的经验法则是通过在创建`TaskCompletionSource`时指定`TaskCreationOptions.RunContinuationsAsynchronously`，让延续在另一个线程上运行。

`DemoNotRecommendedQueueController.cs`

```
[Route("ask")]
public async Task<IActionResult> AskAsync()
{
    var tcs = new TaskCompletionSource<int>(TaskCreationOptions.RunContinuationsAsynchronously);
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

关于使用`TaskCompletionSource`时我们应该注意的问题类型，一篇有趣的文章是 Sergey Teplyakov 的[“任务完成的危险来源< T >级](https://blogs.msdn.microsoft.com/seteplia/2018/10/01/the-danger-of-taskcompletionsourcet-class/)。

### 使任务失败

当然，我们可能不想设置任务的结果，而是取消它或抛出一个异常，我们可以做到这一点。除了`SetResult`(和`TrySetResult`)，我们还有`SetCanceled`(和`TrySetCanceled`)和`SetException`(还有`TrySetException`)。我们来看一个`TrySetCanceled`的例子。

`DemoNotRecommendedQueueController.cs`

```
[Route("queue")]
public class DemoNotRecommendedQueueController : Controller
{
    //...

    [Route("cancel")]
    public IActionResult Cancel()
    {
        if (TaskCompletionSourceQueue.TryDequeue(out var tcs))
        {
            if (!tcs.TrySetCanceled())
            {
                return StatusCode(500);        
            }

            return NoContent();
        }

        return NotFound();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将与我们之前在`CancellationToken` s 中看到的行为相同，以`TaskCanceledException`结束。

## 取消令牌源

说到`CancellationToken`，和`TaskCompletionSource`一样，我们也可以有一个`CancellationTokenSource`，这样我们就可以发出取消操作的信号(就像 MVC 在动作中注入`CancellationToken`一样)。

继续类似于上面的例子，我们有:

```
[Route("queue")]
public class DemoNotRecommendedQueueController : Controller
{
    //...

    private static readonly ConcurrentQueue<CancellationTokenSource> CancellationTokenSourceQueue =
        new ConcurrentQueue<CancellationTokenSource>();

    //...

    [Route("delay/{value}")]
    public async Task<IActionResult> DelayAsync(int value)
    {
        using (var cts = new CancellationTokenSource())
        {
            CancellationTokenSourceQueue.Enqueue(cts);
            await Task.Delay(value, cts.Token);
            CancellationTokenSourceQueue.TryDequeue(out _);
            return Content("Done waiting");
        }
    }

    [Route("delay/cancel")]
    public IActionResult CancelDelay()
    {
        if (CancellationTokenSourceQueue.TryDequeue(out var cts))
        {
            cts.Cancel();
            return Content("Delay cancelled!");
        }

        return NotFound();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

代码与前一个例子非常相似(忽略我没有正确使用`ConcurrentQueue`，因此存在并发问题😛)，但是我们不是在等待`TaskCompletionSource`的任务，而是在等待可以被创建的`CancellationTokenSource`取消的东西。所以为了测试，我们可以去浏览器调用`/queue/delay/10000`，在时间到期之前，调用`/queue/delay/cancel`，导致`DelayAsync`动作突然结束，用通常的`TaskCanceledException`。

## 其他

中的异步、等待和并发编程还有很多。NET，但是我想我已经介绍了它的一些最常用的部分。如果您想深入了解，我会留下一些额外的资源:

*   斯蒂芬·克利里的博客-[https://blog.stephencleary.com/](https://blog.stephencleary.com/)-里面有很多。NET 并发相关的内容，我用了很多作为这一集的参考
    *   [“任务。跑步礼仪举例:不要用 Task。运行在执行"](https://blog.stephencleary.com/2013/11/taskrun-etiquette-examples-dont-use.html)
*   斯蒂芬·克利里·MSDN 关于异步/等待最佳实践的文章- [“异步/等待-异步编程中的最佳实践”](https://msdn.microsoft.com/en-us/magazine/jj991977.aspx) -很可能比我的文章更全面(尽管在 ASP.NET 核心出现之前)
*   David Fowler 的[异步指导文章](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md)，其中有很多 async 核心中常见场景的例子
*   谢尔盖·特普利亚科夫的[“任务完成的危险来源< T >级](https://blogs.msdn.microsoft.com/seteplia/2018/10/01/the-danger-of-taskcompletionsourcet-class/)

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode010)。

请发送任何反馈，以便我可以改进和调整下一集。

谢谢你的来访，西阿兹！