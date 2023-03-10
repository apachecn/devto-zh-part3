# Mercure(简单实时更新)

> 原文：<https://dev.to/zairigimad/mercure-simple-real-time-updates-mmo>

在过去的几周里，我发现了 [Mercure](https://github.com/dunglas/mercure) ，这是我第一次对它进行概念验证测试，并意识到这是实时应用程序开发新时代的开始。

我刚刚参加了 Symfony Live Paris，Mercure 的创建者 Kévin Dunglas 做了一个关于 Mercure 和 API-Platform 的非常有趣的演讲，我告诉自己与 dev.to family 共享该工具会很酷，尤其是 Mercure 得到 Symfony [Mercure 组件](https://symfony.com/doc/current/components/mercure.html)的官方支持。

# 什么是美居？

> 是一种协议，允许以方便、快速、可靠和节省电池的方式将数据更新推送到 web 浏览器和其他 HTTP 客户端。将通过 web APIs 提供的资源的实时更新发布到反应式 web 和移动应用程序尤其有用。

[![Mercure](img/130750e8700d99aa5b028b738be6627e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QQWhCImw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/dunglas/mercure/raw/master/spec/subscriptions.png)

### Mercure 和 WebSocket 有什么区别？

WebSocket 是一个底层协议，Mercure 是一个高层协议。Mercure 提供了方便的内置功能，如授权、重新连接和状态协调；而使用 WebSocket，您需要自己实现它们。此外，与 Mercure(构建在 HTTP 和服务器发送的事件之上)不同，WebSocket [不是为利用 HTTP/2](https://www.infoq.com/articles/websocket-and-http2-coexist) 而设计的。

默认情况下，HTTP/2 连接是多路复用和双向的(HTTP/1 不是这种情况)。当通过 h2 连接使用 Mercure 时(推荐)，您的应用程序可以通过服务器发送的事件接收数据，并通过常规的`POST`(或`PUT` / `PATCH` / `DELETE`)请求向服务器发送数据，没有开销。

基本上，在大多数情况下，Mercure 可以作为 WebSocket 的一个更现代、更易于使用的替代品

### Mercure 和 WebSub 有什么区别？

WebSub 是一种仅服务器到服务器的协议，而 Mercure 也是一种服务器到客户端和客户端到客户端的协议。

Mercure 受到了 WebSub 的极大启发，我们试图使协议尽可能接近 WebSub 协议。

Mercure 使用服务器发送的事件来分发更新，而 WebSub 使用`POST`请求。此外，Mercure 有一个先进的授权机制，允许订阅几个主题，只有一个连接使用 URI 模板。

我邀请您尝试一下 Github 资源库【https://github.com/dunglas/mercure】
[中的](https://github.com/dunglas/mercure)[演示](https://demo.mercure.rocks/)应用程序和完整文档