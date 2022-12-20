# 我运行了一个复杂得可笑的工程项目(并幸存了下来)

> 原文：<https://dev.to/atlassian/i-ran-a-ludicrously-complex-engineering-project-and-survived-2a54>

想象一下，当你头顶上的城市继续着自己的事业，对地下的阴谋视而不见时，你正在挖掘一条新的地铁隧道。这差不多是我们今年早些时候完成的项目。只有我们在云中工作。

坚实的软件工程原则是交付大型复杂项目的关键——如将 Confluence 和吉拉重新架构为无状态、多租户云应用。这是 Atlassian 有史以来最大的单一工作计划，历时三个日历年，相当于几百年。但我们也在不影响现有客户的情况下做到了这一点，他们中的大多数人甚至没有意识到他们下面正在发生的巨大的架构变化。

如果运行不当，这种类型的项目可能会导致溃疡和不眠之夜。在我们向我们的一位云工程经理描述了规模、范围和积极的时间表后，他们回答说“我真的很喜欢这个想法，但它让我眩晕”。

我们做了一系列正确的事情来让这个庞大的项目运转起来。其中最主要的是我们用来计划和执行项目的四个工程原则:

这是一个巨大的项目，需要整个公司，从高管到实习生，都支持它。
**攻击风险最大的假设**——不要想“MVP”想想整个项目中风险最大的部分是什么，并专注于此，直到它不再是最大的风险。
**增量，即使这会带来伤害**–通过将迁移分成尽可能多的小部分来降低风险，即使这会增加开发时间。
**冲刺到 100%**—在这个项目中，回报是我们可以完全停止部署到旧的基础架构。在你完成、完成、完成、再完成之前，不要放慢速度或失去注意力。

这些，加上我们公司的价值观(尤其是“不要以客户为中心”和“用心和平衡地建造”)，构成了整个项目的基础。

首先，一些背景。吉拉的第一行代码是在 2002 年提交的。2004 年合并。当时，很少有客户准备在云中托管任务关键型数据，因此将它们设计为在客户自己的服务器上托管的单租户应用程序是有意义的。2007 年，我们开始提供合流和吉拉 SaaS 选项，这继续发展。但是一切都是微调，使用容器化为每个单独的云客户单独隔离单租户架构。快进到 2015 年:越来越多的客户正在向云迁移，很明显，这种单租户架构无法满足他们的需求——尤其是如果您计划向外发展大约 15 年的话。我们需要重新构建 Confluence 和吉拉，使其成为真正的多租户、无状态云应用。或者，在堆积如山的技术债务下慢慢崩溃。

多年来，我们一直在激烈讨论如何实现这一愿景。内部讨论了几种方法，其中一些在很多年里采取了非常谨慎的步骤。让我们在我给你讲“迷魂药项目”的故事时，逐一解开这四个工程原理。

## 工程原则#1:全包

2015 年初，我们决定放弃渐进的方式，尽可能快地朝着最终目标前进。我们观察了如果我们把现有的 Confluence 和吉拉后端团队放到项目上会发生什么。这需要 4-5 年的时间。那可不行。无论开发人员多么相信最终的目的地，都很难让同样的人在那么长的时间里对同一个项目保持兴奋。此外，在整个项目完成之前，这是一种增值很少的项目。这使得保持士气更加困难。

你也想尽可能地带来更大的回报。作为一家公司，我们知道全力以赴——在内部进行破坏性的团队变革以更快获得回报——是唯一明智的前进方式。

然而，你显然不能在项目的第一天就全身心投入。实际上，时间线是这样的:

2015 年 1 月——架构峰值和概念验证，表明架构可以工作并处理所需的规模。【2015 年 9 月——通过认真落实一些核心工作来热身。很大一部分工作需要在整个代码库中广泛应用相同的模式(比如从内存缓存中删除租用的数据)。在这一阶段的工作中，我们对那些重复模式的前几个例子进行了编码，包括用于测试和持续集成的模式。【2016 年 3 月–真的，真的全押。每一个可用的人手都准备好了将第一个客户迁移到新架构所需的整个工作范围。【2016 年 12 月——我们迁移了第一个客户！【2017 年 12 月–成功迁移最后一位客户。到这个时候，我们也已经完成了在我们迁移第一个客户之前最初从范围中删除的所有内容(包括功能和性能)。
这是令人兴奋的反映。我们花了几年时间知道(并担心)这个巨大的东西会在未来的某个时刻出现。一旦我们致力于正面解决这个问题，大部分工作在九个月内就完成了。全公司齐心协力在尽可能短的时间内完成了这件事。

