# 人体探测和时间记录灯

> 原文：<https://dev.to/obniz_io/human-detect-and-time-record-lamp-4p67>

[![](img/1532d3f5ad3584414b061136f15ddb5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a8Rsvo_7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f78alokugfu299wyuc5m.gif)

# 人体探测和时间记录灯

## 这是什么

家庭物联网套件示例

这让你的房间灯更加智能。当它侦测到有人时会自动开启。它还在 Dropbox 的一个文件中记录了时间。

# 材料

*   奥布尼斯
*   obniz 的电源
*   PIR 传感器(物联网家用套件)
*   伺服电机(物联网家用套件)
*   步伐
*   首先，将伺服电机连接到墙上的开关上。

[![](img/a328d563db5fc50afa06111bb4a88f1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bR4NyUDp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.obniz.img/9b41b763fcf4d4af1530785186303)

连接方式取决于您的交换机。

并将 PIR 传感器和伺服电机连接到 obniz。然后接通电源。

[![](img/a65c8099afabf66fc862ead5b60b883e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jzcMAcM7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.obniz.img/3dbcef89c385b1ad1530785176591)

编写并运行一个程序。这个示例程序有一个手动方法。
向左转动开关，你的伺服会移动。
调整示例程序上的值以正确工作。

并请指定 dropbox API 密钥。你可以看到如何上课。

我们来测试一下。

当你靠近一个传感器时，你的伺服会移动。你会在你的 dropbox 上看到一个包含时间的文件。

[![](img/aa416d4045359ea17a32a1798e85c565.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fuHNOO6Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.obniz.img/2c0128888e59baeb1530785914792)

这一次我使用 HTML，但是 nodejs 会更有效。它坚持不懈地工作。

此外，您可能希望自动关闭或将更多信息记录到文件中。似乎很有趣

## 程序

[https://obniz.io/explore/33](https://obniz.io/explore/33)