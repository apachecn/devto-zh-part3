# 给不懂 GitHub 的人的 GitHub 指南

> 原文：<https://dev.to/lberlin/a-github-guide-for-people-who-don-t-understand-github-n50>

几年前，我和 GitHub 有过一次尴尬的初次相遇。我没有从事过任何一种技术领域的工作，之前从未听说过 Git，从未使用过我的命令行，只想托管一个网站。虽然 GitHub 绝对不是最好的选择，但这是我一路摸索出来的，最终成功了。

然而，这种“最终”意味着我花了大量的时间(很容易想到十几个小时——就像我说的，这很尴尬)试图弄清楚 GitHub 到底是怎么回事，这远远超过了我为我的网站实际编写和测试代码所花的时间。它把 GitHub 变成了这种神秘的、神话般的野兽，尽管我在它面前变得更加自在，但我仍然隐约感到害怕。

[![alt text](img/797be5c21aeddba52ac6edb7f66e5843.png "The Beast Roars")](https://i.giphy.com/media/Fnz5oInGEe5yw/giphy.gif)

对于那些不了解 GitHub，被 GitHub 吓倒，不完全知道人们为什么或何时或如何使用 GitHub 的人来说，这个指南是给你的。我不是专家，但是我站在你的立场上，认为我们可以一起驯服这只野兽(至少一点点)。

* * *

### Git 和 GitHub 不一样

我早期的一个困惑是，我会看到对 Git 和 GitHub 的引用，我甚至没有想到这些是不同的东西。

简而言之:Git 是您的本地版本控制，一个可以用来创建和跟踪代码版本的工具，这样您就可以引用或恢复到以前的迭代。GitHub 是一种托管 Git 存储库(版本和连接文件的集合)的方式，因此您可以共享和协作您的代码。您可以在自己的计算机上使用 Git 进行所有的本地版本控制，而无需连接到互联网。但是，GitHub 允许你将这些版本的代码转移到云中，如果你愿意的话，并且已经开发了一些在相同项目中与其他人合作的技巧。

当你第一次开始创建一个存储库(你想要保存的所有文件的文件夹，也许有一天可以共享)时，你可以在本地使用 Git 或者在你的 GitHub 帐户上使用[来完成这个任务——任何对你有用的东西！](https://guides.github.com/activities/hello-world/#repository)

当你这样做的时候，我会给你两个指导:如果你在本地启动一个仓库，你需要[告诉 GitHub](https://help.github.com/en/articles/adding-an-existing-project-to-github-using-the-command-line) 这个仓库的存在，并允许它制作副本以保持在线。如果你在 GitHub 中创建了一个库，你需要[‘克隆’它](https://help.github.com/en/articles/which-remote-url-should-i-use#cloning-with-https-urls-recommended)以将它复制到你的本地机器上。

你怎么知道应该遵循上面的哪一个指南呢？如果你已经写好了代码，我会选择第一个选项。如果你刚刚开始，我认为在 GitHub 上创建库，然后“克隆”到本地工作会更容易一些。

* * *

### Git 中的步骤比你想象的要多

[![alt text](img/fb8123cc233b25ada013c80bf67c20bc.png "Never Ending Steps")](https://i.giphy.com/media/6csVEPEmHWhWg/giphy.gif)

首先，让我们看看 Git 中在您刚开始使用 GitHub 时有用的部分。为什么我需要“添加”，然后“提交”，然后“推送”我的代码？在处理较小的项目时，所有这些基本步骤看起来都是多余的，术语也不一定是直观的，所以让我们逐一分解。

我认为和 Git 一起工作就像和一出戏一起工作，所以当我试图让这个比喻起作用时，请原谅我。

当你在 Git 中“添加”一些东西时，你基本上是在后台准备一个演员去主舞台，但是这个演员仍然在幕后。你可以一蹴而就地准备好舞台上的一切，但也许你不想让一切都一次登台——有些演员还没有穿上戏服或准备好！“添加”的目的是告诉 Git 在这个场景中哪些文件将真正出现在舞台上，而其他演员还没有准备好，将在后面的场景中更有意义的时候出现。

当你在 Git 中“提交”一些东西时，你实际上是把那些准备好的演员(文件)移到了舞台上。这应该通过某种介绍来完成，演员上台时说的一句话，这样观众就能理解演员为什么会在那里——这是通过良好的“承诺”信息来传达的！

然而，承诺某事并不真的把舞台放在观众面前。当你在 Git 中“推”东西时，你正在把你写的场景，连同舞台上所有合适的演员，移入实际的戏剧中。如果你“推”到你的 GitHub 上，这意味着不仅要把场景添加到剧本中，还要把有了新场景的剧本放到观众面前！现在，其他人可以通过访问您的 GitHub 库来观看您的场景展开。

如果你不知道你的演员在场景之前是在幕后，还是在场景期间在舞台上，或者场景是否已经被添加到剧中(也就是如果你不知道你是否需要“添加”，或“提交”或“推送”)，你可以检查你的 Git“状态”。在最基本的终端的命令行中，它会准确地告诉您什么被添加到了您的存储库中，什么被提交到了您的存储库中，以及您的本地工作与您开始工作的原始工作相比的状态(甚至可能是彩色的！).查看关于 Git 基础知识的教程,了解更多细节，深入了解如何运行这些 Git 命令。

* * *

### GitHub 的宗旨是协作

如果你不在一个团队中工作，并且想要收藏你的代码并且永远不与任何人分享，你可能不需要 GitHub。但更有可能的是，你要么将与其他人一起工作，要么将希望与他人分享你的工作，所以稍微熟悉一下 GitHub 是有价值的。

这种对协作的关注是为什么你可以“分支”你的代码，这是一种说你的代码从现在开始走向新方向的奇特方式。如果你的代码主干和你的分支最终再次走向同一个方向，你可以在以后“合并”这些分支。我知道这是一个新的隐喻，但是树的隐喻显然是 Git 和 GitHub 所想的，这也是大多数人最简单的概念化方式。

拉取呢，为什么“拉取”不同于“拉取请求”？“pull”是一个 Git 命令，用于引入在不同分支上进行的更改。“pull”命令是“fetch”命令和“merge”命令的组合——你要去一段远程代码(“fetch”)，然后把它和你当前的代码(“merge”)挤在一起。

另一方面,“拉取请求”是一个 GitHub 动作，它允许你请求将一个分支(或其他连接的代码段)添加到树的主干中——你是在[将拉取的代码](https://www.wikihow.com/Graft-a-Tree)移植到主干上，以坚持我们的新树隐喻。

所有这些协作——分支、合并、拉取和拉取请求——都是为了确保团队中的每个成员都可以独立工作，但可以在不互相覆盖的情况下将他们的工作结合起来。这就是为什么合并冲突是不可避免的。我不会在这里深入讨论它们，因为其他人在处理合并冲突方面比我更有经验，但只要知道它们会发生，没关系，你会度过难关的。而且记住，GitHub 是想帮忙的，即使有时候很烦。

* * *

### 驯兽师

[![alt text](img/3a424fe32f96ca807a44e9eac2d139ff.png "The Beast Smiles")](https://i.giphy.com/media/skHD14FWM0tTW/giphy.gif)

这只涵盖了非常基础的内容，因为 Git 和 GitHub 是需要时间来掌握的重要工具。但我在 GitHub 面前感觉更舒服，即使它仍然让我感到惊讶，我仍然不完全相信它不会出现并攻击我。我希望你也是！查看上面的许多链接，因为它们都深入到细节中，是很好的资源。

这对概念化你如何以及为什么使用这些工具有帮助吗？你认为 GitHub(或 Git)有什么有趣的地方吗——还有其他隐喻吗？让我知道！

###### *封面图片来源于[戴安娜内库莱与 FreeCodeCamp](https://medium.freecodecamp.org/how-to-get-up-to-3500-github-stars-in-one-week-339102b62a8f) ，三张 gif 来自 [GIPHY](https://giphy.com/) 。*