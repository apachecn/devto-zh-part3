# JMQT (JSON 消息队列和传输)——一种新的物联网和消息协议

> 原文：<https://dev.to/shubhadeepb14/jmqt-json-message-queuing-and-transfer---a-new-iot-and-messaging-protocol-3on8>

亲爱的开发者们:

在过去几年我在许多物联网项目中使用 MQTT 后，我意识到对于初学者来说，理解并在项目中实现 MQTT 有一个巨大的学习曲线。MQTT 有一个复杂的包结构，为特定需求定制开放源码 MQTT 代理对初学者来说也很困难，有时甚至对专家来说也是如此。

我开发了一个名为 JMQT(JSON Message Queuing and Transfer)的物联网和消息协议，它最大限度地减少了开发人员的学习曲线，并可以加快开发周期。该协议的主要特点如下:

1.  它基于 JSON，比现有的物联网协议(如 MQTT)简单得多。大多数主流编程和脚本语言都支持 JSON。
2.  与使用传统用户 id 和密码的其他 IoT 协议(如 MQTT)相反，JMQT 使用身份验证令牌实现了身份验证系统的现代化。
3.  JMQT 非常适合初学者理解发布-订阅协议的基础。
4.  JMQT 很容易扩展和定制。向 JMQT 包添加新参数非常容易，因为它使用 JSON。
5.  JMQT 支持点对点消息传递。这允许程序员开发健壮的解决方案(例如，物联网或信使应用程序)，这些解决方案需要客户端之间的点对点通信。

我还开发了一个实现 JMQT 协议的开源项目。这个项目包括一个支持 SSL 的 python Socket & WebSocket 服务器、一个 python 负载平衡器、一个 python 客户端和一个 Javascript 客户端。初始代码已准备好，经过测试并发布。

想了解更多关于 JMQT 的信息，请访问[http://jmqt.org](http://jmqt.org)。JMQT 开源项目可以在 https://github.com/shubhadeepb14/jmqt 的[找到。](https://github.com/shubhadeepb14/jmqt)

如果您对 JMQT 及其开源实现感兴趣，请告诉我。欢迎合作者。对于任何问题，你可以在这个帖子里评论或者直接给我发消息。

感谢您耐心阅读这篇长文。编程快乐！