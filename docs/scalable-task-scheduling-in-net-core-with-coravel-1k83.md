# 中的可扩展任务调度。带 Coravel 的网芯

> 原文：<https://dev.to/jamesmh/scalable-task-scheduling-in-net-core-with-coravel-1k83>

本帖是 [2018 C#降临节日历](https://crosscuttingconcerns.com/The-Second-Annual-C-Advent)的一部分。

它最初发表在我的博客上。

本着节日的精神，我们将讨论圣诞老人最近是如何使用。NET Core 来构建他的内部圣诞礼物处理系统。

# 圣诞老人的需求

Santa 是一名中级开发人员，但是他一直在学习。网芯。

他最近需要构建一个在安全性和易开发性方面健壮的系统。

他决定。考虑到这些标准，NET Core 是最佳选择。

圣诞老人不想重新发明轮子——但他需要一种可靠而简单的方法来安排后台任务、排队工作，以便他的 web 应用程序能够响应(主要针对精灵)等。

# Coravel

有一天，他遇到了 Coravel，这是一个接近零配置的开源库。NET 核心开发者。

Coravel 专注于帮助开发人员快速启动并运行他们的 web 应用程序——而不牺牲代码质量。它使通常非常高级的功能变得非常易于使用和访问，而无需安装任何额外的第三方基础架构:

*   任务调度
*   排队等候
*   贮藏
*   事件广播
*   还有更多！

因为它是专门作为一套工具编写的。NET Core，它利用了本机特性——比如完全支持[内置的依赖注入服务](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.1)和[托管的后台服务](https://docs.microsoft.com/en-us/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)。

例如，您可以将依赖项/服务注入调度任务、排队任务、事件监听器等。一点也不大惊小怪！

圣诞老人真的很喜欢使用 Coravel，尤其是节省了时间，因为不必配置和安装调度、排队、事件广播等其他依赖项。单独地。

他特别喜欢科拉韦尔与。NET Core 的 DI 系统如此无缝。

# 退税

但是——现在圣诞老人不得不安排一些真正长时间运行的任务。可能需要数小时才能完成的任务。这些都是重要的任务。

在他的 ASP 中做这件事。NET 核心应用程序不是一个选项，因为在 web 应用程序中执行这些类型的长时间运行的任务会导致问题(你可能知道)。

# 解

圣诞老人决定看看 Coravel 的 GitHub repo(T1)，以防这个问题之前已经解决过。

原来有一个例子可以解决这个问题！

我问圣诞老人，我能否分享一下他是如何决定实现这一点的。他同意了，但是我只被允许展示他的系统的一个很小的样本。

# 调度任务从。NET 核心控制台应用程序

Coravel 的好处之一就是专门为。NET Core 就是**配置**这么简单。

与以下之一结合使用。NET Core 最酷的特性，`HostBuilder`，你可以用几行代码做一些真正强大的事情。

顺便说一下，让我们通过添加您需要的特定部分来构建一个. NET 核心应用程序。然后，您可以“托管”您需要的任何东西(迷你 API 端点、运行后台任务的控制台应用程序或多个[托管服务](https://docs.microsoft.com/en-us/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice))，而无需典型 web 项目所需的全部依赖关系。

因为 Coravel 不是一个. NET Framework 库的端口，而是专门为。NET Core，Coravel 的特性——比如调度和排队——被实现为一个[托管服务](https://docs.microsoft.com/en-us/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)。**这意味着 [Coravel](https://github.com/jamesmh/coravel) 在非 web 场景下 100%兼容。**

使用上面提到的例子，让我们看一个使用`HostBuilder`和 Coravel 的调度:
的非常基本的实现

```
class Program
{
    static void Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, configApp) =>
            {
                configApp.AddCommandLine(args);
            })
            .ConfigureServices((hostContext, services) =>
            {
                // Add Coravel's Scheduling...
                services.AddScheduler();
            })
            .Build();

        // Configure the scheduled tasks....
        host.Services.UseScheduler(scheduler =>
            scheduler
                .Schedule(() => Console.WriteLine("This was scheduled every minute."))
                .EveryMinute()
        );

        // Run it!
        host.Run();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将是一个控制台应用程序，挂钩到 Coravel 的调度程序。

每一分钟都会有事情发生。

# 配角。使用 Invocables 的 NET Core 依赖注入

好的，这是一个简单的例子。圣诞老人需要更易维护的东西，他**真的**需要注入他的实体框架核心数据库上下文、`HttpClientFactory`等等。他的预定任务。

使用 [Coravel](https://github.com/jamesmh/coravel) 你可以使用[可调用的](https://github.com/jamesmh/coravel/blob/master/Docs/Invocables.md)类来解决这个问题。

> Invocables 是无处不在的类，可以被调度、排队等。全力支持。净核心依赖注入。
> 
> 它们代表应用程序中的一些“工作”:
> 
> *   发送自动电子邮件
> *   清理您的数据库
> *   处理来自外部队列的消息
> *   在外部 API 和您的系统之间同步数据

# 圣诞老人的急电

圣诞老人有一个 API，他用它来获取谁乖谁淘气(这是他的遗产系统中的一个秘密端点)。然后，他需要进行一些 CPU 密集型处理来验证数据，然后将结果存储在他的数据库中。

他需要使用自己创建的可调用类每小时完成一次。

```
scheduler
    .Schedule<PutNaughtyChildrenFromAPIIntoDb>()
    .Hourly();

scheduler
    .Schedule<PutNiceChildrenFromAPIIntoDb>()
    .Hourly(); 
```

Enter fullscreen mode Exit fullscreen mode

# 可扩展性问题

[Coravel](https://github.com/jamesmh/coravel) 内部使用`Task.WhenAll`来确保调度任务中的异步调用得到有效处理。

但是，CPU 密集型任务会“霸占”当前正在处理到期任务的线程。这将迫使其他到期任务等待，直到 CPU 密集型处理完成。

这种设计确保了在 web 应用场景中 [Coravel](https://github.com/jamesmh/coravel) 不会独占多线程，否则这些线程可能(也应该)被用来响应 HTTP 请求。

但是圣诞老人特别使用了一个控制台应用程序，所以他不需要担心这个问题！

他该怎么办？

# 调度工人

Coravel 用[调度工人](https://github.com/jamesmh/coravel/blob/master/Docs/Scheduler.md#schedule-workers)解决了这个问题。

通过使用调度工人，Santa 可以将这些任务中的每一个放到它们自己的专用管道/线程上:

```
scheduler.OnWorker("NaughtyWorker");
scheduler
    .Schedule<PutNaughtyChildrenFromAPIIntoDb>()
    .Hourly();

scheduler.OnWorker("NiceWorker");
scheduler
    .Schedule<PutNiceChildrenFromAPIIntoDb>()
    .Hourly(); 
```

Enter fullscreen mode Exit fullscreen mode

这将并行执行每个任务，因此 Santa 可以更有效地获取和处理这两个任务。任一任务可能执行的任何密集 CPU 工作都不会导致另一个任务等待/阻塞。

# 调度任务组

在某些情况下，您可能希望将多个任务放在一个工作器上，并可能让一个工作器专门处理一个已知需要很长时间或 CPU 密集型的任务。

例如:

```
scheduler.OnWorker("EmailTasks");
scheduler
    .Schedule<SendNightlyReportsEmailJob>().Daily();
scheduler
    .Schedule<SendPendingNotifications>().EveryMinute();

scheduler.OnWorker("CPUIntensiveTasks");
scheduler
    .Schedule<RebuildStaticCachedData>().Hourly(); 
```

Enter fullscreen mode Exit fullscreen mode

前两个任务不需要那么长时间就能完成，而且就 CPU 使用而言也不是优先任务(它们主要受 I/O 限制)。

然而，第二个需要能够在不被阻塞或阻塞其他任务的情况下工作。

这种设置将确保`RebuildStaticCachedData`总是用它自己的专用管道隔离执行。

# 结论

我希望你喜欢这篇文章，并希望在评论中听到你的意见！也许有更好的方法？也许你只是喜欢其他方式？

* * *

## 保持联系

别忘了在 [twitter](https://twitter.com/jamesmh_dev) 或 [LinkedIn](https://www.linkedin.com/in/jamesmhickey/) 上联系我！

## 你可能也会喜欢(来自我的博客)

*   到目前为止我所学到的。净芯工装)
*   流畅的 API 让开发者喜欢使用你的。网络库
*   [什么使。网芯这么特别？](https://www.blog.jamesmichaelhickey.com/What-Makes-NET-Core-So-Special-Why-You-Should-Use-NET-Core/)

# 导航您的软件开发生涯

一封电子邮件简讯，我将在其中回答订阅者的问题，并就以下主题提供建议:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？

听起来有趣吗？加入社区吧！