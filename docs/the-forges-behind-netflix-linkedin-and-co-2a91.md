# 网飞、LinkedIn 和 Co .背后的伪造者

> 原文：<https://dev.to/schmowser/the-forges-behind-netflix-linkedin-and-co-2a91>

当在家的人提起 Spotify 和网飞时，他们经常会讨论这些平台提供的内容。但是在开发者社区里，我们不仅讨论《纸牌屋》的最新一季，还讨论像这样的公司随着时间的推移设计的框架。

我被这种差异逗乐了，并想知道一家科技公司在哪个点上从内容交付者变成了内容处理者。以 LinkedIn 为例。多年来，个人资料、搜索查询和沟通渠道不断扩大，LinkedIn 将其整体应用基础设施转变为微服务。为了应对这些挑战，LinkedIn 开发了 [Kafka](https://engineering.linkedin.com/blog/2016/04/kafka-ecosystem-at-linkedin) ，一个事件驱动的消息系统。他们从几个用于服务间交互的小型排队管道开始，但随后开发了一个发布者-订阅者平台。[不用一条一条地维护和升级管道。](https://insidebigdata.com/2016/04/28/a-brief-history-of-kafka-linkedins-messaging-platform/)

网飞遇到了类似的问题:由于他们有大量的服务，甚至在不同的区域，但只有很少的边缘服务接收请求，因此必须将如此巨大的呼叫量分配给这些服务。当时的负载平衡算法并没有提供令人满意的性能，所以他们想出了自己的算法并用[丝带](https://medium.com/netflix-techblog/announcing-ribbon-tying-the-netflix-mid-tier-services-together-a89346910a62)把它们绑起来。

基础设施的改变并不是创新的唯一催化剂。即使给定的语言框架不能满足您的需求，您也可以考虑引入自己的语言框架。这就是[脸书如何以及为什么](https://reactjs.org/blog/2013/06/05/why-react.html)生了反应。

而 Spotify 呢？虽然他们没有贡献一个巨大的技术框架，但是他们展示了一种将软件开发的敏捷部分整合到一个组织的整体结构中的方法。他们决定不仅在小团队中使用敏捷实践，而且在大范围内使用。于是，Spotify 推出了[小队、分会、部落和公会](https://www.growly.io/spotify-engineering-model-with-squads-tribes-chapters-and-guilds/)。

提出这些想法是一回事。与他人分享是另一回事。所有提到的新奇事物都冲击了开源领域。上面的消息系统甚至被称为 *Apache* Kafka，以开源许可证命名，其中 *Ribbon* 也被授权使用。更不用说脸书的 React 在前端社区中扮演的角色了。转向开源和公共知识的趋势正在持续。这些公司对公开他们的发明非常感兴趣。

你认为还有哪些公司会成为新技术、新框架或新思想的推动者？或者你有一个坚实的问题，你或你的团队用自己发明的工具集解决了？为什么要开源？