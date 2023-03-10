# Apache Camel # 1-Apache Camel 简介

> 原文：<https://dev.to/djoleb/introduction-to-apache-camel-7m9>

你好世界！

这是我的第一篇文章，所以我决定写一写我在过去几个月里经常使用的框架。

Apache Camel 是一个非常强大的集成框架，它被用作两个系统之间的中介引擎，它有一个很棒的社区，有超过 200 个可以开箱即用的[组件的大型代码库](http://camel.apache.org/component-list.html)，当然它是用 Java 编写的。

在 project [官方网站](http://camel.apache.org/)上，你可以找到文档和所有关于发布的信息。

## 骆驼语境

上下文是 camel 应用程序的核心，它代表运行时系统。

## 骆驼路线

路线示例:

[![](img/db502477cd60ca0a552ac3f4b0325bc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vhdJtYLl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/83icy3metd39k0agqj1h.png)

解释:

正如您可能注意到的，Route 类扩展了 RouteBuilder 类，您将从该类覆盖配置并在其中写入您的路由。

这条路线我用的是 JavaDsl，你可以用 camel 支持的任何其他  。

error handler(deadLetterChannel(" mock:errorRoute "))-这是该特定路由的错误处理程序，如果发生任何异常，消息将被发送到死信模拟队列" error route "。

from("timer:timerName？period=5000") - "from "就像 camel 上下文中的一个端点。在这种特殊情况下，计时器组件(调度程序)将每 5 秒触发一次路由执行，计时器名称为“timer name”。你可以在这个[链接](https://camel.apache.org/from.html)上查看更多信息。

log("路线开始！")-这是一个骆驼的日志组件，它将在控制台中打印文本。

to("mock:anotherRouter") - "to "表示将在哪个端点、队列或路由上发送消息。“mock:anotherRoute”表示将在其上发送消息的模拟队列。

这些都是关于骆驼的最基本的东西。

如果你有兴趣学习更多关于 Apache Camel 的知识，欢迎在这里联系我，或者在 gitter -> [@djoleb](https://dev.to/djoleb) 上联系我。

谢谢！