如果你想进行如此大规模的转变，只有做出艰难的选择才会奏效。在我们的案例中，这意味着打破现有的部门壁垒，围绕现有的路线图和承诺开展工作。反过来，这只适用于高管买入。在我们的情况下，有了 20/20 的后知之明，我们可以走得更快。我很想把前 15 个月的峰值、概念验证和基础工作压缩得更紧。新的首席技术官 Sri Viswanath 是一个转折点，他带来了更高水平的高管认同和信心，我们需要这些来支持 Vertigo。

改变你的组织结构和移动团队，或者团队中的个人，可能会很难。如果你公司的人对他们所在的团队和他们所做的工作有个人依恋 Atlassian 的开发人员往往对他们的工作有非常高的友情和个人投资——那么改变团队结构会被视为是被迫的和不受欢迎的，并会降低士气。

作为一个管理团队，除非受到变更影响的开发人员相信愿景，否则你无法克服这一点。不要在内部博客、演示、一对一的谈话等方面投资不足。当旅程需要如此重大的内在改变时，带上他们。

## 工程原则#2:攻击风险最大的假设

无论项目是初创公司的第一个原型，还是大型组织中的大型跨部门项目，关于运行软件工程项目的建议都会告诉你要精益，尽可能缩小范围，以将工作软件交到用户手中。这是个很好的建议，但这只是个开始。这也有助于避免不教你任何关于你面前的问题的复杂性的出货增量。

我的主要工具是考虑你项目中最危险的假设。关于这个话题，我最喜欢的帖子之一是 MVP 已死。老鼠万岁。这篇文章来自精益创业世界，在这个世界里，风险主要在于寻找产品与市场的契合度。但是它的原理同样适用于像 Vertigo 这样的大型工程项目。您需要一些东西来突出您的焦点，并确定在第一个版本中应该是什么——什么是现在有价值的，什么是低风险的，足以推到以后的里程碑。

这在实践中是如何运作的？让我们看看在迁移第一个客户的过程中遇到的三个有风险的假设。

**许多东西放在一起**

除了在 Confluence +吉拉平台上进行大规模重新架构之外，我们还构建了 15 项新的服务来处理诸如供应客户、访问客户元数据、分布式调度、入站和出站电子邮件以及全新的用户认证平台等事务。这些服务中的每一项都有团队在工作。在高耦合服务并行开发的环境中，最大的风险之一是集成出错。API 可以孤立地漂移，当你把它们放在一起时，它们就不起作用了。然后你有一轮又一轮的 bug 修复，这就推迟了程序的整体交付日期。

为了减轻这一点，从创业世界吸取另一个教训:建立一个一次性的原型。在我们的例子中，它是一个集成所有这些服务的应用程序，拥有者的工作是达到集成里程碑。这个原型在我们迁移第一个客户之前 6 个月就完成了。

那不是零努力。它要求团队改变他们的优先级来致力于集成里程碑。也有来自开发团队的阻力。如果你的头脑在你自己的工作筒仓中，集成里程碑会让你感觉在筒仓中慢下来。此外，还要协调额外的工作，还要编写额外的代码，这些代码永远不会交付给客户。将围绕“集成崩溃”的不可避免的风险提前六个月，给了项目动力、信心和更多的时间来考虑下面描述的更大的风险！

**数据泄露**

Confluence 和吉拉分别是 14 岁和 16 岁。这么多年来，他们一直假设系统中只有一个租户。将单租户系统转变为多租户系统时，最大的风险是您会在客户之间泄露数据。这种影响是灾难性的，尤其是对于像 Confluence 和吉拉这样拥有高度敏感数据的系统。在内部，我们使用“公司终结事件”来描述如果一个严重的数据泄漏错误被发布到生产环境中会发生什么。或者，我们会说“这让我们上了黑客新闻的头版”——但不是出于正确的原因。

可能出错的范围非常广泛。考虑一下，包含租用数据的长期内存中的任何内容(例如，在 Java 世界中，静态成员或单例成员)保留 16 年是完全可以接受的，但是在多租户世界中会导致严重的错误。我们的一名开发人员成为了多租户数据泄漏的负责人，并承担了开发策略的工作，以主动发现多租户违规，这样当我们迁移第一批客户时就不会出现任何潜在问题。

