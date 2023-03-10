# 强化学习:博弈论

> 原文：<https://dev.to/swyx/reinforcement-learning-game-theory-336a>

*这是我学习[佐治亚理工学院/优达城机器学习课程](https://www.udacity.com/course/machine-learning--ud262)的一系列课堂笔记中的第 19 篇也是最后一篇。课堂教材是汤姆·米切尔的[机器学习。](https://www.cs.ubbcluj.ro/~gabis/ml/ml-books/McGrawHill%20-%20Machine%20Learning%20-Tom%20Mitchell.pdf)*

## 为什么博弈论出现在强化学习中

到目前为止，我们对 RL 的了解完全基于环境中的单个参与者。然而，当考虑到其他参与者的行为时，最优策略会发生显著变化，这可能会改变我们自己行为的预期收益。这就是我们在 RL 中加入博弈论的原因。

## Minimax

我们定义了一个两个玩家之间的简单游戏，有一系列选择，零和收益(一个玩家的赢是另一个玩家的输)，确定性结果，完美信息。

```
 B
      L    M     R

    L 1   -3     0
A
    R 0    3    -1 
```

即使在这种情况下，也不清楚应该选择 B 还是 A。要采用的一种启发式方法是，A(例如，它想要最小的数字)总是寻找最大的数字，并将其最小化。反之亦然。每一方都考虑另一方的最坏情况响应。所以 A 选 l，这叫极小极大。

然而，如果 B 也只关注自己，并做极大极小(它想要更高的值)，它会选择 M，讽刺的是，M 给了 A 它想要的-3。所以 B 在制定自己的行动时必须考虑 A 可能会做什么。在这种情况下，最好选择 L，因为最坏的情况并没有那么糟糕。

## 冯·诺依曼的理论

> 在两个参与者的零和确定性完全信息博弈中，每个参与者总是存在一个最优的纯 T2 策略。

也就是说，如果我们假设局中人遵循`minimax`或`maximin`，那么这个博弈总有一个确定的值，它们是相同的。

更多阅读:[https://cs . Stanford . edu/people/eroberts/courses/soco/projects/1998-99/game-theory/neumann . html](https://cs.stanford.edu/people/eroberts/courses/soco/projects/1998-99/game-theory/neumann.html)

以及[博弈论和](https://en.wikipedia.org/wiki/Theory_of_Games_and_Economic_Behavior)经济行为

安德鲁·摩尔谈零和游戏

## 放松假设

如果我们放松**决定论**的要求，在冯·诺依曼下我们会得到同样的结果——只是所有的选择都是基于期望值做出的。

如果我们放松对**完美信息**的要求，就会出现很多复杂性。冯·诺依曼坏了。

对冯·诺依曼来说，依靠纯粹的 T2 战略非常重要。如果每个玩家都过于稳定，他们就会变得可预测，从而被利用。因此，**混合**策略根据概率分布改变策略。然而，如果对于 A 选择的每一个`p`，B 可以选择一个由`p'`支配的相应混合策略来抵消它，那么仍然存在一个概率均衡，其中收益满足:

[https://www.youtube.com/embed/d2gr9VrSNTQ](https://www.youtube.com/embed/d2gr9VrSNTQ)

最后，如果我们放松零和游戏的假设，我们可以得到像 T2 囚徒困境那样的情况，在这种情况下，如果他们不背叛对方，合作可以产生比各自独立更好的平均结果。然而因为背叛策略在每种情况下都占主导地位，

参见[安德鲁·摩尔关于非零和游戏的幻灯片](http://www.cs.cmu.edu/~./awm/tutorials/nonzerosum.html)

## 纳什均衡

给定有策略的参与者，你知道你处于纳什均衡中，如果你让任何一个参与者改变他们的策略，他们会变得更糟。没有人有任何理由改变他们的策略。[这个定义还有一些其他的小结果](https://www.youtube.com/watch?v=rz9cuSIrwGs)。

## 重复游戏

到目前为止讨论的大多数游戏都是有限的、一次性的游戏。如果我们可以玩几轮游戏，考虑到前几轮的信息，会怎么样？这导致了一种形式的策略交流的发生，也建立了采取自私策略的后果，在这种情况下正和结果是可能的。因此，它导致策略倾向于合作。

这可以建模为“信任”，但只有在游戏总数未知的情况下才有意义。(通过归纳，如果你知道你处于重复博弈的最后一轮，那么它是一次性博弈，所以倒数第二轮需要考虑到这一点，以此类推，直到第一轮。所以用一个有限的、已知的`n`重复博弈只会导致`n`重复纳什均衡。).

因此，假设游戏数量未知(有可能结束`gamma`，这看起来非常类似于折扣因子)，鼓励合作的一个伟大策略是[针锋相对策略](https://en.wikipedia.org/wiki/Tit_for_tat)。这建立了回合之间的交流和结果，将净次优结果社会化。事实证明，TfT 最适合 gamma 值高于一定水平的情况，这取决于收益，因为如果不是这样，自私的好处就超过了后果的几率。

## 重复博弈的民间定理

以牙还牙是一个普遍观点的例子，即报复的可能性为合作打开了大门。在博弈论中，术语“[民间定理](https://en.wikipedia.org/wiki/Folk_theorem_(game_theory))指的是在重复博弈中纳什策略可能产生的一组特定的收益。

**最小最大值档案**是一对支付，每个玩家一个，代表玩家在防御恶意对手时可以获得的支付。([更多描述](https://www.youtube.com/watch?v=Yn4zmWwy9mA))

**可行的收益曲线**是可用策略的任意组合:

[![https://vknight.org/Year_3_game_theory_course/Conteimg/L10-img01.png](img/6dc844a8849c2665bdb64c9b837a83f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tH7jceHD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vknight.org/Year_3_game_theory_course/Conteimg/L10-img01.png)

一些可行的收益范围将会比单独实现的更好(合作的激励)，这导致了民间定理:

> 任何严格支配最小最大值/安全级别分布的可行的支付分布都可以实现为纳什均衡支付分布，具有足够大的折扣因子。

换句话说，如果你拒绝合作，我可以把你降到最低限度，而不是让你得到比你自己更好的好处。

## 子游戏完美

这种威胁不能过于极端，以至于难以置信——以至于损害你自己的利益来惩罚我的行为。一个貌似合理的威胁的正式术语是[子博弈完美](https://en.wikipedia.org/wiki/Subgame_perfect_equilibrium)——你给出了独立于历史的最佳回应。

像[冷酷触发器](https://en.wikipedia.org/wiki/Grim_trigger)这样难以置信的威胁并不是完美的子博弈，因为你总是可以放弃策略来提高自己的利益。

TfT 和 TfT 的组合是*而不是*完美的子博弈，因为你可以选择宽恕而不是立即报复。

有一种基于同意/不同意的变体叫做[巴甫洛夫策略](https://www.frozenevolution.com/pavlov-strategy)，它提供了更多的记忆，这就是完美的子博弈，因为不同意会导致背叛，而背叛会导致合作的协议。精彩！现在，它变成了一个看似合理的威胁，比 TfT 更容易实施。([更多关于这个](http://www.pnas.org/content/93/7/2686.short))

## 随机博弈(或马尔可夫博弈)

这是 MDPs 和重复博弈的推广，是多主体强化学习的正式模型。

## 我们系列的下一个

关于此主题的更多说明:

*   [课堂笔记](https://www.ritchieng.com/machine-learning-game-theory/)
*   关于[计算民间定理](http://jmvidal.cse.sc.edu/library/littman03a.pdf)的论文，展示了你可以构造子博弈完美纳什均衡。多项式时间内的任何游戏。
*   Coco 价值观——增加辅助支付以鼓励纳什均衡

希望这是对博弈论的一个很好的介绍。欢迎反馈和提问。整个系列可在此处查看:

*   [概述](https://dev.to/swyx/machine-learning-an-overview-216n)
*   监督学习
    *   [决策树](https://dev.to/swyx/machine-learning-classification-learning--decision-trees-1mbh)
    *   [回归](https://dev.to/swyx/supervised-learning-regression-4d17)
    *   [神经网络](https://dev.to/swyx/supervised-learning-neural-networks-mpo)
    *   [基于实例的学习(K 最近邻)](https://dev.to/swyx/supervised-learning-instance-based-learning-and-k-nearest-neighbors-kge)
    *   [集成学习(AdaBoost)](https://dev.to/swyx/supervised-learning-ensemble-learning-lim)
    *   [内核方法&支持向量机](https://dev.to/swyx/supervised-learning-support-vector-machines-3mgk)
    *   [计算学习理论](https://dev.to/swyx/supervised-learning-computational-learning-theory-160h)
    *   [VC 尺寸](https://dev.to/swyx/supervised-learning-vc-dimensions-10b)
    *   [贝叶斯学习](https://dev.to/swyx/supervised-learning-bayesian-learning-403l)
    *   [贝叶斯推理](https://dev.to/swyx/supervised-learning-bayesian-inference-4l72)
*   无监督学习
    *   [随机优化](https://dev.to/swyx/unsupervised-learning-randomized-optimization-4c1i)
    *   [信息论](https://dev.to/swyx/unsupervised-learning-information-theory-recap-4iem)
    *   [聚类](https://dev.to/swyx/unsupervised-learning-clustering-42mi)
    *   [功能选择](https://dev.to/swyx/unsupervised-learning-feature-selection-84f)
    *   [特征变换](https://dev.to/swyx/unsupervised-learning-feature-transformation-pcf)
*   强化学习
    *   马尔可夫决策过程-3 月 25 日的一周
    *   “真实”RL-4 月 1 日开始的一周
    *   [博弈论](https://dev.to/swyx/reinforcement-learning-game-theory-j1o)