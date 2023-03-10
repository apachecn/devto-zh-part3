# DSCE·哈克，2016

> 原文：<https://dev.to/laneone/dsce-hack-2016-1k9>

达雅南达·萨加尔工程学院，我目前所在的学院，在 2016 年 3 月期间为该学院的计算机科学工程学生举办了一场公开主题的 16 小时黑客马拉松。大多数二年级和三年级的学生都参加了，只有我是一年级的学生。

我想在物联网领域开发一个产品，一位来自 DERBI 的导师建议我试着向他展示一个全功能的物联网堆栈，我同意了，并着手开发了一个。

HackIndia 对物联网有所了解，在这个演示中，我有两个 led 来代表两个房间的灯泡状态，这些 led 连接到一台笔记本电脑，而笔记本电脑又连接到互联网。我用一个运动传感器构建了一个单独的电路，它连接到另一台笔记本电脑，并发送请求来打开/关闭连接到第一台笔记本电脑的 led。除了运动传感器，你还可以在我们快速编写的 Android 应用程序的帮助下改变开关的状态，它还可以向 LED 连接的笔记本电脑发送请求。这一切之所以成为可能，是因为第一台笔记本电脑上运行着一个 web 服务器，我们为这个服务器做了一个前端，这样你也可以从电脑上切换 led。因此，每个 LED 有 3 个可切换的接入点，您可以轻松地将 LED 替换为一个实际的家用电器，每个电器有一个单通道继电器。

这使你可以在技术的帮助下控制你的家用电器，你可以潜在地扩展这个系统来检测运动，如果没有任何运动，你可以自动关闭房子里的所有灯来省电。为了改进产品，您可以添加热水瓶传感器，这样您就可以知道房间的温度，并改变风扇的速度以匹配给定的用户温度，您还可以添加光传感器，这样您就可以调暗自然光。添加谷歌的 Nest API 支持将有助于新设备与现有物联网堆栈进行交互，大多数切换可以通过 Nest API 处理，因此我们不需要为用户构建客户端界面。

目前，这是用 Arduino 构建的，因为它只是一个演示，如果您要按比例实施，您最好使用具有强基本信号的 ESP8266，这样您就可以对设备进行对等控制，而且成本也会大大降低。进入将使用 ESP8266 制造的产品的成本壁垒可能低至 Rs。家里每件家电 350 卢比。ESP8266 和 Rs 为 280 英镑。单通道继电器为 70。

评委们对我的产品印象深刻，我在这次黑客马拉松中获得了第一名，我获得了一枚奖牌、一张证书和一张 Rs 的亚马逊礼券。1000.