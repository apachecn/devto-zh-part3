# 为什么您应该考虑分发您的 Web 应用程序？

> 原文：<https://dev.to/azure/why-should-i-distribute-my-web-app-1kk8>

在 Twitter 上关注我，很乐意接受您的反馈和评论。

##### *分布式系统、流处理和大数据系统成为头条新闻。但是，我们为什么需要它们呢？*

*   建立分销系统的 4 个理由以及 Kubernetes 如何帮助我们开始。

#### 什么是 web app？

web 应用程序是一种可以通过网络访问的应用程序，通常通过网络浏览器进行访问。它包含服务器和客户端。客户端包含用户界面和客户端逻辑。该服务器通常包含数据库、一些逻辑和可以使用的其他服务器。在服务器端，我们通常为服务器和客户端定义一个 API 来进行通信。这是他们交流的语言。服务器也可以使用相同的 API 与其他服务器通信。定义 API 的方法有很多，我最喜欢的是 Petr Gazarov 的方法。

[![](img/0905550606a66e92e6375a53a2fefb70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PsOzNhBl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.techterms.com/img/lg/client-server_model_1253.png)

#### 很好，但是什么是分布式 web 应用程序，我为什么需要它？

分布式系统是多台机器上的组件通过网络相互传递消息进行通信的系统。它的组件通常位于不同的计算机网络上，它们通过相互传递消息来进行通信。消息传递协议和它们通信的方式是由一个特定的 API 定义的，该 API 是为支持这一目的而构建的。通过消息系统，他们使用**通信模型**协调他们的行动。一种常见的通信模式是*主/从。*我们有一个*主*控制流程，多个*从*执行主给的任务。_ 这个通信模型是在 Apache Spark 中实现的，它被称为驱动程序和执行程序，其中驱动程序是主程序，执行程序是从程序。

有多种方式来构建具有各种通信模型的分布式系统，如[客户端-服务器](https://medium.com/r/?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FClient%25E2%2580%2593server_model)、[三层](https://medium.com/r/?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FThree-tier_%28computing%29)、[T5】、n 层、或](https://medium.com/r/?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FMultitier_architecture)[点对点](https://medium.com/r/?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FPeer-to-peer)；或者分类:[松耦合](https://medium.com/r/?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FLoose_coupling)，或者[紧耦合](https://medium.com/r/?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FComputer_cluster)。但是，今天我们要关注的是**为什么**。

#### 那么，我们为什么需要分布式系统呢？

假设我们构建了一个 web 应用程序。开始时，我们服务 100 名满意的客户，我们的产品不断增长，在一年内，我们现在需要服务 1000 万名客户。我们希望尽可能快地为这些客户服务，而不让他们感觉到延迟。最重要的是，我们希望该应用程序在任何时候都可用，无论发生什么情况。此外，我们希望应用程序能够充分发挥其预期功能，也就是说，我们需要可靠性。

### **构建分布式系统的 4 个理由:**

#### 1 -可用性

它是系统在任何给定时间可用的概率。确保如果一台服务器出现故障，我们有另一台服务器来替换它，而用户不会注意到任何延迟。更好的是，不断地监控服务器，以确保它可以满足客户即将到来的请求。让多个组件准备好承担这个角色有助于使系统高度可用。

#### 2 -可扩展性

可伸缩性有多个组成部分:可伸缩的数据库，可以保存和管理数 Pb 数据的系统，和/或可以处理数百万个并发请求的系统。在每一种情况下，我们都可以使用一种分布式方法来构建一个可以在需要时伸缩的系统。如果我们从一开始就为系统设计规模，那么向系统添加组件应该不难。

#### 3 -可靠性

系统产生正确输出的概率。一个可靠的系统不会在数据丢失或损坏的情况下安静地继续运行并交付结果。相反，它会检测故障，发出警报，并在可能的情况下从故障中恢复。构建一个具有可靠性的系统就是要确保我们的客户可以依赖系统提供的响应和数据。这可能包括他们正在建立的业务，如商业发展分析、金融机构的风险管理分析等。当我们使一个系统更加可靠时，我们就减少了它在特定时间段内发生故障的机会。我们通过增加组件的冗余或复制来做到这一点，并管理它们以实现高可用性，使它们更加可靠。

#### 4 -透明度

客户不知道分布式系统，因为他们与产品的交互就像他们与集中式系统的交互一样。这意味着系统的分发与客户是隔离的。我们获得了分布式系统的所有好处，而客户却感觉不到。

#### 既然我们明白了为什么，那就让我们触及什么，什么是最简单的开始方式？

[![Alt Text](img/1f1f6d742c0e1650d4bad5d7822e2930.png)](https://i.giphy.com/media/4abqdfTBWYD5KsUrBg/source.gif)

像 [AKS](https://medium.com/r/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Faks%2Fintro-kubernetes%2F%3FWT.mc_id%3Ddevto-blog-adpolak) 这样的托管 Kubernetes 服务可以为构建分布式系统提供一个坚实的平台。我们定义节点的数量——这是 Azure 虚拟机的数量，Azure Kubernetes 服务负责控制平面或主节点，它们是免费管理和提供的。从图中，您可以看到一个非常简化的 Kubernetes 集群的 worker 节点图，这些节点托管我们的客户将要访问的 web 应用程序。负载平衡器接收客户请求，并将其发送到我们的 web 应用程序，该应用程序的组件分布在我们的工作节点池中。组件可以复制，我们的节点池可以根据我们的负载增加或减少。

[![](img/accb030442e99d9325a859910795c16c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8uut_EAC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AJ4BypEYTe_qpOymFFexvGg.png)

在[这篇文章](https://dev.to/azure/virtual-kubelet-with-spring-boot-and-the-jvm-what-is-it-and-how-you-start-48mj)中，你将了解我们如何创建分布式 web 应用程序的所有细节:)

#### 引用:

*   [维基百科-可靠性、可用性和可服务性](https://en.wikipedia.org/wiki/Reliability,_availability_and_serviceability)
*   [AKS 简介](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes/?WT.mc_id=devto-blog-adpolak)
*   [K8S 为什么牛逼](https://medium.com/@jandavid.staerk/why-kubernetes-is-awesome-9f7ff0186996)