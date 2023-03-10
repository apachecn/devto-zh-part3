# 阿帕玛休息服务公司

> 原文：<https://dev.to/tpetrov9/apama-rest-services-4e6>

# 用 HTTP 连接传输实现请求/响应协议

由 HTTP 服务器连接传输提供的阿帕玛 REST 服务器只能通过 REST 提供事件提交。在 10.3.1 版本中(对软件 AG 10.3 和 10.4 客户都可用)，它现在还可以提供请求/响应 API。本文探索了一些新功能。

## 其余用例同阿帕玛

阿帕玛是一个事件处理引擎，它需要与其他系统集成来使用和发出事件。传统上，这是通过我们自己的客户端库和协议，或者通过消息总线，如 Java 消息服务(JMS)或通用消息。在过去的几个版本中，我们一直在增加通过标准的基于 web 的协议，比如 REST，直接与阿帕玛集成的方法。

**从阿帕玛查询/调用 REST 服务**

一些阿帕玛应用程序需要来自外部服务的数据来处理事件。其他应用程序的结果是对外部服务的操作。在阿帕玛 10.0 中，我们引入了 HTTP 客户端传输，它可以用来从您的阿帕玛应用程序发出这样的请求，等待响应并处理它。

**在休息时间向阿帕玛交付赛事**

或者，您可能有一个需要向阿帕玛传递事件流的外部服务。在 10.1 中，我们引入了 HTTP 服务器传输，它提供了 HTTP REST 端点，您可以从其他服务调用它。这个版本的 HTTP 服务器是单向的。您可以向阿帕玛发送一个事件，但是阿帕玛无法从应用程序中得到任何响应。

**阿帕玛提供休息服务**

您可能希望提供一个完整的 REST 服务，由您的阿帕玛应用程序处理查询，并决定如何响应，而不仅仅是提供一个只提交的 API。然后，这个响应被发送回客户端。这个用例是我们在 10.3.1 中提供的，也将是本文其余部分的主题。

## 给项目添加 HTTP 服务器支持

要将 HTTP 服务器添加到您的项目中，您需要添加“HTTP server”包。这可以在 Software AG Designer 中通过“连接和适配器”节点来完成，也可以使用 10.3.1 中引入的新 apama_project 工具来完成。

这将在*config/connectivity/http server*下向您的项目添加一个额外的 YAML 配置文件和相关属性。您需要修改属性文件来指定运行服务器的端口，并更改任何其他设置，如 TLS 支持。关于配置选项的更多细节可以在阿帕玛文档中找到。

默认配置是基于 JSON 的只提交 API，它假设 JSON 有效负载的特定格式。您必须更改这个配置文件来启用请求/响应，并提供 REST API 主体到阿帕玛事件之间的映射(见下文)。

## 启用来自 EPL 的其余响应

默认情况下，HTTP server bundle 将有一个仅提交 API，不允许您从 EPL 发送响应。为了成为完整的 REST 服务器，您需要更改配置文件。我们将创建一个 Create Request Update Delete (CRUD)存储，您可以在其中请求在阿帕玛存储数据，然后在以后检索它。完整的示例可以在您的阿帕玛安装的 samples/connectivity _ plugin/application/http server-responses 目录中找到。

首先，您必须通过更改传输上的一个标志来停止传输自动发送响应:

```
HTTPServerTransport: automaticResponses: false allowedMethods: [PUT] 
```

其次，传输提供了一些需要映射到事件中的信息，以便您可以在 EPL 使用它。这是通过在配置文件
中的链定义中改变 mapperCodec 的规则来实现的

```
mapperCodec: “\*”: towardsHost: mapFrom: - payload.requestId: metadata.requestId defaultValue: - payload.channel: “@{httpServer.responseChannel}” 
```

这个片段适用于所有请求消息，我们在有效负载中设置了两个字段。请求 ID 取自每个消息，响应通道是在创建链时替换的常量。除了为所有类型的请求添加任何其他规则之外，您还需要添加这些映射规则。请求 ID 用于匹配对适当请求的响应。通道告诉 EPL 如何将响应发送回传输。

我们还需要对从 EPL 传回来的信息做一些改变。具体来说，我们需要提供来自相应请求的响应 ID:我们还需要对从 EPL 返回的消息进行一些更改。具体来说，我们需要提供来自相应请求的响应 ID:

```
mapperCodec: “\*”: towardsTransport: mapFrom: - metadata.requestId: payload.requestId 
```

## 处理 REST 请求并发送响应

最后，我们准备编写一些 EPL，它将处理请求并发送适当的响应。让我们以 CRUD 示例的“创建”部分为例。您可以在完整示例中看到所有其他版本。

```
**on**  **all** CreateObject() **as** co { **try** { **string** path := generateNewKey(); datastore.add(path, co.data); **send** Success(co.requestId, path) **to** co.channel; } **catch** (Exception e) { **send** Error(co.requestId, 500, e.getMessage() **to** co.channel; } } 
```

这表明我们从 *CreateObject* 请求中获取请求 id，并在*成功*和*错误*响应中将其传回。这些响应被发送到也是请求对象的一部分的通道。

还需要将成功和错误消息发送回客户端的映射规则。你可以在完整的样本中看到细节。

## 总结

在本文中，我们学习了如何在您的阿帕玛应用程序中处理 REST 请求，并生成发送给客户端的定制响应。完整的细节可以在您的安装中的 *httpserver-responses* 示例和“HTTP Server Transport Connectivity 插件”下的阿帕玛文档中找到要获得更多帮助，请查看我们在 www.apamacommunity.com[上的定期博客帖子，或者询问关于带有 **apama** 标签的堆栈溢出的问题。](http://www.apamacommunity.com)