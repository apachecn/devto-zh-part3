# 玩 P2P 协议和 Firefox

> 原文：<https://dev.to/soapdog/playing-with-p2p-protocols-and-firefox-3dab>

[https://www.youtube.com/embed/Yg3O8n6hJqI](https://www.youtube.com/embed/Yg3O8n6hJqI)

# 老浏览器可以学新花样

重要的是，我们要让浏览器朝着我们想要的未来发展。正如视频中提到的，我在去中心化领域投入了大量资金，我想要一个用户代理来释放我的代理，允许我浏览我想要的平台，同时保持对我的数字生活的控制。

我们可以通过使用 [Web 扩展(在 Firefox 中它们被称为插件)](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions)来增加我们最喜欢的独立浏览器的功能，为浏览器添加新功能，包括支持全新的协议。

在本视频中，我使用了以下附件:

*   [DAT P2P 协议](https://addons.mozilla.org/en-US/firefox/addon/dat-p2p-protocol/)
    *   来源:https://github.com/sammacbeth/dat-fox/
*   [IPFS 的同伴](https://addons.mozilla.org/en-US/firefox/addon/ipfs-companion/)
    *   来源:https://github.com/ipfs-shipyard/ipfs-companion
*   Patchfox ，还没准备好，所以这个链接是 Github 上的源码。

它们中的每一个都接管不同的协议模式，例如`dat://`、`ipfs://`和`ssb:`。我[是将那些协议](https://bugzilla.mozilla.org/show_bug.cgi?id=1428446)列入白名单以便在网络扩展中使用的补丁的作者，所以从我制作那个补丁的那天起，我就一直希望能够使用这样的插件。我认为我们应该更多地投资于构建这些类型的插件，塑造我们的浏览器体验，以支持对等网络，而不是发布许多不同的基于电子的 Dapps，每个 Dapps 都自成体系。

# 走向一个促进去中心化的用户代理

我在视频中简单提到了在 Libdweb 上正在进行的实验。这是一个 WebExtension，它为其他 Web 扩展添加了新的 API(也称为 WebExtension Experiment ),尽管它还没有走上实现或发布的轨道，但它是一个非常棒的工具包，允许我们研究如果插件作者可以访问低级 API，我们可以在浏览器中构建什么样的体验。

Libdweb 增加了对以下内容的支持:

*   TCP 套接字
*   UDP 数据报
*   文件系统访问
*   数据流
*   自定义协议
*   mDNS

因此，使用这些 API，开发人员可以在 Firefox 中构建一个完整的 IPFS 或 DAT 节点(这已经发生了，非常棒)。最近，作为一个玩具，我用这些 API 构建了一个小小的 Web 服务器演示。看到你的浏览器转变成一个服务器是很有力量的。

# 最终想法和反馈请求

所以，我希望你们喜欢这个小视频。我仍在开发 Patchfox，希望它能尽快进入测试阶段。让我们所有人在同一个用户代理中一起构建分散式 web。

请告诉我你对这些想法和视频的看法。联系我:

*   安全的小道消息:`@gaQw6z30GpfsW9k8V5ED4pHrg8zmrqku24zTSAINhRg=.ed25519`
*   乳齿象: [@soapdog@toot.cafe](https://toot.cafe/@soapdog)
*   推特: [@soapdog](https://twitter.com/soapdog)

你也可以订阅我的 [RSS feed](http://andregarzia.com/feeds/all.rss.xml) 来了解我的最新动态，还可以订阅我的 [YouTube 频道](https://www.youtube.com/channel/UCrZBEFp11PB7TkryjS2LWHw)来看更多关于去中心化、编程语言和网络的视频。