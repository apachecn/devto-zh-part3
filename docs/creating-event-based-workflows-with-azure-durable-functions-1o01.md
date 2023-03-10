# 使用 Azure 持久功能创建基于事件的工作流

> 原文：<https://dev.to/azure/creating-event-based-workflows-with-azure-durable-functions-1o01>

[持久功能](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview?WT.mc_id=devto-blog-aapowell)是 [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/?WT.mc_id=devto-blog-aapowell) 无服务器堆栈的扩展，它引入了跨功能的状态管理和编排，而无需自己编写管道代码。

今天，我想看一下创建客户端驱动的事件工作流系统的场景。我们的客户将发起一个请求，这将启动一个工作流程。我们将使用 [HTTP 绑定](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-http-webhook?WT.mc_id=devto-blog-aapowell)作为我们的函数，并传入 [`OrchestrationClient`](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-bindings?WT.mc_id=devto-blog-aapowell#orchestration-client) :

```
[<FunctionName("StartWorkflow")>]
let startWorkflow
    ([<HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "start/{input}")>] req : HttpRequest)
    ([<OrchestrationClient>] starter : DurableOrchestrationClient)
    input
    (logger : ILogger) =
    task {
        logger.LogInformation(sprintf "Starting a new workflow for %s" input)
        let! _ = starter.StartNewAsync(eventName, input)
        return OkResult()
    } 
```

route 有一个传入的参数`input`，我们将在 API 调用中使用它作为我们的标识符(如果您愿意，也可以使用启动工作流返回的`instanceId`),否则这里没有什么过于复杂的，我们使用`DurableOrchestrationClient`通过`StartNewAsync(<name of instance>, <data for instance>)`来启动工作流。

现在我们需要创建我们的工作流函数。这就要用到 [`OrchestrationTrigger`](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-bindings?WT.mc_id=devto-blog-aapowell#orchestration-triggers) :

```
module Workflow

open Microsoft.Azure.WebJobs
open Microsoft.Extensions.Logging
open FSharp.Control.Tasks.V2.ContextInsensitive

let eventName = "Workflow"

[<FunctionName("Workflow")>]
let run
    ([<OrchestrationTrigger>] context : DurableOrchestrationContext)
    (logger : ILogger) =
    task {
        let input = context.GetInput<string>()
        sprintf "Starting workflow for %s" input |> logger.LogInformation
        do! context.WaitForExternalEvent(eventName)
        sprintf "Workflow for %s is stopping" input |> logger.LogInformation
    } 
```

该模块定义了我们在第一个函数中使用的事件名称`Workflow`，然后使用`context.GetInput<string>()`解包传入的数据，然后告诉函数休眠，直到使用`context.WaitForExternalEvent(eventName)`触发一个事件。

现在，这个`WaitForExternalEvent`是一个重要的函数，它所做的是告诉我们的函数，在它的控制之外的一些东西将控制它的执行，它应该进入睡眠，直到那个事件被触发，并且那个事件也必须在特定的实例上被触发。这个函数现在处于“休眠”状态，不消耗资源(或金钱),只要你需要，它就可以休眠。它还返回一个`Task`，这意味着它是异步的，所以你可以把它和一个定时器结合起来，让它只休眠一段时间。

我们要创建的下一个函数是一个 HTTP 端点，用于检查工作流的状态。一旦工作流完成，您可以从客户端以轮询方式调用该函数来执行操作。

```
[<FunctionName("CheckWorkflow")>]
let checkWorkflow
    ([<HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "check/{input}")>] req : HttpRequest)
    ([<OrchestrationClient>] starter : DurableOrchestrationClient)
    input
    (logger : ILogger) =
    task {
        logger.LogInformation(sprintf "Checking workflow for %s" input)

        let offset = TimeSpan.FromMinutes 20.
        let time = DateTime.UtcNow

        let! instances = starter.GetStatusAsync
                                 (time.Subtract offset,
                                  Nullable(time.Add offset),
                                  System.Collections.Generic.List<OrchestrationRuntimeStatus>(),
                                  CancellationToken.None)

        return OkObjectResult(instances |> Seq.find (fun i -> i.Name = eventName && i.Input.ToObject<string>() = input))
    } 
```

我们再次使用了`HttpTrigger`,并提供了一个`OrchestrationClient`,但这一次我们使用客户端通过`GetStatusAsync`方法搜索所有正在运行的工作流实例(我还提供了搜索的日期范围，以便它不会找到我的存储帐户中的所有内容)。一旦我们有了所有的实例，我就会寻找任何与传入的`input`匹配的实例，但是如果你使用的是`instanceId`，你可以过滤掉。然后，该函数返回包含找到的实例的对象。这将允许客户端检查它是否完成，并决定在客户端做什么。

我们的工作流可以启动，我们能够轮询它并检查它的状态，现在是时候实现一种方法来调用事件并完成工作流了:

```
[<FunctionName("StopWorkflow")>]
let stopWorkflow
    ([<HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "stop/{input}")>] req : HttpRequest)
    ([<OrchestrationClient>] starter : DurableOrchestrationClient)
    input
    (logger : ILogger) =
    task {
        logger.LogInformation(sprintf "Stopping workflow for %s" input)

        let offset = TimeSpan.FromMinutes 20.
        let time = DateTime.UtcNow

        let! instances = starter.GetStatusAsync
                                 (time.Subtract offset,
                                  Nullable(time.Add offset),
                                  System.Collections.Generic.List<OrchestrationRuntimeStatus>(),
                                  CancellationToken.None)

        return! match instances |> Seq.tryFind (fun i -> i.Name = eventName && i.Input.ToObject<string>() = input) with
                | Some instance ->
                    task {
                        logger.LogInformation(sprintf "Found a matching instance with id %s" instance.InstanceId)
                        do! starter.RaiseEventAsync(instance.InstanceId, eventName, input)
                        return OkObjectResult(instance) :> IActionResult
                    }

                | None ->
                    task {
                        sprintf "Didn't find a matching instance for %s" input |> logger.LogInformation

                        return NotFoundResult()
                    }
    } 
```

我们在这里执行一个类似的搜索逻辑来查找我们的工作流实例，如果找到了，我们使用`DurableOrchestrationClient` `RaiseEventAsync`方法，并向它提供工作流实例的 ID 和我们正在等待的事件名称，以及我们希望为事件传递的任何输入。

此事件将被异步引发，工作流函数将从等待事件的点恢复，然后运行直至完成。这里重要的一点是它是**异步的**，这意味着如果您随后立即轮询，那么状态*可能*没有完成，因为工作流功能可能没有触发/运行完成。

## 结论

这里我们有一个在持久函数中使用事件来控制后台作业的例子。诚然，我们已经使用 HTTP 端点来触发每一步，但是没有理由不能编写“stop”函数来等待一个项被写入 Blob 存储或任何其他函数触发器。

同样值得记住的是，这个处理过程是异步处理的，所以您可以等待多个事件，并使用一个`Task.WhenAny`来只等待一个事件被触发，或者结合一个超时来只等待一个事件一段预定义的时间。

如果你想自己尝试一下，我在 GitHub 上创建了一个示例，用 [F#](https://github.com/aaronpowell/durable-functions-workflow-demo/tree/master/fsharp) 、 [C#](https://github.com/aaronpowell/durable-functions-workflow-demo/tree/master/csharp) 和 [JavaScript](https://github.com/aaronpowell/durable-functions-workflow-demo/tree/master/javascript) 实现。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [ aaronpowell ](https://github.com/aaronpowell) / [耐用-功能-工作流程-演示](https://github.com/aaronpowell/durable-functions-workflow-demo)

### 一个简单的演示，展示了持久函数如何用于工作流

<article class="markdown-body entry-content p-5" itemprop="text">

这个库包含一个简单的[持久函数](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview?WT.mc_id=durablefunctionsworkflowdemo-github-aapowell)工作流演示，用 C#、F#和 JavaScript 编写。

# 入门指南

1.  克隆或下载代码并打开你想使用的语言的文件夹
2.  打开 [VS 代码](https://code.visualstudio.com/?WT.mc_id=durablefunctionsworkflowdemo-github-aapowell)中的文件夹

*   你需要安装 [VS Code Azure 函数扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions&WT.mc_id=durablefunctionsworkflowdemo-github-aapowell)

3.  编辑`local.settings.json`文件以正确设置`AzureWebJobsStorage`连接字符串

*   如果你在 Windows 上，你可以使用[存储模拟器](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator?WT.mc_id=durablefunctionsworkflowdemo-github-aapowell)
*   如果你使用的是 Mac 或 Linux，你要么需要一个 Azure 账户来创建一个存储账户([在这里注册免费试用](https://azure.microsoft.com/en-us/free/?wt.mc_id=durablefunctionsworkflowdemo-github-aapowell))要么需要一个本地模拟器的[Azure](https://github.com/azure/azurite)

4.  启动调试器

# 如何运行演示

当演示功能启动后，它们将由 3 个 API 端点组成:

*   `/api/start/{input}`
    *   为您提供的输入启动新的工作流，例如:`/api/start/aaron`
*   `/api/check/{input}`
    *   检查您启动的输入的工作流程状态，例如:`/api/check/aaron`
    *   返回一个…

</article>

[View on GitHub](https://github.com/aaronpowell/durable-functions-workflow-demo)