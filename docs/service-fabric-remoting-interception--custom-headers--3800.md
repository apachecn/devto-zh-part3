# 服务结构远程处理:拦截和自定义头

> 原文：<https://dev.to/expecho/service-fabric-remoting-interception--custom-headers--3800>

[Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/) 是一个分布式系统平台，用于打包、部署和管理大规模的无状态和有状态分布式应用程序和容器。Service Fabric 运行在 Windows 和 Linux 上、任何云上、任何数据中心上、跨地理区域或您的笔记本电脑上。

# 简介

在这篇文章中，我们将学习两件事，当使用[服务远程](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-communication-remoting)进行服务和参与者之间的通信时，这两件事可以使支持高级场景变得更加容易。我们将利用我创建的[简单库](https://www.nuget.org/packages/ServiceFabric.Remoting.CustomHeaders/)来做到这一点。它使您能够:

*   截获服务结构远程处理消息，以便您可以在即将进行调用时以及在调用完成后采取措施。
*   向远程处理消息添加自定义标头。例如，这可以用于添加跟踪标识符，以便进行呼叫跟踪和日志记录。

# 消息拦截

消息可以在发送端和接收端被截获

### 客户端消息拦截

在接收端，当创建服务监听器:
时，可以使用`BeforeHandleRequestResponseAsync`和`AfterHandleRequestResponseAsync`扩展点来拦截消息

```
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    yield return new ServiceInstanceListener(context =>
        new FabricTransportServiceRemotingListener(context,
            new ExtendedServiceRemotingMessageDispatcher(context, this)
            {
                // Optional, log the call before being handled
                BeforeHandleRequestResponseAsync = reqInf =>
                {
                    var sw = new Stopwatch();
                    sw.Start();
                    ServiceEventSource.Log.ServiceMessage(Context, 
                            $"BeforeHandleRequest {reqInf.Service}  {reqInf.Method}");
                    return Task.FromResult<object>(sw);
                },
                // Optional, log the call after being handled
                AfterHandleRequestResponseAsync = respInf =>
                {
                    var sw = (Stopwatch) respInf.State;
                    ServiceEventSource.Log.ServiceMessage(Context, 
                            $"AfterHandleRequest {respInf.Method} took {sw.ElapsedMilliseconds}ms");
                    return Task.CompletedTask;
                }
            }));
} 
```

Enter fullscreen mode Exit fullscreen mode

### 服务器端消息拦截

在发送端，当在`ServiceProxyFactory` :
的构造函数上创建`ExtendedServiceRemotingClientFactory`时，可以使用`BeforeSendRequestResponseAsync`和`AfterSendRequestResponseAsync`扩展点来拦截消息

```
var proxyFactory = new ServiceProxyFactory(handler => // or ActorProxyFactory in case of actors
    new ExtendedServiceRemotingClientFactory(
        new FabricTransportServiceRemotingClientFactory(remotingCallbackMessageHandler: handler), 
            customHeadersProvider)
    {
        // Optional, log the call before being handled
        BeforeSendRequestResponseAsync = reqInf =>
        {
            var sw = new Stopwatch();
            sw.Start();
            Console.WriteLine($"BeforeSendRequest {reqInf.Method}");
            return Task.FromResult<object>(sw);
        },
        // Optional, log the call after being handled
        AfterSendRequestResponseAsync = respInf =>
        {
            var sw = (Stopwatch)respInf.State;
            var duration = sw.ElapsedMilliseconds;
            Console.WriteLine($"AfterSendRequest {respInf.Method} took {duration}ms");
            return Task.CompletedTask;
        }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

# 自定义表头

自定义标头可用于在发送方和接收方之间传递数据，如跟踪信息或安全上下文数据。使用`BeforeHandleRequestResponseAsync`和`AfterHandleRequestResponseAsync`动作，可以应用额外的日志记录来监控远程调用之间的流程。

### 准备可靠的服务

修改服务并创建一个可以处理请求的监听器:

```
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    yield return new ServiceInstanceListener(context =>
        new FabricTransportServiceRemotingListener(context,
            new ExtendedServiceRemotingMessageDispatcher(context, this)));
} 
```

Enter fullscreen mode Exit fullscreen mode

### 添加自定义表头(调用者)

```
var customHeaders = new CustomHeaders
{
    {"Header1", DateTime.Now},
    {"Header2", Guid.NewGuid()}
};

