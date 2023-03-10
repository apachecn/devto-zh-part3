# 我无法构建的东西:ActivityPub 上的播客

> 原文：<https://dev.to/flaque/things-i-cant-build-podcasting-on-activitypub-4g1o>

编辑:2019 ->🎉我要恢复这个项目。我在 Quirk 上的工作达到了一个更稳定的点，这让我可以回到这里。尽管可以随意阅读。🎉

* * *

我通常喜欢写一些讲述一些技术主题的故事；如果你和我一样喜欢这些，那么这篇文章可能不适合你。它并不是特别完美，也不意味着仅仅是对维护者的一个呼吁。📺定期安排的节目将很快回来。

在过去的一年里，我意识到有很多事情我想去做，但是我可能不会去做。

为了更好地保持生活中的平衡，我限制自己穿两种类型的编程服装:

*   单个核心项目(目前被分割)
*   个人学习

虽然副业可以融入个人学习，但目的是自我成长，而不是产品本身。目前，成为一名更好的作家和研究者是我目前的个人学习主题。

# 但有些东西是应该建造的

有很多项目我很想看到建成，不仅因为我认为它们对这个世界非常有价值，还因为我认为从事这些项目会很有趣。

如果你正在寻找一个大的肉类项目，这些会成为我的存在吗，wontcha？

* * *

## ActivityPub 应该保存播客

播客正在经历一段时间。随着越来越好的播客发布，越来越多的人知道他们也可以用两个男人的谈话来填补他们生活中的每一个清醒的时刻，听众人数也在上升。

与此同时，播客赖以建立的技术 RSS 正在慢慢消亡。[RSS 在 andrewchen](https://andrewchen.co/the-death-of-rss-in-a-single-graph/) 的单一图中的死亡

许多较大的玩家已经注意到了这一点，主要是 Spotify，并试图填补这一需求。在未来 5 年内，Spotify 可能会取代它的位置，成为“播客的 YouTube”

但这回避了一个问题，为什么在播客成功多年后，还没有一个播客的 YouTube？

### 播客会失去控制

然而，很容易预测这样一个未来:像 Spotify 这样更大的玩家将会给播客提供比 RSS 更多的功能，但同时减少控制；潜在地对媒体有害。

实际上，我们已经在博客上看到了这种情况。随着 RSS 阅读器的消亡，博客们越来越难与观众联系，被迫要么实现自己的订阅服务，要么跳到 Medium 这样的现有平台上。

然而，这现在意味着媒体负责谁能看到什么。他们的算法是决定什么受欢迎和看什么的算法，而不是用户的个人选择。我们离开了媒体。原因如下。–最近–中等

与此同时，youtube 也遇到了类似的问题，因为在这个世界上，YouTube 向你展示的是它认为你想要的东西，而不是人们实际想要的东西，他们没有真正的方法与观众联系。

### 但是等等，等等，等等 RSS 还没有死*还没有*，为什么播客会转换？

因为目前来看，一个集中的系统可以为播客提供比 RSS 更好的工具和功能。

Spotify 可以为你提供最新的、准确的衡量谁在听你的播客的指标。他们可能会准确地告诉你什么时候人们会停止倾听，并衡量你的听众。对于靠广告生存的播客来说，这些都是严重的商业问题。缺乏知识使他们处于不利地位，因为他们有更多的观众，而不知道这一点会使你在赞助谈判中失去优势。

此外，Spotify 可以为听众提供一个更清晰、更容易理解的评级系统，而不像现有的系统那样“不知何故”基于苹果 iTunes 的评级。

Spotify 拥有庞大的网络，可以向新听众推荐他们可能还不知道的节目。对于播客来说，这意味着一个全新的观众群！

这些优势是以失去对你的观众的所有权为代价的。然而，一旦播客转移到 Spotify，就很难回头了。Spotify 有资金和影响力向日益增长的播客市场宣传自己是播客的市场。

很可能在 5 年内，许多外行人会把播客和 Spotify 联系在一起。

### 一种解决方案和前进道路

我们需要的是一个新的、开放的、分布式的标准。RSS 成功是因为*任何人*都可以设置一些东西。

然而，创建一个新标准并立即被采用是非常困难的。

因此，让我们看看已经存在的标准。

### 乳齿象和活动 Pub

目前，有一个联合的 twitter 克隆正在获得动力。它大约有 150 万活跃用户。

乳齿象基于一个叫做 ActivityPub 的网络协议。ActivityPub 可以让几个不同的网站相互交流。

这就是单个乳齿象实例如何与另一个乳齿象实例对话。

但是，这也是乳齿象基于 ActivityPub 与其他网站交流的方式。这个系统的一个奇怪之处在于，Mastodon 上的某个人可以订阅 PeerTube 上某个人的 feed，peer tube 是基于 ActivityPub 的 youtube 克隆。

这提供了一个荒谬的优势。

### 我们将把播客放在 ActivityPub 上

我们需要在公共广播中宣传这家酒吧。(抱歉 Laugantias)。

ActivityPub 将允许任何人在没有中央权威机构所有权的情况下制作播客，但具有中央权威机构的所有好处。

ActivityPub 具有足够的可扩展性，我们可以在它的基础上开发自己的功能，就像 mastodon 所做的那样，但具有足够的互操作性，我们可以立即插入到大量的用户中(fediverse)。

我们应该为播客建立一个乳齿象。

### 还有呢💸钱💸来支持发展

如果开源得不到资金支持，它可能会因精疲力竭而死亡。

ActivityPub 服务器必须托管，而且必须有人为此付费。许多播客*已经*为媒体托管付费；他们还不如花钱买一个能更好地洞察受众的网站。