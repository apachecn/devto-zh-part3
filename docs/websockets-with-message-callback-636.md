# 带有消息回调的 Websockets

> 原文：<https://dev.to/aidv/websockets-with-message-callback-636>

这是我参与的一个大项目的一部分，我想其他人可能会觉得它有用。

项目:[https://github.com/aidv/websockets-callback](https://github.com/aidv/websockets-callback)

在我的项目中，我使用 WebSockets 向服务器发送数据，然后等待响应，直到数据得到处理。

一旦收到响应，就会执行回调并通知用户。

我的库大大简化了这样的流程，代码可以像编写任何回调驱动代码一样编写。