# 代码所有权的成本

> 原文：<https://dev.to/mrlarson2007/cost-of-code-ownership-3ekb>

如果你花时间买了一辆车，我敢肯定你已经做了调查，并问:这辆车要花多少钱？我会从制造商那里贷款买车，还是使用银行或信用合作社？这辆汽车的保险要花多少钱？维护它要花多少钱？弃用是什么？以及更多的问题。所有这些因素都会影响到拥有一辆汽车的总成本。

虽然我们可能会花很多时间来计算拥有一辆车的成本，但你有没有停下来想过，“我每天写的代码，花了我多少钱？我的团队要花多少钱？我为之写作的组织要花多少钱？”这些是我们必须面对的重要问题。如果你从总体上看这个行业，我们通常会看重神话般的“10 倍发展”，它可以绕过其他所有人。能够完成工作并编写大量代码的人。但是我们应该为此进行优化吗？为此奖励人们？

## 代码应被视为负债

艾德加尔·迪克斯特拉的一句名言提醒了我:

> 我今天的观点是，如果我们希望计算代码行数，我们不应该将它们视为“产生的行数”，而应该视为“消耗的行数”:当前的传统智慧是如此愚蠢，以至于将这些计算在账本的错误一侧。

他为什么这么说？我想到了一个原因，复杂性。随着我们编写越来越多的代码，事情变得越来越复杂。我们在绿地项目行业中经常看到这种情况。你有没有想过为什么创业公司可以把事情做好，然后绕着大公司跑？为什么在一个全新的项目中，每件事情都变得容易得多？原因是我们没有现有代码库的所有包袱。在我们写一行代码之前，我们的机会是无限的。没什么好理解的。没有什么可以整合的。没有什么可测试的。这是全新的，令人兴奋的！

快进几年，经常会发生什么？随着我们添加越来越多的代码，事情变得越来越难以理解。我们不能再在头脑中保留一个系统的工作模型。如果我们在项目中增加了新人，这只会增加复杂性。我们很多人，包括我自己，经常边走边学。这意味着我们可能甚至不知道如何编写代码来对抗这种复杂性，我们不小心让事情变得更糟。因此，即使是拥有最佳工程实践的熟练开发人员也面临着日益增长的复杂性，因为我们的系统随着我们编写的每一行代码而变得越来越大。

## 偶然的复杂性

这让我们想到了另一点，如果仅仅是添加代码的行为造成了复杂性，那么糟糕的代码会使情况变得更糟。丹诺斯曾这样说过:

> 我称之为错误二分法。我在其他地方说过，软件开发的目标是“可持续地最小化对业务影响的交付时间”。如果不认真对待工程学，你就无法可持续地做任何事情。糟糕的代码质量只是在短期内对用户不可见。
> —Daniel Terhorst-North(@ tastapod)2019 年 3 月 4 日
> 【https://twitter.com/tastapod/status/1102570605986103297 T2】

糟糕的代码质量不仅影响开发人员，还会影响业务。如果不通过良好的软件工程实践来管理和最小化复杂性，那么交付周期和吞吐量都会慢如蜗牛。随着添加功能的时间越来越长，曾经闪电般的快速启动逐渐停止。由于错过了所有的机会，这反过来又耗费了企业的资金。它还开始在组织中制造裂痕，因为经理们试图通过发布最后期限和最后通牒来“解决”问题。开发人员开始感到压力，想要离开这种危险的处境。随着开发商开始寻找更好的工作，这一行业遭受的损失甚至更大。这反过来创建了一个强大的反馈循环，要么是新的人被扔向问题，要么是团队被管理层强迫开始通过测试或其他确保质量的实践来偷工减料。少数留下来的有经验的人要么解决别人制造的混乱，要么培训所有的新人。

## 我们能做什么？

在这一点上看起来似乎是绝望的情况，随着我们添加更多的代码，事情只会变得更糟。但是我们可以做很多事情。下面是我们将要讨论的几件事情:

*   每个人都需要知道代码的状态
*   坚持软件工程最佳实践
*   不要为代码生产而优化，要关注结果
*   删除不再有价值的特性，删除死代码
*   需要平衡

### 每个人都需要知道代码的状态。

