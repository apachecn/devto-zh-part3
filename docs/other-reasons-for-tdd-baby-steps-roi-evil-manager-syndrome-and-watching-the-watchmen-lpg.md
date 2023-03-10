# TDD 的其他原因:小步走，投资回报率，邪恶经理综合症和观看守望者

> 原文：<https://dev.to/danlebrero/other-reasons-for-tdd-baby-steps-roi-evil-manager-syndrome-and-watching-the-watchmen-lpg>

也许这个世界不需要另一个“TDD 的 X 个理由”,但是我发现大多数人忽略了遵循测试优先方法的四个非常重要的理由:

1.  婴儿学步
2.  投资收益率
3.  邪恶经理综合症
4.  看着守望者

## 婴儿步伐

TDD(测试驱动开发)工作流程鼓励你一小步一小步地进步。有了这个工作流程，你会觉得自己就像一只每走几步就要站起来检查周围环境的小猫鼬。

也许这不是到达目的地最快的方式，但无疑是最安全的。但是，由于我们没有被吃掉的风险，对我们来说更安全意味着什么呢？

在每个小的改变之后，我们检查没有引入 bug。如果引入了一个 bug，那么很明显这个 bug 是在何时何地引入的:在最后一个微小的步骤中。

查找 bug 变得微不足道。bug 是软件开发中第二大时间浪费:

1.  首先，你花时间写它。
2.  然后必须有人检查这个 bug。
3.  然后，您必须将其发布到生产环境中。
4.  你的委托人必须举报。
5.  你对影响进行分类。
6.  你的经理必须优先考虑它。
7.  你必须填补一些 JIRA 问题。
8.  你给你的 QA 解释一下。
9.  你必须调试代码，这些代码现在对你来说可能是完全陌生的，因为你可能已经写了几天或几周了。
10.  那你来修。
11.  有人检查了修复。
12.  最后，您将修复发布到产品中。

因此，矛盾的是，循序渐进和更安全也意味着更快，这取决于你写了多少个 bug。

这个原理其实和连续交货的[“小批量”原理](https://continuousdelivery.com/principles/#work-in-small-batches)完全一样。

寓意:如果你不写 bug，就不要浪费时间写测试。

### 婴儿的步子应该有多小？

尽可能小，但它的下限将取决于您的测试套件有多快:

[![Changes between runs](img/bbc6f9fb27deb4af465825344d2a3646.png "Changes between runs")](https://res.cloudinary.com/practicaldev/image/fetch/s--TEym3CTo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/why-tdd/changes-between-runs.jpg)

即使你试图在每一个小的变更后都非常严格地运行你的测试，如果你的测试套件很慢，你最终会使你的变更批量变大，只是为了避免等待的痛苦。

因此，如果您想要影响测试运行的频率，您需要确保它们运行得非常快。

## 投资回报率

一个测试套件是一项投资，和任何投资一样，您希望有一个高的正投资回报(ROI)。ROI 可以计算为:

*ROI =(投资收益-投资成本)/投资成本*

测试套件的成本非常明显:构建和维护它的时间。还有运行它的持续成本(更快的测试套件更好的另一个原因)。

测试套件有几个好处，但是我们可以把它们总结为在进行变更时节省的时间。

因此，一个测试套件的 ROI 可以计算为:

*ROI =(节省的时间-花费的时间)/花费的时间*

如果我们在构建一个新特性时用最后测试的方法来绘制 ROI(红色)，我们可以想象成这样:

[![ROI test last](img/d7042f04934058adab6432a55d9ea5ad.png "ROI test last")](https://res.cloudinary.com/practicaldev/image/fetch/s--nwte1Vlb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/why-tdd/roi-test-last.jpg)

如果你再也没有接触过代码库的那一部分，那么测试是你最糟糕的投资，因为直到你开始在代码库的那个特定区域再次做出改变，它才会开始有回报。

让我们将其与测试优先的方法进行比较:

[![ROI test first](img/cd92ace852a3c744e38f1322efbd1ef1.png "ROI test first")](https://res.cloudinary.com/practicaldev/image/fetch/s--VXJFPdY5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/why-tdd/roi-test-first.jpg)

测试开始立即得到回报，因为您正在积极地改变那些测试所覆盖的代码库。

*寓意:* **如果**你写一个测试，先写出来。注意前面语句中高亮显示的 **if** 。

## 恶经理综合症

当我们被问到为什么我们的代码覆盖率不够理想时，“我没有时间写测试”可能是我们最喜欢的借口之一。这不是你的错，这是你邪恶的经理的错:

经理:“我们昨天就要发货了！”

团队:“可是我们还没完呢！!"

经理:“让我们抄近路吧！！!"

团队:“让我们跳过编写测试，直到我们有时间！！！!"

经理:“牛逼！你们是一个了不起的团队！！！！!"

哭着重写的螺旋通常是这样开始的:

[![Spiral to the crying rewrite](img/011867a612351541d6d56ed4828ccbb2.png "Spiral to the crying rewrite")](https://res.cloudinary.com/practicaldev/image/fetch/s--dIEuJ_FH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/why-tdd/spiral-to-crying-rewrite.jpg)

1.  因为你只有很少的测试，所以你不能重构。
2.  由于您不能重构，您的代码库开始积累垃圾。
3.  代码库中的垃圾越多，功能实现的时间就越长。
4.  功能实现的时间越长，时间压力就越大。
5.  时间压力越大，考试越少。

这就完成了恶性循环，最终我们，开发者，哭着要求完全重写。

让我们看看同样的情况，但是在进行测试优先时:

经理:“我们昨天就要发货了！”

团队:“可是我们还没完呢！!"

经理:“让我们抄近路吧！！!"

团队:“我们能切什么特征？！？！？!"

经理:“切割功能？？？不要写任何测试！！！！!"

团队:“我们已经写好了！！！！！!"

经理:“混蛋！！！！！！!"

然后是长时间的谈话，讨论应该削减哪些部门，这是你的经理不喜欢的。欢迎来到敏捷。

## 守望的守望者

如果测试测试了应该测试的东西，那么看到测试失败就是测试。

或者用更简单的话来说，你怎么知道你的测试没有任何 bug？谁看着你的守望者？

如果你先写了一个测试，然后你发现它失败了，这是一个很强的迹象，表明有一些产品代码，一些逻辑，是不存在的。

如果您编写了测试，但从未看到它失败，您不知道这是因为您已经实现了一个特性，还是因为您忘记了测试中的断言，或者设置代码不正确。

[![watchmen](img/58f1c20cf546baec6c6fef74f83ddcbc.png "Watching your tests")](https://res.cloudinary.com/practicaldev/image/fetch/s--n3ElUgky--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/why-tdd/watchmen.jpg)

寓意:首先编写您的测试保持守望者的诚实。

## 测试——首先所有的事情？

但是不管你有多少理由，当你提出这种测试优先的方法时，大多数时候你会得到不祥的答案:“如果我不知道我要构建什么，我怎么能首先编写测试呢？”

任何有经验的 TDD 开发人员都会告诉你，这是废话...但并不总是如此。下次我们将会看到什么时候 TDD 是最好的方法，什么时候不是。