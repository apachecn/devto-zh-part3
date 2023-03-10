# Logary Rutta 简介

> 原文：<https://dev.to/henrikfeldt/logary-rutta-intro-254d>

Logary 的 v5 版本即将发布，所以我想现在是时候在 dev.to 上发布我的第一篇博客了。

在过去的几周里，我在 Rutta 上做了很多工作，这是 Logary 的日志路由器，目的是让它更容易使用，更可靠。Logary 是核心库，Rutta 是一个普通的程序/服务，你可以在 docker 容器中运行，并向其发送日志。

图书馆的生命周期是这样的:

*   创建一个新的日志实例，并使用您想要发送日志的位置(目标)对其进行配置
*   从实例中获取一个`Logger`并将`Message` s 记录到其中

Rutta 的生命周期如下:

*   选择其模式:

    *   用于接收数据并将其发送到目标的路由器
    *   发送到 Rutta 路由器或 Rutta 代理的发货人
    *   代理桥接子网并转发日志
*   如果是路由器，您现在可以选择它监听的内容和发送的目的地:

    *   提供若干“监听器”，包括:
        *   传输(TCP、UDP、HTTP)
        *   一次装订(如`[::]:8080`)
        *   编解码器(JSON、plain、binary、log4jxml 等)
    *   提供一个以 URI 格式提供多个“目标”，发送至，例如:
        *   `stackdriver://./?projectId=my-gcp-project`
        *   `console://./`

就是这样；你可以自己用`docker run --rm -it haaf/rutta:5.0.0-rc.2.211cd352`试试——或者用它的[舵图](https://github.com/logary/logary/tree/master/src/services/rutta-helm-chart)

伐木快乐！