业务需要清楚地了解是什么塑造了代码的每个主要领域。在敏捷圈子里，特性总是被同等对待。功能 1 和功能 2 通常仅根据对业务的价值进行排名，但管理层通常不太了解添加这些功能的复杂性。这里需要清晰和开放的交流，我们需要清晰和可理解的方式将代码的每个主要领域的状态传达给业务。Michael Feather 在这次演讲中给出了一些很好的想法，关于如何将代码中的技术债务水平传达给企业[这里](https://youtu.be/7hL6g1aTGvo)。一个想法是，使用假设的特性，你将把它添加到代码库的每个主要区域，并随着时间的推移跟踪团队中开发人员的估计。随着时间的推移，当您更改系统时，您可以看到这些更改是如何影响评估的。

随着这种清晰的交流，开发人员和管理人员都需要认真审视一下为什么我们一开始就以一团糟告终？管理层是否给出了不切实际的最后期限？管理人员是否告诉开发人员开始偷工减料以完成工作？软件开发商是否给出了过于乐观的估计？归根结底，组织和团队文化对软件开发人员编写的代码有着强大的影响。如果你想做得更好，你必须有正确的文化！

### 坚持最佳工程实践

正如已经提到的，在进度压力下我们能做的最糟糕的事情就是开始偷工减料。经理们可能认为停止重构、减少单元测试以及其他类似的措施将有助于特性的淘汰。虽然这可能在很短的时间内奏效，但这样的决策会对项目产生持久的影响。马克·西曼说得再好不过了:

> 我也经常观察到经理们表示偏爱某个“把事情做好”的开发人员。
> 
> 那个开发人员经常做的是宣布一大堆任务要“完成”，让其他人来收拾他/她的烂摊子。
> —马克·西曼(@ ploeh)2019 年 3 月 3 日
> 【https://twitter.com/ploeh/status/1102301536443600897 T2】

答案是什么？测试驱动开发在这里非常重要。首先，它确保我们有一套好的单元测试。这反过来给了我们进行重构所需的安全网。我经常看到开发人员害怕接触代码的某些部分，并且从来不想重构它。为什么？它很难理解，很少或者没有单元测试，这使得它很容易被破坏。因此，我们必须遵守单元测试的纪律，我认为测试驱动开发对此有所帮助。因为我们将测试放在第一位，并且在进行更改时依赖于它们，这激励开发人员首先确保他们的代码是可测试的，其次确保测试是快速的。

但是为什么我们需要花这么多时间去重构呢？同样的原因，我们需要使用书面形式的草稿。通常一个东西的第一稿并不是我们最好的作品，在我们做出不仅更好，而且更容易理解的东西之前，往往需要很少的修改。我们的代码也是如此。通常初稿不是我们最好的作品，我们只是专注于让代码工作！现在我们需要时间让它变得可理解和可维护。

> 软件开发的生产率与你生产代码的速度没有多大关系。
> 
> 它与代码的总拥有成本有更密切的关系。
> 
> 有些代码，你可以在一个小时内写完，然后继续浪费几天或几个月的时间来维护和排除故障。
> —马克·西曼(@ ploeh)2019 年 3 月 1 日
> 【https://twitter.com/ploeh/status/1101442797436055552 T2】

记住 Mark Seemann 在这里所说的，仅仅因为我们能够快速地生产代码并不意味着我们想要或者应该这样做。我们需要时间来重构和修改代码。如果我们只是匆匆忙忙地完成事情，有一个很好的改变，那就是我们最终会创建没人理解的代码，增加复杂性，并为我们的队友创造更多的工作。

我们还必须考虑我们系统的架构。如果我们有一个小团队，那么让每个人都参与进来以降低复杂性可能是有意义的。如果我们有几个团队在工作，我们可能想要追求一个微服务架构，以允许团队专注于他们所拥有的系统的部分，并且有良好定义的契约来与其他团队所拥有的服务进行通信。看看你的情况，花点时间选择正确的解决方案，这将有助于最小化你所面临的复杂性。

最后要记住的一点是，关注你今天的需求，而不是未来可能发生的事情。我经常看到开发人员如此沉迷于让代码处理成千上万种不同的情况，而实际上我们现在只关心其中的一种。只编写解决当前问题所需的代码。不要试图预测可能永远不会到来的未来需求。如果你猜错了，你最终会得到很多只会让人迷惑的死代码。

### 不要为了代码生产而优化，关注结果

永远不要为代码生产而优化。为何如此强烈的声明？古德哈特定律指出:

> 一旦出于控制的目的而对任何观察到的统计规律性施加压力，它就会崩溃。
> [https://en.wikipedia.org/wiki/Goodhart%27s_law](https://en.wikipedia.org/wiki/Goodhart%27s_law)

这意味着，一个衡量标准，一旦被用来评估任何人的表现，最终都会变得毫无用处。原因是人们将开始优化这种测量。因此，如果我们奖励开发人员一天中产生的代码行数，或者他们提交的数量，这自然会鼓励他们产生更多的代码。他们会这样做，即使这会损害项目或破坏团队合作和结对。人们可能甚至没有意识到他们正在这样做。历史上充满了这样的例子。这也被称为眼镜蛇效应，如果你想知道英国人在印度尝试时发生了什么，你可以在这里听魔鬼经济学播客。

关注业务的结果，试图解决什么问题，并在发布软件时考虑衡量这些问题的方法。在一天结束的时候，我们试图帮助别人解决我们正在创建的软件的问题。如果你想朝着正确的方向开始，Jez Humble 在他的 Goto 会议演讲中给出了很多很好的想法。

### 删除不再带来价值的特性，删除死代码

我们不想成为代码囤积者，试图保留每一行代码，在我们的代码库中留下注释掉的代码。源代码管理是我们的朋友，如果我们以后需要它，我们可以取回这些代码。这里重要的是我们需要删除死代码。对每个人来说，代码越少，复杂性越低。

与此同时，我们需要对我们的应用程序的特性集进行长时间的关注。大多数团队的自然进展就是不断地添加新特性，但是每一个新特性都会增加代码的复杂性。请记住，我们多达 2/3 的功能要么不起作用，要么对业务产生负面影响。因此，这项业务也不应该保留功能。我们需要不断地思考和分析，“我需要哪些特性？我可以放心地摆脱哪个？”如果我们不这样做，我们最终会被没有人使用的功能所窒息，或者更糟的是让我们损失很多钱。

在这篇文章中，迈克尔·费瑟提出了一个激进的解决方案。代码应该会自动销毁，并在几个月后被删除。他认为这将迫使团队和企业真正考虑什么是重要的，以及如何只保留用户想要或需要的软件。

### 需要平衡

最后，我们必须记住这是软件工程。凡事都有取舍和平衡。两端都有极端。请记住，我们并不是在这里争论所有的复杂性都是不好的。我们可以用一个领域的复杂性来换取另一个领域的优势。关键是，我们不能让这些决定随机发生，或者对它们不加考虑。如果我们付诸实践来帮助我们管理复杂性，我们可以帮助我们自己和我们的团队制作更好的软件，并以更大的灵活性交付它。我希望这篇文章对你有用，并记住我们都需要担心代码的成本，我们可以管理它！