为了给我们信心，我们使用了几种技术的组合，包括静态代码分析、运行时内存分析(即，进行一次将已知字段插入 UI / API 端点的测试运行，然后在测试结束后搜索内存中这些字符串的存在)，以及字符串读/写的运行时跟踪。我们投入了大量的时间来构建和运行工具，以获得高度的信心。我们肯定进入了几个产生低信噪比的兔子洞，或者太多的假阳性而没有真正的用处。

但最后，还是解决了。自从迁移第一个客户两年后，以及 100%迁移一年后，我们已经避开了黑客新闻的恶名。

**我们可能会 f# & k 第一个客户**

在引入许多新服务的情况下，在两块巨石上进行大规模的重新架构是一项高风险的开发活动。一旦我们对零数据泄漏有了很高的信心，降低这种风险的最关键的事情就是让一小部分客户尽早使用这个新平台。作为整个项目的所有者，我的工作是尽可能地阻止范围蔓延。Confluence 和吉拉的首批客户于 2016 年 12 月同时迁移。第一版不支持任何第三方插件，不支持任何基于 Confluence 或吉拉的扩展(如吉拉服务台或 Confluence Questions)，也不支持任何完整的站点导入/导出。它支持不到 50%的 JQL 语法，并且只能在非常低的比例下运行。

现在，我们需要在不违背我们公司价值观“不要拒绝客户”的情况下，让最初的几个客户加入平台。我们研究了我们的客户正在使用哪些功能、JQL 语法和附加组件、它们的性能特征、用户生成的数据量以及使用模式，以找到符合这些特征且不影响其用户体验的客户。我们选择了与我们合作了很长时间的客户——理由是，使用一个产品超过三年且没有显著扩展(或开始使用高级功能，或采用新的附加产品)的客户比新客户更不可能突然开始这样做。

我们添加了警报，让我们知道这些客户是否遇到了任何未实现的功能，并建立了一个单键“反向迁移”，如果他们触发了这些警报，就可以立即返回到旧平台。再说一次，这不是免费的。但是让第一批顾客过来，向我们自己和整个公司证明这是正确的，这是值得的。

我们在 2016 年 12 月迁移了 22 个客户，历史表明我们选择得很好。没有一个需要迁移回旧平台。

“增量地进行”或者“在迭代中交付工作软件”说起来很容易。在任何足够复杂的问题中，很难计算出最有价值的下一个增量是什么。我们使用风险最大的假设规则来指导我们。这是一个持续的焦点。当你推翻了第一个风险最大的假设后，下一个可能不会马上显现出来。对于领导者来说，这需要不断的警惕，有时还需要艰难的对话，让你的团队不断地问“最大的风险是什么”，而不是“接下来最容易运送的是什么”。

## 工程原则#3:循序渐进，即使会受伤

上述“全进”方法的一种表述方式是“尽可能快地运行”到最终状态。但是“明智”是什么意思呢？为了解释这一点，我将深入探讨新旧平台之间的架构差异。请原谅我。

这里的工作计划是将单租户系统变成多租户系统。我们还使用术语“零关联性”，表示应用程序群集中的计算节点从不与任何特定客户绑定(即具有关联性)，这意味着所有计算节点都可以服务于任何客户请求。从单租户到多租户和零关联基本上是一个从应用程序中提取每一个与租户相关的状态并将其外部化的过程。

在某些时候，您需要将客户从旧平台迁移到新平台。这里最危险也是最不明智的策略是将所有数据移动，并将所有代码从旧代码更改为新代码，作为大规模迁移的一部分。对我们来说，“明智”的做法是在旧平台上进行尽可能多的小的、增量的更改，这样当我们到达最后一步(实际的迁移)时，新旧系统之间的差异已经很小了。

整个项目中增量迁移的一些示例如下:

**文件和附件**–从本地文件系统移动到外部存储。我们已经在将它外部化的过程中，以便为客户提供更高的灾难恢复缓解，因此该计划提出了迁移的紧迫性。
**身份和用户群**—将身份验证和用户群管理转移到一个外部系统。再一次，这已经在整个亚特兰蒂斯进行了，这个项目使它变得更加紧迫。
**搜索**——从本地 Apache Lucene 转移到外部 Elasticsearch。
本地缓存–从可变的本地状态转移到外部缓存(或者，移除缓存并优化数据访问)。
**生态系统**–Atlassian 附加组件拥有完全不同的服务器和云架构。我们与一些成功的供应商合作，允许他们向云客户提供他们的服务器插件。Vertigo 需要与他们合作，以过渡到我们的云附加架构。

