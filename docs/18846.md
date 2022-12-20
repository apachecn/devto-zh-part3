# 向 Web 共享目标 API 注册为共享目标

> 原文：<https://dev.to/chromiumdev/registering-as-a-share-target-with-the-web-share-target-api-3aep>

Pete LePage 通过 origin 试用版介绍了 Web Share 目标 API 和 Chrome 中的可用性

> 到目前为止，只有本地应用可以注册为共享目标。web 共享目标 API 允许已安装的 Web 应用向底层操作系统注册为共享目标，以从 Web 共享 API 或系统事件(如操作系统级共享按钮)接收共享内容。

[阅读全文](https://developers.google.com/web/updates/2018/12/web-share-target?utm_source=feed&utm_medium=feed&utm_campaign=updates_feed)。

这个 API 是网络上的游戏规则改变者，它打开了网络，让原本只有本地应用才能使用的东西:本地共享。应用程序是孤岛，它们吸收所有数据，使得跨平台访问变得困难。Share Target 开始创造公平的竞争环境，让网络也能参与同样的游戏。

Twitter 移动体验的共享目标[已经启用](https://mobile.twitter.com/manifest.json)。这篇文章是使用我在我的网站“管理面板” [manifest.json](https://paul.kinlan.me/share/share-manifest.json) 中定义的共享目标创建的——它工作得非常好，一旦他们获得文件支持，我就可以将我设备上的任何图像或 blob 发布到我的博客上。

非常激动人心的时刻。

阅读链接文章，了解更多关于 API 何时上线以及如何使用 API 的时间表。