# 发现持久功能 API——检索编排状态(DurableOrchestrationClient 第 2 部分)

> 原文：<https://dev.to/xpirit/discovering-the-durable-functions-api-retrieving-the-orchestration-status-durableorchestrationclient-part-2-40kc>

[![Methods in DurableOrchestrationClientBase](img/a82c4630dd6668387f3db360629afa9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7q_ihkEH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.marcduiker.nl/assets/2019/02/10/DurableOrchestrationClientBase2_900.png)

## durableorchestration client(Base)类-检索状态

这篇文章是探索持久函数 API 系列博客的第二部分。

在下面链接的视频中，我正在查看来自 [`DurableOrchestrationClient` ( `Base` )](https://github.com/Azure/azure-functions-durable-extension/blob/master/src/WebJobs.Extensions.DurableTask/DurableOrchestrationClientBase.cs) 类的功能，这些功能可用于检索编排实例的状态。

这些是检索一个编排的**状态并返回一个 [`DurableOrchestrationStatus`](https://github.com/Azure/azure-functions-durable-extension/blob/master/src/WebJobs.Extensions.DurableTask/DurableOrchestrationStatus.cs) 对象的方法:**

*   `GetStatusAsync(string instanceId)`
*   `GetStatusAsync(string instanceId, bool showHistory);`
*   `GetStatusAsync(string instanceId, bool showHistory, bool showHistoryOutput, bool showInput = true);`

这些是检索多个编排的状态的方法，它们返回一个`IList<DurableOrchestrationStatus>`:

*   `GetStatusAsync(CancellationToken cancellationToken = default(CancellationToken));`
*   `GetStatusAsync(DateTime createdTimeFrom, DateTime? createdTimeTo, IEnumerable<OrchestrationRuntimeStatus> runtimeStatus, CancellationToken cancellationToken = default(CancellationToken));`

这是视频，如果你喜欢，请竖起大拇指，如果你还没有订阅我的频道，请订阅:

[https://www.youtube.com/embed/d5fsidj_EDs](https://www.youtube.com/embed/d5fsidj_EDs)

### 资源

*   GitHub 回购:[github.com/marcduiker/demos-azure-durable-functions](https://github.com/marcduiker/demos-azure-durable-functions)。
*   微软文档:[持久函数 HTTP API](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-http-api) 。

### 链接到本系列的其他帖子

*   [开始编排(持续编排客户端第 1 部分)](https://dev.to/marcduiker/discovering-the-durable-functions-api-starting-orchestrations-durableorchestrationclient-part-1-52ic)
*   检索业务流程状态(DurableOrchestrationClient 第 2 部分)
*   [清除&终止编排(DurableOrchestrationClient Part 3)](https://dev.to/marcduiker/discovering-the-durable-functions-api-purge-terminate-orchestrations-durableorchestrationclient-part-3-59g6)