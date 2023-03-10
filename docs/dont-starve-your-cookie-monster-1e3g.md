# 不要让你的饼干怪兽挨饿

> 原文：<https://dev.to/rafalpienkowski/dont-starve-your-cookie-monster-1e3g>

首先，我想就封面图片说几句话。我敢打赌，你们几乎都知道一个叫做[饼干怪兽](https://en.wikipedia.org/wiki/Cookie_Monster)的布偶[。正如 Cookie Monster 不喜欢没有 Cookie 一样，我们的线程也不喜欢被阻塞。](https://en.wikipedia.org/wiki/The_Muppets)

使用线程并不简单。这对每个尝试过的人来说都是显而易见的。大量问题中的一个是给定资源的排他锁，它为我们提供线程同步。

[![synchronize](img/4d568f0fa900e53fda8755da08c86d57.png)](https://i.giphy.com/media/69FpUKMjZ1er2lc5AZ/giphy.gif)

同步对资源的访问的最简单的方法是在一个保护变量上使用 [*lock*](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/lock-statement) 语句。就像下面的例子:

```
var guard = new object();
lock(guard)
{
    // access the critical path
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你认为无害的锁不会有危险，那你就错了。这种看似简单易行的线程同步方式可能会以**的死锁结束。**

 **[![deadlock](img/dc622641e304cf7d7f14c35035ee6abc.png)](https://i.giphy.com/media/3o7TKqnMdPFGtzD6zm/giphy.gif)

你可能会问这怎么可能。为了理解这个问题，我们需要更深入地了解编译器是如何处理上面给出的代码的。

```
var guard = new object();
try
{
    Monitor.Enter(guard);
    // access the critical path
}
finally
{
    Monitor.Exit(guard);
} 
```

Enter fullscreen mode Exit fullscreen mode

lock 语句已被替换为 [*监视器。*输入()](https://docs.microsoft.com/en-us/dotnet/api/system.threading.monitor.enter?view=netframework-4.7.2)方法。这种方法是如何工作的？一个线程请求锁定一个资源，并等待直到获得访问权。这是如何造成僵局的。让我们来看看这个两个守护变量上的两个线程同步的例子。

*   螺纹 A 锁定防护装置 X
*   螺纹 B 锁定防护装置 Y
*   线程 A 想要锁定防护 Y，但是防护 Y 被线程 B 锁定了
*   线程 B 想要锁定防护 X，但是线程 A 锁定了防护 X

我们的线程 A 和 B 一直等啊等，一直等到世界末日，就像饼干怪兽在悲伤地等待饼干一样。
[![waiting](img/271949516b2a0cf4e7e2732e06f9e471.png)T3】](https://i.giphy.com/media/o5oLImoQgGsKY/giphy.gif)

我们可以通过使用[* Monitor *](https://docs.microsoft.com/en-us/dotnet/api/system.threading.monitor?view=netframework-4.7.2)类中的另一个方法来防止这种情况。该方法命名为 [*试输入*](https://docs.microsoft.com/en-us/dotnet/api/system.threading.monitor.tryenter?view=netframework-4.7.2) 。为什么它如此特别？它可以将指定的时间量作为参数，指示尝试将重复多长时间。在给定的时间之后，线程将继续前进——不再有死锁。

```
var guard = new object();
try
{
    Monitor.TryEnter(guard, TimeSpan.FromSeconds(15));
    // access the critical path
}
finally
{
    Monitor.Exit(guard);
} 
```

Enter fullscreen mode Exit fullscreen mode

一个好的做法是通过调用 [*监视器来确保锁被释放。退出*的](https://docs.microsoft.com/en-us/dotnet/api/system.threading.monitor.exit?view=netframework-4.7.2)法在给予守护。

我知道 lock 语句比使用显式 Monitor 更短。尝试输入方法，但是在生产部署期间的星期天晚上，您可能要花费精力编写一些额外的代码行。

总结一下。

> 避免使用 lock 语句，开始使用 ***监视器。尝试输入*T3。你的线程会很感激。**

[![Thanks](img/1290ed3ba31c847fba0031f47c30d357.png)](https://i.giphy.com/media/lD76yTC5zxZPG/giphy.gif)

所有的 gif 都来自 giphy.com。**