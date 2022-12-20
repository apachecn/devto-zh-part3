# 如何使用 Firebase？

> 原文：<https://dev.to/alejandro/how-to-use-firebase-2pb0>

我一直在阅读有关 Firebase 的内容，并观看了一些“入门”视频，但我仍然不明白。

在我看到的视频中，我经常会发现“如何将 firebase 与[Vue|Angular|React]连接起来”之类的问题，但我也有疑问，firebase 只是数据库层还是也是后端层？

例如:

我有一个电子商务网站，用户想要购买产品。在 Vue 上，我会做所有的前端工作。在 Net Core 上，我会做所有的后台工作，比如检查产品是否在库存中。

如果我添加了 Firebase，那么架构会变成这样？

1.  Vue 连接到 Firebase 取回产品。
2.  Vue 连接到 Net Core，后者连接到 Firebase 以了解库存，如果库存不可用，则 Net Core 会回复 Vue，表示不能购买。

在这种情况下:

A.连接 Vue -> Firebase 有什么意义？
B .如果是连接 Vue - >网芯- > Firebase？使用 Firebase 到底能给你带来什么好处？我知道拥有一个实时应用是 firebase 的卖点之一，但我认为连接到网络核心
C 会失去这一点。或者它应该是一些混合架构，可能有 Vue - > Firebase(通知数据库)，然后有 Vue - >网络核心- > Firebase(产品数据库)？

谢谢你的帮助。