所有这些功能都是新平台所需要的，但却是在旧平台上实现的，客户数据在特定租户最终迁移之前就已迁移。因此，在将客户从旧平台迁移到新平台时，所有需要外部化的数据都已经外部化了。所有的编码模式都被改变、发布和优化。我们将新旧平台之间的功能切换保持在最低限度。唯一要移动的是主数据库，在迁移后有一些配置更改。

话虽如此，在第一次和最后一次客户迁移之间的一年中，我们在外部化方面走了捷径。我们需要将自动化作业迁移到一个新的平台，但觉得耦合到最终迁移的风险足够低…然后调度程序遇到了性能问题，当我们开始大规模迁移时，不得不暂停迁移。我们还在最终迁移期间更改了系统时区(旧架构将系统时区设置为客户的本地时区)——同样，我们认为这是低风险的，但在早期遇到了错误。

回过头来看，我希望我们已经完成了避开调度程序怪癖的工作，并将调度程序迁移从最终的转换中分离出来。我希望我们在迁移之前就改变了旧系统上的时区。如果你在这种情况下，你有一个你相信的关键原则，比如“最小化新旧之间的差异”，10 次中有 9 次不要遵循它。把电话拨到 11。

## 工程原则#4:冲刺到 100%

我一生中做过几个大的迁移项目。我见过不止一个迁移项目，你完成了大部分的工作，而最后的 1%变得如此困难，以至于你又要一年才能完全完成。通常是一系列小的不同的问题。我们称它们为“雪花”,因为每一片都是独一无二的，嗯，以它自己的方式“美丽”。

只要我们在旧平台上有一个客户，我们仍然需要保持代码路径、构建管道、测试和 CI、部署管道以及客户供应基础设施的正常运行。这对每个融合和吉拉云开发者来说都是一个拖累。在停止部署到旧平台之前，我们不能容忍有大量棘手的问题需要解决。我们需要尽可能快地运行，直到工作完成 100.0%。没有几乎完成，没有接近 100%，但完全完成。

在实践中，这意味着我们在项目的早期处理和清除雪花时非常自律。我们花时间审核旧平台上客户的所有配置:系统配置和内部 Atlassian 插件配置在旧架构上可能因客户而异，但在新架构上需要保持一致。即使是 Confluence 或吉拉的部署版本也有 99.9%的一致性，但可能在 0.1%的异常值上有很大差异。我们花了几个月的时间集中清理旧平台的周边部分，例如，解决我们的采购系统和基础设施中相应的租户管理系统之间的不一致，以确保没有任何隐藏的租户会再次推出迁移的尾巴。

这与上述专注于追逐最危险假设的建议有冲突吗？是啊！我们在内部就这个话题进行了激烈的辩论。假设“我们可以在几天内，而不是几个月内，完成最后的 1%”本身就是有风险的。在最初的几个客户证明了这个平台之后，这显然是最大的风险之一。你需要尽早开始在这方面投资，以便掌握主动权。

这也是团队的动力。到最后一个客户迁移的时候，有些人已经在这上面工作了两年半。如果你曾经经历过这种情况，你就会知道保持高动力是多么的重要(和困难)。知道结束实际上是结束，而不仅仅是等待推翻更多问题的另一块石头，是开发和领导团队的强大动力。

清除雪花的投资得到了回报。在第一个客户之后，我们开始攻击那些雪花，同时为所有客户实现剩余的功能和性能工作。当我们到达迁移的最后阶段——要迁移的最大、最棘手的客户——时，我们 100%摆脱了雪花。对于团队来说，知道一旦他们解决了那些大客户的工作，我们就 100%在那里，这是一种解放和激励。没有长尾，没有要解析的奇怪配置。你完了。

## 终于

如果你在云世界中，你有快乐的和增长的客户，那么你需要不断改进，改造和优化你的系统。大多数时候，这种改进会涉及到某种级别(从小到大)的架构和数据迁移。

我们已经尽力让这个帖子尽可能的公开和公正。我希望这里概述的工程原理和经验教训能帮助你驾驭接下来出现的任何令人眩晕的项目！