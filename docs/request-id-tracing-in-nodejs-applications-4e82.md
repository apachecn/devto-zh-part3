# Node.js 应用程序中的请求 Id 跟踪

> 原文：<https://dev.to/puzpuzpuz/request-id-tracing-in-nodejs-applications-4e82>

<figure>[![](img/67c1316ccb1f99370580a83165d2b87d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pN6PMiKP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AsduEjXj8BCMSMYU1) 

<figcaption>照片由[菲利普·布朗](https://unsplash.com/@nebirdsplus?utm_source=medium&utm_medium=referral)拍于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

如果您曾经在 Node.js 中编写过后端应用程序，您就会知道通过日志条目跟踪相同的 HTTP 请求是一个问题。通常你的日志看起来像这样:

```
[07/Nov/2018:15:48:11 +0000] User sign-up: starting request validation

[07/Nov/2018:15:48:11 +0000] User sign-up: starting request validation

[07/Nov/2018:15:48:12 +0000] User sign-up: request validation success

[07/Nov/2018:15:48:13 +0000] User sign-up: request validation failed. Reason:
... 
```

Enter fullscreen mode Exit fullscreen mode

这里，日志条目混在一起，没有办法确定它们中的哪一个属于同一个请求。虽然你可能更喜欢看到这样的东西:

```
[07/Nov/2018:15:48:11 +0000] [request-id:550e8400-e29b-41d4-a716-446655440000] User sign-up: starting request validation

[07/Nov/2018:15:48:11 +0000] [request-id:340b4357-c11d-31d4-b439-329584783999] User sign-up: starting request validation

[07/Nov/2018:15:48:12 +0000] [request-id:550e8400-e29b-41d4-a716-446655440000] User sign-up: request validation success

[07/Nov/2018:15:48:13 +0000] [request-id:340b4357-c11d-31d4-b439-329584783999] User sign-up: request validation failed. Reason:
... 
```

Enter fullscreen mode Exit fullscreen mode

注意这里包含请求标识符的[request-id:*]部分。这些标识符将允许您过滤属于同一请求的日志条目。此外，如果您的应用程序由通过 HTTP 相互通信的微服务组成，请求标识符可以在 HTTP 头中发送，并用于跟踪链上所有微服务生成的日志中的请求链。对于诊断和监控目的来说，很难过高估计这个特性的价值。

作为一名开发人员，您可能希望在一个地方配置 web 框架和/或日志库，并自动生成请求 id 并打印到日志中。但很遗憾，可能是 Node.js 世界的问题。

在这篇文章中，我们将讨论这个问题和一个可能的解决方案。

### 好吧，但这甚至是个问题吗？

在许多其他语言和平台中，如 JVM 和 Java Servlet 容器，HTTP 服务器是围绕多线程架构和阻塞 I/O 构建的，这不是问题。如果我们将处理 HTTP 请求的线程的标识符放入日志中，它已经可以作为跟踪特定请求的自然过滤参数。这个解决方案远非理想，但是可以通过使用[线程本地存储](https://en.wikipedia.org/wiki/Thread-local_storage) (TLS)来进一步增强。TLS 基本上是一种在与当前线程相关的上下文中存储和检索键值对的方法。在我们的例子中，它可以用来存储为每个新请求生成的 id(和任何其他诊断数据)。许多日志库都有围绕 TLS 构建的特性。例如，查看 [SLF4J 的映射诊断上下文](https://logback.qos.ch/manual/mdc.html)的文档。

由于 Node.js 基于[事件循环](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)的异步特性，根本不存在线程本地存储，因为 js 代码是在单线程上处理的。如果没有这个或类似的 API，您将不得不在整个请求处理调用中拖动一个包含请求 id 的上下文对象。

让我们看看它在一个简单的 Express 应用程序中是什么样子的: