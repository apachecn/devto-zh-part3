# KubeCon 2018 -现在开发者的故事是怎样的？

> 原文：<https://dev.to/adron/kubecon-2018-41m1>

这是我在 KubeCon 的第二年。今年我的使命与去年大不相同。去年，我想了解更多关于服务网格的知识，Kubernetes 的各种特性的状态，比如有状态集，并对产品的整体形状和项目的方向有一个更好的了解。

今年，我想找出两件特别的事情。

*   *开发者故事:*开发者故事变得更好了吗？
*   *数据库故事:*在 Kubernetes 上，有什么数据库和它们各自的存储需求有好故事吗？

嗯，我拿起我可信赖的 GoPro 相机，把它像“掠夺者”用的佳能相机一样架在我的肩膀上，然后离开了。我将带着一个略有不同的进攻计划去参加这次会议。我希望有视频，而不仅仅是做一些笔记，参加一些会议，并尝试通过这种方式搜索我收集的信息。我的想法是这样的，有了额外的资源，我将能够回忆和使用更多可用的信息。有了这些，这是我在仔细查看展厅时拍摄的视频和一些一般性观察。在折叠和视频下面，我会有额外的评论，链接和更新，以及更多揭露垃圾的好消息！

[https://www.youtube.com/embed/EhVq598LEI0](https://www.youtube.com/embed/EhVq598LEI0)

**[![Gloo-01](img/2ea97427ffb28bd156b6367ff8622b2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OY2yvyLu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://compositecode.files.wordpress.com/2018/12/Gloo-01.png)Gloo**——好吧，这看起来有点意思。我停下来看，只是因为它有有趣的字符。当我走到展台前时，我听了几分钟，但最终意识到我需要挖掘一下网上存在的文档和宣传资料。以下是一些快速总结。

Gloo 作为一个应用程序网关而存在。有点像网眼之类的东西，不是很清楚。但是我[在这里](https://github.com/solo-io/gloo)RTFMed 了 Github Repo，并抓取了这个高级架构图。使它变得有趣，并为它的使用提供了前瞻性的见解。

[![high_level_architecture](img/9d03ec0241e265b31117be1bf7ab3167.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--teGQKxMV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://compositecode.files.wordpress.com/2018/12/high_level_architecture.png)

Gloo 有一些，我想它们也是“子”项目。这是一组 em 的截图。单击它导航到 solo.io，它似乎是父组织。那里有一些非常酷的软件，很多开源软件。这也让我想到这可能是我正在寻找的第一点的一部分，即*改进的开发者故事*在哪里？

[![gloo.png](img/19e691fa0e15c013ad5fdd377d6b8bc8.png)](https://www.solo.io/)

稍后会有更多的内容，现在，在继续下一篇博文之前，我想再谈一件事，关于我很想告诉你的 KubeCon 的细节。

[![ballerina-logo](img/32050799479af2095eb6e8934c5b1533.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x43ByzNm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://compositecode.files.wordpress.com/2018/12/ballerina-logo.png)

芭蕾舞演员 -好的，当我走近展台时，我并不 100%确定这将是我想要的。在得到一个演示(也有视频)然后回到网上——就像你做的那样——然后挖掘细节并稍作思考，我变得有希望了。这一堆技术看起来不错。我们来复习一下！

[网站上的描述](https://ballerina.io/)将芭蕾舞女描述为，

> 一种编译的、事务性的、静态的和强类型的编程语言，具有文本和图形语法。Ballerina 结合了分布式系统集成的基本概念，并提供了一个类型安全的并发环境，以实现具有分布式事务、可靠消息传递、流处理和工作流的微服务。

这听起来很可靠，可以真正帮助开发者以一种非常有意义的方式构建——比如说 Kubernetes。然而，它也可以扩展到远远超过 Kubernetes，这是我希望看到的，并帮助开发人员扩展和加快他们的过程和业务线应用程序的开发。目前，它仍然是古老的 RAD 工具，一直到今天的 React 和 web 工具，没有一个好的方法来开发那些理解或集成 Kubernetes 等现代工具的工具。它总是，把它塞在上面，配置一堆 yaml，然后把它扔过墙。

关于网站上如何描述芭蕾舞演员的更多要点。这是既定的哲学目标，

> " *Ballerina 使编写云原生应用变得容易，同时保持可靠性、可扩展性、可观察性和安全性。*

可观察性和安全性？好吧，我会进一步调查这个问题，并在未来几周内最终潜入*。看起来 2019 年将是我钻研不止一门新语言的一年！呀，那会很激烈的！*

 *显然，PingCap 的团队没有听从“不要编写自己的数据库，它会……”来自慈善专业和无数其他人，他们写了自己的数据库！但是，嘿，这一个，不管被忽视的建议，看起来有点有趣。就在 [TiDB repo](https://github.com/pingcap/tidb) 中，他们有一个架构图...好吧，先看看图表。

[![tidb-architecture](img/1ca857c8e87c009508bcae43fbfe6997.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f56v3WN5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://compositecode.files.wordpress.com/2018/12/tidb-architecture.png)

所以它有一个 mySQL 应用程序协议，与 TiDB 集群连接，然后 TiDB 集群有一个 DistSQL API(？？)和 KV API 连接到 TiKV(代表 Ti 键值), TiKV 是一个集群，然后使用 DistSQL API 将另一个方向连接到 Spark 集群。然后可以使用 Spark SQL。似乎运行的主题是 SQL 所有的东西。

除此之外，为了管理集群，还有一个“PD 集群”,我也需要了解一下。如果你看过上面的视频，你会注意到提到它是系统的动物园管理员。这个“PD 集群 ZooKeeper”管理元数据、TSO 数据位置和与 Spark 集群相关的数据位置。总体而言，4 个集群管理系统。

为了更好地衡量(也在视频中), TiDB 内置于 Go 中，而 TiKV 内置于 Rust 中，一些数据位置或部分 Spark 通信由一些 Java 虚拟机处理——我想——我可能误解了一些解释。那么这一切是如何运作的呢？我不知道这一点，但我很想知道。但在此之前，在接下来的几周和几个月中，我将深入研究用 Node.js、Java 和 C#构建应用程序，以对抗 Cassandra 和 DataStax Enterprise，因此我可能会添加一些与 TiDB 的交叉比较。

此外，尽管我没有与 Foundation DB 的任何人交谈过，但我也对它目前的工作方式感兴趣，尤其是考虑到它有些传奇的历史。但是，嘿，现在哪个项目没有传奇的历史呢？请继续关注，在博客上订阅，我会在该作品和其他视频、twitch streams 等发布时更新。

在所有那些谈话和在地板上跑来跑去之后，在这一点上我不得不休息一下喝咖啡。因此，请欣赏这段视频，了解如何快速、恰当地享用美味的咖啡和饼干。干杯！

[https://www.youtube.com/embed/jfdeBhOldIA](https://www.youtube.com/embed/jfdeBhOldIA)

###### 是的，我写错了“dummy ”,没关系，我不想重新渲染它。我也知道饼干的名字有点粗俗 LOLz，但你知道吗，欢迎来到西雅图我们爱你，即使你的思想在阴沟里！

我在 Twitter [@Adron](https://twitter.com/Adron) 和 Twitch [@adronhall](https://twitch.tv/adronhall/) 上一直听金属和编码，写关于编码、学习和教授各种主题，从数据库技术到编码印章。感谢阅读！*