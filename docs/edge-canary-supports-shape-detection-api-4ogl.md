# 边缘金丝雀支持形状检测 API

> 原文：<https://dev.to/georgemandis/edge-canary-supports-shape-detection-api-4ogl>

微软 Edge Insider 的 macOS 版本今天在 microsoftedgeinsider.com 发布。目前只有金丝雀频道，每天更新。更稳定的测试和开发渠道可能会在未来几周内推出，因为我们将在微软 Build、谷歌 I/O、WWDC 以及其他一些以开发人员为中心的活动之间展开挑战，我忘记了这些活动会在每年的这个时候发生。

Edge 的新版本是建立在 Chromium 上的，这对于网络来说是一个苦乐参半的胜利，取决于你如何看待它。另一方面，选择和差异化是开放网络伟大之处之一。没有一家公司拥有网络，也不应该拥有网络。尽管它做得很好，但采用谷歌的项目来支持他们的浏览器感觉就像微软放弃了对谷歌在网络上的控制权，尽管我认为他们总体上是不错的管理者。

另一方面，对基于 Chromium 的浏览器进行故障排除和调试将比处理 Edge 好得多(T2 )!前几周我在微软 Build 的时候，问了一个关于新 Edge 是否支持形状检测 API 的问题。它现在可以在 Chromium 中找到，隐藏在`chrome://flags.`的实验性网络功能标志下

前几周，我在微软 Build 演讲时问到了这个问题:

> 有人知道基于 Chromium 的新 MS Edge 是否有一个相当于“实验”页面(chrome://flags)的页面来启用实验性 web 平台功能吗？
> 
> 你可以在 Chromium 上启用形状检测 API，这让我有点吃惊。想知道 Edge 是否继承了这个...
> 
> —乔治·曼迪斯(@乔治·曼迪斯)[2019 年 5 月 3 日](https://twitter.com/georgeMandis/status/1124443813110374400?ref_src=twsrc%5Etfw)

长话短说——看起来你可以！只要转到`edge://flags`，你就会发现一个相同的屏幕。

> 来回答我自己的问题:是的！在 macOS 上的 Edge Canary 中，您可以在以下位置找到标志:edge://flags/
> 
> 我还测试了形状检测 API，它工作正常！！！
> 
> 从长远来看，我很兴奋地认为这意味着对一个非常酷的功能的广泛支持:[https://t.co/wj4CKixLGZ](https://t.co/wj4CKixLGZ)https://t.co/Dx4Pt1fZch[T3】](https://t.co/Dx4Pt1fZch)
> 
> —乔治·曼迪斯(@乔治·曼迪斯)[2019 年 5 月 21 日](https://twitter.com/georgeMandis/status/1130920860330602496?ref_src=twsrc%5Etfw)

一旦你启用了这个，你应该能够在我的被稍微忽略的项目**调试艺术**中看到这个演示:

[debuggingart.com/sketch/shape-detection-dva-brata/](https://debuggingart.com/sketch/shape-detection-dva-brata/)

尽管我对浏览器同质化的状况仍是喜忧参半，但我确实认为像这样的功能可能会更快地广泛传播是件很酷的事情。