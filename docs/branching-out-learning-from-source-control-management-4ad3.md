# 拓展业务。从源代码管理中学习

> 原文：<https://dev.to/pcloudy/branching-out-learning-from-source-control-management-4ad3>

源代码控制是任何 DevOps 设置的基础。事实上，源代码控制也是任何软件开发工作的支柱。不是为什么 SCM 的问题，是哪个的问题？

下一个问题是如何最好地利用它。作为一家几年前从很少的开发人员起步的公司，我们有一套非常适合当时情况的流程。现在有了大约 30 个开发人员，我们发现自己很快就超越了同样的理想过程。这反映在许多糟糕的部署(幸运的是，只是在产品化阶段)、覆盖代码和合并冲突的实例中。然后我们开始调整我们的流程，在适当的时候，我们的效率提高了许多倍。

这篇文章记录了一个旅程，从一个使用简单策略的小团队到现在的系统，在这个系统中，所有这些人都在一起工作，而不会踩到彼此的隐喻脚趾。它还详细描述了我们建立的分支策略，并深入研究了我们如何围绕新的分支模型创建测试和开发运维流程。

**过于简单的概述**

在我开始解释我们以前和现在的流程以及 git 策略之前，我想向您展示一个客户对 pCloudy 的看法，仅供参考。

