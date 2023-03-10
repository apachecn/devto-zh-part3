# 银河国际象棋守护者 Vol. 1 又名约束机器 ELI5

> 原文：<https://dev.to/radixdlt/chess-guardians-of-the-galaxy-vol-1-aka-the-constraint-machine-eli5-2iaf>

或者为什么约束机会拯救宇宙…

我们的任务是让 Radix Tempo Consensus 可编程，我们偶然发现了一个新的想法，我们称之为约束机器。

撇开谦逊不谈，我们相信基数约束机是一个没有人要求但每个人都需要的解决方案:一个可伸缩的、分散的、实时/离线的、可编程的状态机存储。

在第一篇文章中，我们将回顾最后一部分“状态机存储”的确切含义。要做到这一点，让我们先来看看传统的数据库…

# 传统数据库

传统的数据库就像一本数据“书”。任何有权限的人都可以打开并阅读它。任何人用它的铅笔和橡皮都可以在里面写字和擦除。这样，“书”对于信息共享是非常有用的。

然而，对于更复杂的活动，例如下棋，仅仅使用一本书和一支铅笔将是相当具有挑战性的，因为这本书缺乏规则。这本书没有仲裁人或监护人说“不，你不能写，因为这不是一个有效的举动！”。

[![alt text](img/6f7c40c6dd4393e22bde2cd7822ebf71.png "Chess Guardians of The Galaxy Vol. 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--yKGxjUd2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/unnamed-11.png)

# 服务

守护者就这样被创造出来，或者用软件行话来说，“服务”。在我们的象棋例子中，用户不再直接阅读和书写书籍，而是必须通过象棋卫士。

[![alt text](img/0732d0036e01dee4411dc7e9a9178d37.png "Chess Guardians of The Galaxy Vol. 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--ASTzj8NE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/unnamed-1.jpg)

然后，监护人保护数据，并把用户告诉他的监护人认为有效的任何举动写入书中。现在，如果有人给了守护者一个糟糕的举动，象棋守护者将会在那里阻止这个糟糕的举动到达书本。

# 缩放

现在，如果我们想要 1000 或 100000 名用户下棋，我们需要“扩展”我们的服务。我们可以通过增加更多的象棋卫士(服务)和书籍(数据库)来做到这一点。

[![alt text](img/509714ada885043bccecfc479d796274.png "Chess Guardians of The Galaxy Vol. 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--PpocRLvf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/unnamed-10.png)

够简单，或者是…

# 保护数据库

因为这些书包含了所有象棋游戏的真相，守护者必须用他们的生命来保护它。一个能够躲过监护人的入侵者可以偷一本书，或者更糟，直接在书上写字，改变整个游戏。这不能发生！

为了防止这种情况，需要在书籍周围建立墙和防御(防火墙)。此外，必须用仅给予监护人和最值得信任的个人的锁和钥匙(许可)来保护书籍。

[![alt text](img/ef91fa82b2e0fbaf812dd0a2dca260fa.png "Chess Guardians of The Galaxy Vol. 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--9oay_Vah--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/unnamed-9.png)

# 再次保护数据库！

即使有完美的防御，事故也可能发生。一个值得信任的人可能会不小心把一个糟糕的举动写进书里，或者随着时间的推移，这本书可能会开始磨损，变得不可读。

为了防止所有这些，复印机(数据库冗余)被用来将监护人写的所有东西复制到一个或多个额外的书中(所有这些都需要一把锁和钥匙)。这样，如果原书因为任何原因被弄乱了，还有额外的备份书可以使用。

[![alt text](img/d4b4a9f28bc9467928bda97c833c33c3.png "Chess Guardians of The Galaxy Vol. 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--CxnNXigt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/unnamed-8.png)

我们的象棋服务现在受到了很好的保护…或者是…

如果我们的小堡垒被自然灾害袭击了怎么办？为了防止这种情况，我们需要在远处创建一个重复的堡垒，并找到一种方法在两者之间进行通信以实现一致性(地理复制)。

为什么我们要做这么多，仅仅是为了维持一个功能正常、可扩展的服务。这么简单的事情怎么会这么难？

归结起来就是监护人(规则)和账本(数据)之间缺乏同步性。守卫者希望以一种非常特殊的方式使用这本书，但这需要巨大的努力来实施，因为这本书是如此开放。要是有一种方法能把两者的力量合二为一就好了…

# 约束机器和守护书

在这里我们终于有了发现:约束机器或守护者之书。

我们所做的是将守护者(规则)和他们的精神与书(数据)深度融合，以创建一个基本的原语:一个“守护者之书”，也就是说，一本书本身可以理解复杂的、有状态的规则，并可以执行它。

[![alt text](img/a9bb405caf1f34b63076ab7649c7a93e.png "Chess Guardians of The Galaxy Vol. 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--EM3DVBp7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/unnamed.jpg)

从外表上看，这本《守护者》看起来就像一本普通的书，但是它有着黑魔法，这使得它与其他任何一本书都不一样。你不是在空白纸上写数据，而是在书中与“活的”动态事物互动。

你不用写下一步棋，而是说出你的下一步棋。一旦守护书确定你和你的移动是“有价值的”，你就可以亲眼看到棋子移动到正确的方块。这就是《守护者之书》中的守护者精神。

# 这在实践中是什么意思？

因为《卫报》和书是一体的，用户现在可以直接与书互动。不再需要监护人、锁、钥匙、墙壁和复印机。这意味着更少的基础设施、更低的复杂性、更低的成本。

[![alt text](img/ec058a6356ee3a31a96174f96a962b49.png "Chess Guardians of The Galaxy Vol. 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--SXjTtAje--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/constraintmachine2.png)

为了最后一次说明这一点，常规书籍(数据库)存储象棋游戏的表示，而监护人书籍(约束机器)存储实际的“现场”象棋游戏(状态机存储)。

# 卷二…

我们已经了解了约束机作为状态机存储的基本含义，但是还有更多内容有待发现。

在以后的文章中，我们将展示约束机制是如何工作的:

-完全异步、实时/离线能力
-可编程
-能够实际上自由分支和时间旅行(像 git)
-线性可扩展
-完全分布式/分散式(如果需要)
-无许可共识协议的完美匹配，Tempo

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询