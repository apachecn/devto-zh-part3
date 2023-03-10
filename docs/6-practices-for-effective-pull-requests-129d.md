# 有效拉动需求的 6 个实践

> 原文：<https://dev.to/ph1/6-practices-for-effective-pull-requests-129d>

<sub>*本文原帖[在我的博客](https://blog.thepete.net/blog/2019/05/10/6-practices-for-effective-pull-requests/)*</sub>

我曾经以为代码评审就是抓 bug，但是我错了。

在这篇文章中，我将讨论团队从代码审查*中获得的超过*缺陷检测的额外价值，以及*拉请求* (PRs)是如何最大化该价值的。理解了基于 PR 的工作流的缺点，我们将会发现一些简单的实践来防止 pull 请求降低团队的效率。

在过去的几年中，我所工作过的几乎每个团队都在使用某种版本的[*Github Flow*](https://guides.github.com/introduction/flow/)——一种代码开发分支工作流，开发人员在相对短暂的特性分支上工作，并在变更被合并到共享集成分支(通常是`master`，有时是`develop`)之前，使用 pull 请求向团队的其他成员请求对该分支上的变更进行代码审查。

这种方法对许多团队来说非常有效——这是一种直接的分支策略，它提供了到共享分支的频繁集成，通过合并前的代码审查增加了一些控制和严格性。然而，Github Flow 并不是没有缺点，特别是当被教条地遵循时。

## 更好的基于 PR 的代码评审实践

在本文的后面，我们将更深入地探讨 Github 流的一些优点和缺点——特别是围绕基于拉请求的代码审查——目的是确定允许我们在这个模型中最有效地工作的实践。下面先睹为快，看看其中的一些想法:

不要将开发任务与拉取请求一对一地联系起来。可以将一个特性的开发拆分到多个拉取请求中。创建与特定任务无关的拉请求也是可以的。

使用命名约定来明确地对代码评审反馈进行分类。PR 提交者应该知道哪些代码评审意见指出了必须解决的问题，哪些只是风格上的反馈，没有歧义。

使用工具和惯例来减少乏味的争论。来回讨论大括号前后是否应该有空格是一种极低价值的活动。选择一个约定，并在变更进入代码评审之前，用解决这些风格问题的工具来执行它。

**不要*要求*代码审查。**震惊！恐怖！也许两行拷贝的改变或者对一些简单的、容易理解的逻辑的小的更新可以在代码被合并之后*被审查？*

**在编码之前，而不是之后，讨论整体技术途径*。***没有人喜欢这样的代码审查，其结论是整个分支需要重做，因为方法是错误的。

用内联反馈增加异步代码审查。像 mob 编码和结对编程这样的实践提供了一个*疯狂的*紧密的反馈循环，相比于拉请求——几秒对几天。

先不说预览，让我们先来看看为什么拉请求模型变得如此流行。

## 拉取请求中的点

为了理解这些实践的动机和好处，让我们首先更详细地看看为什么拉请求首先是有价值的。

在 Github 流中使用拉请求作为编排代码审查的机制。但是为什么我们需要代码审查呢？它提供了什么价值？

### 代码审查捕捉 bug

观察一组代码变化的额外大脑可以发现原作者遗漏的错误或缺陷。有一段时间，我认为这是代码审查的基本要点——捕捉 bugs 但是随着时间的推移，我开始意识到代码审查实际上提供了其他的好处，这与缺陷检测一样有价值，如果不是更有价值的话。

### 代码审查强制执行规范

为了说明这些其他的好处，让我们来看看一些代码评审注释:

> *“不必要的空白”*
> 
> *“我们应该对常量名称全部使用大写字母”*
> 
> *“生活在一个控制器类中感觉太有逻辑性了”*
> 
> *“我不确定我们是否应该在客户端上做这项工作”*

这些注释中的反馈在范围上有很大不同，从小的编码风格细节——换行应该在方法调用之前还是之后——到大的架构决策——一些逻辑应该在客户机上还是在服务器上。然而，尽管这些评论的范围变化很大，但它们都遵循一个共同的主题:指出偏离团队商定的规范。这些问题本身并不构成错误或缺陷，但它们确实代表了应该解决的重要反馈。事实证明，保持代码库(和团队)围绕编码和设计约定保持一致是代码评审的一个关键好处。

### 代码评审进化出规范

这里还有一些代码评审的例子，展示了另一类反馈:

> 哦，好极了，这个查找表比我们之前的 switch 语句要简洁得多
> 
> *“嗯，我不确定是否要在这里进行验证，但现在我想起来，这更有意义了”*
> 
> *“我们真的需要决定是让这些常量位于中央文件还是位于引用它们的文件的顶部”*

这些评论引起了讨论。他们没有强制执行现有的编码约定，而是提供了发展或调整这些约定的机会。这是我从代码审查中看到的第三个好处——公共关系评论可以作为一种“城镇广场”,一个团队讨论新方法和新规范的共享空间。

## 拉式请求的弊端

我们已经看到，拉请求可以作为引入代码评审价值的渠道，但是它们也带来了一些缺点。

### 合并延迟

PR 遭受两种类型的延迟。创建 PR 后，它可以等待工程师找到时间开始审查代码。然后，一旦评审开始，可能会有一段时间来来回回地发布评审意见，进行更改，并重新评审代码。如果一个团队分布在不同的时区，或者有特别严格的评审文化，那么这种来回可能会拖上几个小时、几天甚至几周！

这种延误的代价非常昂贵。先说为什么。首先，PR 中的变更在等待批准时会变得陈旧。昂贵的合并冲突的机会增加了，因为同时其他的变更继续落在集成分支上。更重要的是，这种代码审查延迟代表了已完成的工作进入生产并实际向用户交付价值的延迟。在精益的思维方式中，[这种延迟代表浪费](https://dzone.com/articles/waste-5-delays)。

在一个高绩效的团队中，这种延迟可能会增加几个小时，但正如我刚才提到的，我见过这样的团队，在最终被接受和合并之前，一个 PR 可能会花上几天甚至几周的时间进行审查。这是一笔极其昂贵的开销。

### 关注错误的事情

PRs 鼓励的离线代码审查会出现一种奇怪的现象。评审者将花费大量的时间在相对次要的问题——空白、命名约定等等——的反馈上，而同时却不能对 PR 中更严重的设计或架构问题提供任何反馈。

这是为什么呢？我怀疑 pull 请求提供的面向行的注释机制是部分原因，因为它将审阅者推向低级的、面向行的反馈，而不是高级的设计反馈，后者不一定映射到 PR 变更集中的一组特定行。

除了评审反馈机制，还有很多人类心理学在起作用。一个评论者可能会不舒服地建议，已经使用的设计方法是错误的，整个公关应该返工。人们很容易屈服于[沉没成本谬误](https://en.wikipedia.org/wiki/Sunk_cost)，并且不愿意建议“抛弃”现有的实现，尤其是如果它功能正常的话。告诉和你一起工作的另一个人他们做错了，需要从头开始，这也是一件很困难的事情。因此，有时我们会犹豫不决，没有给出我们应该给出的重要反馈。

基于 PR 的代码评审的异步、文本性质也使得提交者很难衡量每个评论的相对权重。一个评论说“命名似乎令人困惑”是否意味着命名必须在 PR 合并之前得到改进，或者我们应该在未来的 PR 中修复它，或者下次我们在代码的这个区域时，或者只是评论者只是顺便评论命名似乎不是特别清楚？

基于 PR 的代码评审的本质也使得某些问题在评审过程中难以发现。爬行代码复制就是一个很好的例子。当主要通过*改变了什么*的角度来执行代码审查时，您可能不会发现一个新的类实际上与复制粘贴而来的其他四个类有 70%相同，或者一个新的单元测试与一些现有的测试极其相似。这是因为当评审一个关注于什么被改变的差异时，现有的代码不容易被看到。

### PRs 耦合到分支

使用 Github Flow，我们定义了我们希望使用分支进行代码审查的变更集。这就是拉请求机制的工作方式——您将一个*分支*标记为准备审查。作为一个单元，在一个分支上的变更集和将要被评审的变更之间总是有直接的对应关系。这种耦合意味着，如果一个特性分支包含大量的变更，那么所有的变更都必须在一次大的代码评审中处理。将变更分解成几个更小的评审*是可能的*，但前提是作者能够将变更分解成一系列独立的特性分支，这些分支可以一个接一个地集成到共享分支中。碰巧的是，这种更加[的基于主干的方法](https://trunkbaseddevelopment.com/)有很多其他的好处，但是它需要思考和小心，并且这样做所需要的工具和实践并不是每个开发团队都容易得到的。

这意味着对于许多团队来说，一个复杂的特性意味着一个大的特性分支，这意味着一个大的代码审查变更集。[研究](https://link.springer.com/article/10.1186/s40411-018-0058-0) [指出](https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/)大型变更集的代码评审效率较低，但不幸的是，拉请求所基于的面向分支的模型使得很难将大型特性的评审划分为较小的块。

## 更好的基于 PR 的代码评审技术

我们已经看到了代码审查的重要作用，Github 流使用拉请求作为代码审查的机制。但是我们也看到了基于 PR 的代码审查的缺点。因此，我们想知道如何最大化 PRs 的好处，同时最小化其缺点，我们又回到了我在本文开始时概述的有效拉取请求的实践。

### 使用快速跟随 PRs

PRs 的一个缺点是它们在合并变更时引入了延迟。我们可以通过不要求在代码评审期间提出的每个问题都在**相同的** PR 中解决来减少延迟。相反，我们可以选择在同一个 PR 中只修复关键的、阻碍合并的问题，并在*快速跟进* PR 中修复其他非关键问题。这允许 PR 提交者以更少的延迟合并他们的变更，同时仍然解决代码评审中提出的所有问题。

这种方法需要一定的纪律和团队内部的信任。公共关系提交者必须遵守纪律，在快速跟进的公共关系中履行解决问题的承诺，审查者必须相信他们会这样做。然而，我要说的是，这种纪律和信任是在一个健康的团队中工作的专业开发人员的合理期望。如果工程师不信任其他工程师像成年人一样行事，那么团队就有比他们的拉请求约定更紧迫的问题需要关注。

### 使用符号对代码评审意见进行分类

我们在前面看到，很难理解代码评审注释是识别一个必须修复的缺陷，还是简单地偏离约定。如果我们计划只修复原始 PR 中的关键问题，那么我们需要一个明确的方法来区分关键和非关键缺陷。

为了消除这种模糊性，我鼓励团队在他们的代码评审注释中采用简单的符号:

> *“拦截器:该 API 在移动浏览器上不可用，会导致手机爆炸”*
> 
> *“快速跟进:我们真的不应该在控制器动作中使用这种业务逻辑——请将它放到一个域类中”*
> 
> *“NIT:我可能会把它放到一个小的帮助函数中”*

希望这种符号是不言自明的。在合并此 PR 之前，必须解决**阻塞**。必须解决**快速跟进**，但可选择通过跟进 PR 解决。一个 **NIT** 是作者可以接受或离开的反馈，或者是一个就团队规范展开讨论的机会。

这个符号不需要被教条地遵循。例如，如果一个评论者想留下一个评论，简单地称赞一个设计方法或实现细节，他们应该自由地这样做。我合作过的许多团队会从代码审查中多一点积极的反馈以及建设性的批评中受益！

### 使用工具强制执行编码约定

工程师在代码评审中讨论标点符号的位置是实施团队规范的一种非常昂贵的方式。在某种 CI/CD 基础设施中运行的工具是一种更加有效和一致的方法。利用静态分析工具，如 linters、代码格式化程序等。自动执行团队标准，并保存代码评审意见，以便在机器不能(现在还不能)进行的更微妙的主题上进行有意义的讨论。)检测与团队约定规范的偏差。

### 将开发任务分解成多个拉取请求

当评审变更集太大时，代码评审根本不能交付太多的价值。将一项任务分解成多个更小的 PRs 会产生更高质量的评审反馈。此外，通过将一个开发任务分解成多个 PRs，我们减少了延迟的成本，因为所需的变更可以被增量地评审和合并，而不是必须等待*每件事情*完全完成之后才能开始评审*任何事情*。

有两种方法可以将我们的变更分解到多个 pr 中。首先，我们可以使用基于主干的开发技术，比如特性标记，它允许我们在一个完整的特性完成之前，增量地将代码变更交付给一个共享的集成分支。

第二，我们可以使用*快速跟进 PR*来解决代码评审中提出的非关键问题，而不是要求所有的变更都针对原始 PR。

### 鼓励“露营地”公关

童子军有一个规定，要把露营地打扫得比你发现的还要干净。这个应用于软件的“营地规则”[，也许是实现常青代码库的唯一最佳技巧；一个不会随着年龄的增长而退化成一堆不可行的意大利面条的代码库。不幸的是，当被教条地遵循时，Github Flow 往往会阻碍露营地规则，因为开发人员被鼓励将他们分支中的变更集中在手头的功能上，以便减少最终代码审查的规模，以及避免作为“露营地清理”的一部分所做的不相关变更的潜在干扰。一个折中的办法是在小的、独立的 pr-"露营地" pr 中建立一个团队文化来处理这种类型的](https://www.oreilly.com/library/view/97-things-every/9780596809515/ch08.html)[机会主义重构](https://martinfowler.com/bliki/OpportunisticRefactoring.html)。这需要一点纪律，并愿意学习必要的 git-fu，以便在遇到清理机会时安全地将您的功能分支放到一边并创建一个新的分支，但这是一个团队习惯，将在您的代码库的长期生命中带来美妙的回报。

### 尽早并经常审查 PRs 之外的技术方法

我们已经看到，从比技术实现更广的角度来看，代码审查并不是一个确保变更合适的好机制。这种形式不适合这种类型的讨论，而且从根本上说，反馈来得太晚了——在那些决策的实施完成之后。

对于这些更广泛的技术讨论，有更好的论坛。团队可以建立“故事启动”，在开发开始前，某个功能的开发人员与技术负责人和产品人员一起讨论细节。

此外，随着功能开发的进行，为轻量级接触点设置各种机会允许技术方向根据新信息进行改进——一旦我们遇到敌人，大多数好的计划都会受益于一些调整。我个人喜欢建立“技术秘密会议”(tech huddles)<sup id="fnref1">[1](#fn1)</sup>，这是一个非常简短的以技术为导向的聊天，在每天的会议结束后，团队成员可以提出前一天出现的任何有趣的技术话题。

即使一个特性已经完成并准备好接受评审，团队也不应该依赖拉请求作为进行代码评审的唯一方式。拍拍某人的肩膀(或快速分享一个屏幕)并进行面对面的评论，比在网络浏览器中的文本框提供了更高带宽的讨论。您可以将此提升到下一个级别，并执行“mob 评审”，将团队的全部或部分成员聚集在监视器、投影仪或屏幕共享前，作为一个小组来执行代码评审。虽然这对于受益于额外的大脑的关键变更有价值，但是这也是最大化代码评审的第三个好处——演进团队规范的机会的好方法。让团队一起讨论新方法的利弊比通过拉式请求评论来讨论想法更有成效，也更具包容性。

## 削弱 PRs 的重要性

本文一开始，我讨论了拉请求的好处。然而，我们所看到的是 PRs 根本没有提供多少*内在*价值——它们主要作为代码审查的有用机制。代码审查提供了真正的价值。此外，我们已经看到，虽然 PRs 是获得代码评审价值的一种方式，但还有其他机制，教条地遵循 Github 流程的团队可能会错过这些机制。

像 mob 编码、结对编程和良好的老式团队讨论这样的实践，可以提供一个带宽更高的替代方案来进行基于请求的代码审查。

此外，当我们使用 PRs 作为代码审查的组织结构时，我们不需要强迫该结构与我们的开发任务 1:1 地保持一致。我们可以选择将开发工作划分到多个 pr 上，减少异步预合并代码审查可能导致的昂贵的延迟。

感谢阅读。我希望你很快会兴奋地在你的团队中尝试这些想法！

* * *

1.  我是科技聚会的忠实粉丝，部分原因是我喜欢匆忙地称之为科技拥抱，看看人们需要多长时间才能理解。一些团队已经“接受”了这种替代术语。 [↩](#fnref1)