[![](img/7170dfc4edcd79b90245b952cc2c8a55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N1RyorMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2018/10/pcloudy-access-devices-remotely.jpg)

在一个非常高的层面上，pCloudy 是一个基于云的 SAAS，它托管物理手机和平板电脑，您可以通过浏览器访问它们，并可以用于您的移动应用测试需求。

下面是我们云架构的一个非常基本的观点。

[![](img/93f0fc19f232b6636b631f14d5dfb3ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rOJ3zEXt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2018/10/device-user-facing-services.jpg)

我们有一堆运行在云上的服务，我们称之为 pBox (pCloudy 的服务盒子)。我们还有一些物理机器，我们云上的手机和平板电脑都连接到这些机器上。我们称它们为盒子(远程盒子，这是一个实际的物理盒子)。请注意，这是一个过于简单化的观点。我在这里掩盖了一些复杂性，过分简化了一些细节，否则我将在接下来的几篇博客中只解释这一点。但是这种更简单的表示将帮助您更好地理解我们的整体云。

在上面提到的架构中，我们有一个开发云和一个暂存云。这些分别是我们内部开发和测试用的。这两种云都是生产公共云的副本。差别大多只是在规模上。

**出牙问题**

现在您已经对我们的流程和架构有了基本的了解，让我们更深入地了解一下。我将首先解释当我们是一个小公司并且只有很少的开发人员时我们所遵循的过程。那时，几乎所有的开发人员都独立开发不同的软件组件。每个组件在 git 中都有一个 repo。因此，每次回购通常只有一个人处理代码。这是一个非常好的场景，因为每个开发人员都知道他们的回购中发生的所有变化，并且在大多数情况下，你可以肯定，一旦你在你的机器上进行测试，你的变化将在生产中工作，但我并不提倡直接部署到生产中。那是一个更简单的时代，我们的设置也更简单。那时，关于源代码控制的唯一策略是我们需要使用一个。主要是因为没有必要更加复杂。(以及由此产生的复杂性)。

但是随着员工人数的增加，我们遇到了问题。能够不断增加员工数量是成功的一个标志，但这也带来了一系列问题。主要的一个是管理代码从开发人员的计算机到云的旅程。我们的主要架构块和软件组件或多或少保持不变，但大多数组件现在有多人在工作，其中一个通常是新员工。这个人不仅是团队的新成员，而且对他们加入之前我们所遵循的流程的细微差别一无所知。更多的人也意味着更多的失败。

那我们做了什么？我们做了一些小调整。继续做那些调整。从未停止。

**进化超过革命**

那么，为什么要做小小的调整呢？为什么没有更大的改变呢？作为一个哲学问题，我们的方法是避免运行系统中破坏性的大爆炸变化，除非它是不可避免的。

我们很早就意识到，在别处行得通的复制粘贴工作在这里可能行不通。每个公司都不一样。每个团队工作的限制也是如此。我并不是说我们的问题更加困难，只是它们不同而已。出于类似的原因，我们不会仅仅因为某个过程在其他地方显示出结果就直接开始实施它。在本系列中，我将给出一些例子。但现在，很明显，虽然我们不会重新发明车轮，但我们也不会不加思索地将别人的车轮安装到我们的汽车上。

像许多初创公司一样，当我们开始时，我们的目标是做出我们能做出的最棒的产品，而不是成为敏捷/DevOps 冠军。在我看来，从其他目标出发既不明智，也不会让我们走得更远。因为产品是我们存在的理由。此外，虽然有很多关于敏捷和良好开发运维的数据和材料，但真正重要的是你如何采用每个原则，并以这样一种方式为你的团队实现它，即你的团队和企业的生产力比实现它之前更高。如果不满足这个条件，那么要么您没有正确地完成您的实现，要么您的团队还没有为此做好准备。

所以我们做了些小调整？很多都是随着时间的推移。直到需求得到满足。如果我们的需求发生变化，我们将继续这样做。

**我们的调整**

我们的大部分调整集中在以下几个方面。

Git 分支模型。
批准代码审查。
将分支映射到环境。

**现在让我详细解释一下**

正如我之前说过的，我们没有任何标准的分支模型。由于工作的人越来越少，部署也不是什么麻烦事。但是随着我们团队力量的增强，问题开始出现。尤其是当不止一个开发人员在同一个 repo 中处理同一个文件时。

我们看到的主要问题是开发人员无意中覆盖了其他开发人员编写的代码。这通常是因为有人将他们的代码推入回购，而没有首先检查其他人是否也做了同样的事情。这是人的问题。很容易犯这个错误。人类会忘记事情。简而言之，在将代码推入源代码控制之前，我们试验了一个供开发人员遵循的清单。但是结果并不是决定性的。

所以我们转向了。我们强迫我们的 git 分支模型标准化。这种变化不仅仅是一个简单的调整。这是一个相对较大的变化，但我们继续前进，因为这是不可避免的。之前缺乏标准流程可能会在未来对我们造成伤害。两年后的今天，这看起来是一个显而易见的事情。但是在最终选择 git-flow 之前，我们确实花了很多时间考虑这个问题。我们由此导出了我们的分支模型。你可以在这里、这里和这里找到模型的详细说明。但是下面让我总结一下什么是 git flow，以及我们为什么选择它。

**去流**

这张图很好地概括了 git-flow 的样子。

[![](img/09de141fe18295c4c734ac490ca145ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gXOuisnj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2018/10/git-flow-branching.jpg)

从本质上说，Git flow 提供的是我所说的代码“分阶段提升”。您有不同的 git 分支(通常是主分支、开发分支和多特性分支)。根据您的特定特性所处的开发阶段，您的代码将驻留在不同的分支中。您的特性(或者 bug 修复，或者增强)开始于特性分支，一个专门为该特性创建的分支。开发人员在那里进行开发，一旦他们对特性的完成感到满意，就会通过一个拉请求将代码推送给开发人员。然后对拉取请求进行审查和合并。这实现了一个重要的目标。当您将代码推送到 any 分支时，您是通过 pull 请求来完成的，这会强制进行代码审查。没有它，你的工作就无法进展。在我看来，这是基于“拉式请求”流程的最大优势。您可以根据需要进行任意多的修改，唯一需要注意的是，您的代码是从 git 部署的，而不是通过随机复制或修改单个文件。在这个分支中，来自所有其他开发人员的代码被集成在一起，并进行验证。一旦通过验证，代码就被部署并推送到主服务器。

这允许对代码进行逐阶段的验证，在每个阶段完成后，代码得到提升。另外，因为提升代码需要拉请求，所以代码评审被构建到这个过程中。

**随着我们的方法流动**

我们的流程源自 git-flow，但并不完全相同。我们有更多的分支，(特性、开发、登台、主)或多或少地确保我们的开发和登台云被正确使用。在我们的例子中，开发从一个特性分支开始，当开发人员对他在机器上的工作感到满意时，他向开发分支发送一个拉请求。一旦 pull 请求被批准，代码就会从 Dev 分支部署到 Dev Cloud 上。

一旦您看到您的代码工作正常，您就以同样的方式将它推送到测试分支，以便对设置进行正式的验证。测试分支上的代码被部署在阶段环境中。在这里，您的代码在被声明为通过或失败之前得到广泛的测试。

如果通过，那么您就有权限向主服务器发送一个 pull 请求。master 中的代码与我们生产中的代码相对应。如果它失败了，那么它将返回到开发人员的开发分支或他的特性分支。开发人员修复代码，然后再次开始相同的过程。这确保了我们尽可能少地破坏代码，如果我们曾经这样做，那通常是因为过程没有被遵循。

现在下一个问题是在测试失败后，部署到测试分支和登台云的代码会发生什么。我们定期从 master 重置内部云。在这样的重置过程中，这个未提升的代码会被自动丢弃。这是至关重要的，否则您将在您的开发或登台环境中拥有您从未打算推向生产的代码。

这就是我们的 Git 分支策略。但在我结束这篇博客之前，我想留下一些我们在完善 devops 的这一部分时获得的其他见解。

**bInsights**

永远不要让一个人去寻找不匹配的括号:我已经记不清有多少次我们在试运行和有时生产中失败了，因为简单的错误，比如不匹配的括号，或者 json 结构中缺少逗号，或者一些看起来同样微不足道的事情。这让我们想知道为什么这么多代码审查会遗漏这样的东西。然后硬币落下，我们意识到这样的错误确实很难被肉眼看到。所以我们退而求其次。代码的静态分析器。在我们使用了一个之后，我们开始想没有它我们是怎么生活到现在的。市场上有数百种这样的产品。我们选择 CodeFactor，部分是因为它与 github 集成，你可以在你的 pull 请求中直接看到所有的缺陷，部分是因为它可以与许多语言兼容。

小就是美:至少对于签入和拉取请求，我们发现小而多的签入和拉取请求才是正确的选择。审查较小的增量要容易得多。如果出现问题，恢复起来也容易得多。此外，较小的更改更容易测试和验证。

代码评审最好是亲自进行:当然，github(和其他公司)给了你一个非常复杂的 UI，你可以在一个不同的视图中看到代码增量，并且允许你就代码和变更进行对话。但是我已经看到最好的评论发生在个人身上。面对面的时间是最好的，如果你有一个关于你的拉式请求的评论的问题，最好是直接走到有问题的人面前澄清，而不是懒洋洋地键入一行回复。在最好的情况下，你会收到另一个答复，可能会或可能不会完成澄清，在最坏的情况下，你会以一个漫长的来回链结束，之后你给我们，并有一个面对面的 conv。有时你的评论者甚至会忽略，因为他们有更紧迫的事情要做，在所有这些情况下，这是你的损失。

这就是我今天的全部内容。欢迎通过评论回复。我很乐意继续这个话题。请继续关注这个空间，我会写更多的博客，讲述我们在创业公司做 DevOps 时获得的见解和经验。