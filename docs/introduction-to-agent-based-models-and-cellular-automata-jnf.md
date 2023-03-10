# 基于主体的模型和元胞自动机简介

> 原文：<https://dev.to/thegeoffstevens/introduction-to-agent-based-models-and-cellular-automata-jnf>

基于主体的模型(ABM)是一种计算工具，用于模拟一组自主主体，目的是分析系统作为一个整体是如何受到影响的。

在反弹道导弹中，每个个体都遵循一套特定的规则。作为观察者，我们可以看到这些规则是如何影响整个系统的。

城市是简单的基于代理的模型的一个例子。模拟的规则是在微观层面上编码的(例如，个人)，但最有趣的揭示发生在宏观层面上。

例如，在高峰时间，每个人都决定走最短的路回家。一些工人将走高速公路，而其他人可能选择走小路。

因此，某些道路可能会比其他道路更加拥挤。对城市的影响，作为一个整体，是许多不同的人采取的个人行动的结果。

## 细胞自动机和康威的生命游戏

构建 abm 有许多不同的方式，但最基本的实现之一被称为[细胞自动机](https://en.wikipedia.org/wiki/Cellular_automaton)。

在其最简单的形式中，细胞自动机是使用网格实现的基于代理的模型。网格中的每个单元都是一个单独的代理，可以存在于有限的状态中。一个单元的状态取决于其邻居的状态。随着时间的推移，每个单元将重新评估其当前状态，并做出任何必要的更改。

让我们看一个例子。最著名的细胞自动机是康威的生命游戏。

生命的游戏是一个二维的网格，每个细胞都可以是活的，也可以是死的。在生命游戏的开始，在一个被称为播种的过程中，一组细胞被随机选择存活。在模拟的每次迭代期间，每个单元将根据其邻居更新其状态。基本规则是:

1.  任何少于两个活邻居的活细胞都会死亡。
2.  任何有两个或三个活邻居的活细胞都可以存活到下一代。
3.  任何有三个以上活邻居的活细胞都会死亡。
4.  任何有三个活邻居的死细胞都会变成活细胞。

如果你在一个网格上想象这个过程并不断迭代，你会得到这样的结果:

[![Conway's Game of Life](img/7b62613eb6c1726d6c140bb824ad4fc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mm4G4y35--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/e/e5/Gospers_glider_gun.gif)

如果你对尝试不同的模式感兴趣，你可以尝试一下网络版的生活游戏。

## 为什么开发者要关心 ABMs 和游戏人生

以下是我认为任何技能水平的开发人员都应该了解 abm 的原因:

*   **高可用性**:没有复杂统计或数学背景的开发人员可以构建有意义的现实场景模型，并获得有趣的结果。
*   **非常适合实验**:因为即使复杂的 ABM 也可以根据简单的规则构建，所以有很多方法可以定制、调整和扩展 ABM 的复杂性。ABMs 也提供了很多机会来学习更多相关类型的模型，像[多代理系统](https://en.wikipedia.org/wiki/Multi-agent_system)，它使用智能代理来解决单个代理或整体系统难以解决的问题。
*   **学习的乐趣**:如果你正在考虑学习一门新的语言，那么《生活的游戏》是用多种语言编写的，这对于相互参照来说是非常好的。有了简单的模拟规则，您可以更专注于理解代码的不同部分。
*   强大的适用性:我们周围的世界充满了使用 abm 进行复制的机会。股票交易、中产阶级化、森林火灾、流行病、社交网络——都可以用 ABMs 建模。有无穷无尽的事情等着你去尝试。

## 对游戏人生有帮助的代码示例

如果你正在寻找一些你可以探索的例子或教程，我已经收集了一些我遇到的有用的回复。

#### HTML/CSS/JavaScript

Chris Coyier 展示了使用不同的库和工具实现生命游戏的各种方法，包括 Backbone.js、D3.js 和 CoffeeScript。每个都有自己的代码笔，所以很容易复制。

罗伯特·斯巴茨也有一个非常直观的密码本[这里](https://codepen.io/RBSpatz/pen/rLyNLb)值得一试。

#### 巨蟒

罗伯特安德鲁·马丁有一个简短的中型职位[在这里](https://medium.com/@martin.robertandrew/conways-game-of-life-in-python-2900a6dcdc97)和 GitHub 回购[在这里](https://github.com/robertmartin8/PyGameofLife)开始。唯一的依赖项是 numpy 和 matplotlib，这是两个非常常见且有良好文档记录的 Python 库，因此代码非常容易访问。

Jake VanderPlas 用 Jupyter 笔记本详细记录并仔细解释了 Python 中的生命游戏的实现。Jake 还提供了一些不同的初始配置供您尝试。

#### Java

汤姆·布莱克摩尔用 Java 做了一个简短的实现。该模拟使用控制台作为其显示器，因此它很容易启动和运行，不需要复杂的 GUI 或许多依赖项。

#### 去吧

围棋的主要网站[golang.org](https://golang.org/)，这里有一个非常基本和简洁的生命游戏[的实现。](https://golang.org/doc/play/life.go)

如果你有兴趣了解更多，我强烈推荐你看看我上面提到的一些项目。如果你遇到任何其他有用的回复或工具，请在评论中分享！