var serviceUri = new Uri("fabric:/ServiceFabric.Remoting.CustomHeaders.DemoApplication/DemoService");
var proxyFactory = new ServiceProxyFactory(handler => 
                    new ExtendedServiceRemotingClientFactory(
                        new FabricTransportServiceRemotingClientFactory(remotingCallbackMessageHandler: handler), 
                            customHeaders));
var proxy = proxyFactory.CreateServiceProxy<IDemoService>(serviceUri); 
```

Enter fullscreen mode Exit fullscreen mode

Create 方法有一个接受 Func 的重载。这在重用创建的代理工厂或代理的情况下很有用。由于创建代理工厂的成本很高，如果您需要动态头值，这是首选方式。使用代理发出的每个请求都会调用 func。

### 读取自定义表头(被叫方)

接收服务或参与者可以使用`RemotingContext`类:
提取定制头中的值

```
 public async Task<string> SayHello()
{
    var remotingContext =
        string.Join(", ", RemotingContext.Keys.Select(k => $"{k}: {RemotingContext.GetData(k)}"));

    ServiceEventSource.Log.ServiceMessage(Context, 
            $"SayHelloToActor got context: {remotingContext}");
    return Task.FromResult($"Got the following message headers: {remotingContext}")
} 
```

Enter fullscreen mode Exit fullscreen mode

# 接下来是什么？

更多文档和演示服务结构应用程序可在以下存储库中找到:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[Expecho](https://github.com/Expecho)/[service fabric-Remoting-custom headers](https://github.com/Expecho/ServiceFabric-Remoting-CustomHeaders)

### 这个包允许在运行时将自定义消息头注入到远程消息中(参与者和可靠服务，仅限 V2 远程)。客户端可以读取标题。它还使用 beforehandlerequestresponsesync 和 AfterHandleRequestResponseAsync 对远程事件进行操作来提供消息拦截。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 服务结构。Remoting.CustomHeaders

这个包允许在运行时将自定义头注入到远程消息中(参与者和可靠服务，仅限 V2 远程)。客户端可以读取这些头。它还使用 beforehandlerequestresponsesync 和 AfterHandleRequestResponseAsync 提供消息拦截，以处理远程事件。

常用类别:

*   [CustomHeaders](https://github.com/Expecho/ServiceFabric-Remoting-CustomHeaders/blob/master/src/ServiceFabric.Remoting.CustomHeaders/CustomHeaders.cs)
*   [删除上下文](https://github.com/Expecho/ServiceFabric-Remoting-CustomHeaders/blob/master/src/ServiceFabric.Remoting.CustomHeaders/RemotingContext.cs)

## 努格特

*Nuget 包:* [ServiceFabric。Remoting.CustomHeaders](https://www.nuget.org/packages/ServiceFabric.Remoting.CustomHeaders/)

## 例子

这个存储库包括一个用于演示的服务结构应用程序。一个[控制台应用程序](https://github.com/Expecho/ServiceFabric-Remoting-CustomHeaders/blob/master/src/Demo/Program.cs)用于访问该应用程序并显示软件包的使用情况。

## 使用场景

自定义标头可用于在发送方和接收方之间传递数据，如跟踪信息或安全上下文数据。使用 beforehandlerequestresponsesync 和 AfterHandleRequestResponseAsync 操作，可以应用附加日志记录来监视远程处理调用之间的流。

## 如何使用

### 准备可靠的服务

修改服务并创建一个可以处理请求的侦听器

```
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    yield return new ServiceInstanceListener(context
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/Expecho/ServiceFabric-Remoting-CustomHeaders)