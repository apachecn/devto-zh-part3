# Lonestar ElixirConf 2019 亮点

> 原文：<https://dev.to/jackmarchant/lonestar-elixirconf-2019-highlights--575n>

上周是在德克萨斯州奥斯汀举行的 Lonestar ElixirConf 2019。这个会议持续了两天，是我参加的第一个灵药会议。

在这篇文章中，我将回顾我个人在这次会议上的一些亮点，包括我对一些会谈的想法。不过，在我开始之前，我想先说，在一个挤满了各种经验水平的仙丹爱好者的房间里是多么的棒。一些人在那里寻找一种方法来帮助他们的组织销售长生不老药，而另一些人对生产长生不老药有着有益的见解。会议组织得非常好，日程安排允许在所有精彩的演讲之间有足够的休息时间。

### 神经

《神经工程》的作者贾斯汀·施内克为会议做了主题演讲。[嵌入式系统](https://embedded-elixir.com)在 Elixir 社区真正起飞了，Justin 清楚地说明了原因，因为他展示了开始使用 Nerves 是多么容易，以及如何使用 [NervesHub](https://www.nerves-hub.org) ，这是一种允许您管理物理设备固件更新的工具，使部署变得简单而安全。

虽然我还没有做过任何紧张的工作，但它确实让我有兴趣找到一个我可以尝试的副业。

我在 [Vamp](https://vamp.me) 的大部分工作都是在网络上进行的，所以不太可能会有嵌入式系统的需求，但它表明了灵丹妙药的灵活性和独特性，类似神经的东西可以让任何人开始使用真正的设备工作。

### 分布式状态管理

Elixir 社区中的一个热门话题围绕着[分布式状态管理](https://dockyard.com/blog/2018/11/07/the-distributed-state-of-things-new-elixir-library-enhances-development)和常见陷阱，以及潜在的解决方案。

我们都知道，Elixir 在并发性方面非常出色，并提供了一个编程模型，使其变得更加简单，但真正让 Elixir 大放异彩的是当您随着应用程序的扩展添加额外的节点时。

这是会议第一天早上就开始讨论的主题，双方都详细描述了所涉及的复杂性。

会谈并没有真正提供一个具体的解决方案(尽管他们提到了像 [Swarm](https://github.com/bitwalker/swarm) 这样的分布式进程注册表)，而是提到了任何面临这些问题的人都必须做出的权衡，最著名的是[上限定理](https://en.wikipedia.org/wiki/CAP_theorem)以及一致性和可用性的平衡尺度，因为在分布式系统中总会有一个网络分区。

我真的很喜欢这一部分，所以这是会议的一个很好的开始。

### Ecto

随着最近[将 Ecto 库拆分成两部分](http://blog.plataformatec.com.br/2018/10/a-sneak-peek-at-ecto-3-0-breaking-changes) : `ecto`和`ecto_sql`，以使开发者更加清楚地看到，可以在没有数据库的情况下使用 Ecto，因此有一些关于 Ecto 的讨论是合适的。我特别喜欢 Greg Vaughn 的《没有数据库的 Ecto 》,其中 Greg 展示了使用 [Ecto 变更集](https://hexdocs.pm/ecto/Ecto.Changeset.html)来验证外部数据、映射到结构并应用某些动作来实现与 [Repo](https://hexdocs.pm/ecto/Ecto.Repo.html) 回调(如`insert/1`和`update/1`)相同的验证的实际例子。

一般来说，这种方法似乎强调了这样一个事实，即在应用程序中使用数据结构而不是在域逻辑中使用特别的映射，可以更容易地处理错误，并防止混乱的代码。

### 灵丹妙药的商业案例

Brian cardar ella(dock yard 首席执行官)介绍了他对 Elixir 商业案例的看法，特别提到了 4 个要点(释义):

*   稳定:Elixir 中的稳定版本，加上近期不会发布该语言的 2.0 版本的计划，意味着开发人员可以相信他们现在编写的代码能够经受住时间的考验。
*   **效率:**用一种语言开发人员的生产力是非常重要的，特别是对于那些需要将新功能快速推向市场的初创公司。在处理模块(函数组)时，Elixir 的认知负荷较低，这意味着系统的某些部分比其他语言更容易修改，在其他语言中，你可能需要对应用程序的代码库有更全面的理解。
*   **可扩展性:** Elixir 以其以最小的努力、最低的成本进行扩展的能力而闻名。这使得它对于较小的团队来说是一个非常有吸引力的解决方案。
*   **易加工性:** Elixir 的受欢迎程度正在上升，Brian 预计到 2020 年，我们将会看到更多的公司在生产中使用 Elixir。总的来说，布莱恩为那些想把灵丹妙药引入自己组织的人提供了完成工作的合适话题。

### 凤凰直播

虽然已经宣布了(但还没有发布)，但凤凰直播还是在 Lonestar ElixirConf 上向观众展示了，克里斯·麦考德承诺最早在本月底发布，但至少在未来几个月内发布。

Chris 谈到了构建 LiveView 的动机，并强调了尽可能推迟不可避免的单页面应用程序路径的目标。多长时间将在发布后人们有时间使用它时确定。

我个人对此非常乐观，尽管我很高兴在需要时继续在前端编写 JavaScript，但这将使构建原型应用程序来展示 Elixir 的实时功能变得更加容易。

LiveView 的概念编程模型与 [React](https://reactjs.org) 和其他 JavaScript 库非常相似，因为每个组件都有一个父子关系，默认行为是，如果一个子组件出现故障，它可以重新启动到它最后一个已知的状态。正是通过这种方式，他们表现得像一棵监督树上的孩子。React 和 Elixir/Erlang 监督树等前端视图库之间的相似之处是我在之前写过的一个[主题。](https://www.jackmarchant.com/articles/a-comparison-of-elixir-supervision-trees-and-react-component-trees)

### 二郎生态系统基础

在 Jose Valim 的主题演讲中，他介绍了作为 Elixir 和 Erlang(以及在 BEAM(Erlang 运行的虚拟机)上运行的任何其他语言)社区的新组织的 [EEF](https://erlef.org) 它的目标是为社区内的项目筹集资金，帮助改进围绕 Erlang 的工具。

### 百老汇

何塞还展示了一周前刚刚发布的新图书馆[百老汇](http://blog.plataformatec.com.br/2019/02/announcing-broadway)。百老汇是 GenStage 的扩展，它将生产者和消费者建模为吸收和处理数据的管道中的各个阶段。

新的库意味着允许分布式管道并行操作。

核心团队并没有将这些特性添加到 Elixir 语言中，而是倾向于保持标准 API 较小，并与 Erlang 紧密匹配。

### 包装完毕

作为一名灵丹妙药的开发者，这是一个激动人心的时刻，因为这种语言在没有做出任何重大改变的情况下已经成熟了。在未来的几年里，我希望将会有更多公司使用 Elixir 的成功故事，同时促进来自开发人员生产力和快乐的成功，构建 Elixir 应用程序并扩展它们。

Lonestar ElixirConf 2019 似乎非常成功，我当然喜欢在那里。

我要感谢 [Vamp](https://vamp.me) 赞助我从澳大利亚踏上旅程，并希望看到更多的澳大利亚公司加入全球灵药社区。

最初发表于[jackmarchant.com](https://www.jackmarchant.com)