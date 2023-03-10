# 我的 2018 年度回顾

> 原文：<https://dev.to/piotrmurach/my-2018-annual-review-3i97>

新年是反思我过去一年的起起落落的好时机。我从未做过任何官方回顾，所以今年，我决定取代对过去一年的模糊反思，转而将它们变成更具体的计划和想法。

这篇评论沿着三个无耻地从 [James Clear](https://jamesclear.com) 偷来的问题组织了我的想法:

1.  今年什么进展顺利？
2.  今年有什么不顺利的？
3.  我努力的方向是什么？

我想先提出几点警告。我正在进行一次发现和改进的个人旅程。我寻求理解是什么激励了我，并在我的肚子里点燃了火焰。你可能会在这里找到与你产生共鸣的东西。如果是这样，那太好了！然而，我不会装成一个老圣人，用后见之明告诉任何人如何生活。我更多的是一个探险者，在众多体验岛中修正他的人生轨迹。

彼得·德鲁克说过“你不能管理你不能衡量的东西”。通过这个年度回顾，我很想知道我的进步，我的想法是如何随着时间的推移而改变的，并提供了塑造我现在的行为轨迹。

也就是说，让我们从好的方面开始。

## 1。今年什么进展顺利？

**开源。这真是多产的一年。我写了很多红宝石！准确地说是七个:**

*   [tty-markdown](https://github.com/piotrmurach/tty-markdown)
*   [tty 配置](https://github.com/piotrmurach/tty-config)
*   [共同准备](https://github.com/piotrmurach/coinpare)
*   [tty-pie](https://github.com/piotrmurach/tty-pie)
*   [tty-box](https://github.com/piotrmurach/tty-box)
*   [字符串-ansi](https://github.com/piotrmurach/strings-ansi)
*   [基准-趋势](https://github.com/piotrmurach/benchmark-trend)

我获得了很多乐趣，特别是想出了用于检查和跟踪加密货币投资的 [coinpare](https://github.com/piotrmurach/coinpare) 工具。我不费吹灰之力，主要使用 tty gems，构建了一个相当复杂的命令行应用程序。我非常相信图书馆能做好一件事。这个项目给了我额外的验证，证明了独立的、最小的和可重用的组件的总体思想。

我复活了一个名为[有限 _ 机器](https://github.com/piotrmurach/finite_machine)的红宝石，自 2015 年以来，我一直没有积极维护它。它有一些与代码设计特别是线程相关的棘手问题。重写给了我一些有价值的教训。其中之一是命名概念是阐明系统组件背后意图的重要部分。另一个是软件设计越少越好。通过删除不必要的功能和简化设计，我解决了长期存在的问题，包括线程。有趣的是，我对结构化代码的想法发生了变化。我确认我的直觉和代码敏感度已经大大提高了。如今，我真的很欣赏简单的设计。

**大会发言。**今年有一些最令人难忘的演讲活动！我有机会说:

*   印度 RubyConf
*   巴斯红宝石
*   RubyKaigi Japan
*   布莱顿红宝石

印度的 Ruby 大会是一次非常独特的经历。我可以站在舞台上，但以一种更轻松的方式，开鲁比的玩笑，和观众一起开心。也许这次会议最好的一面是人们——愉快、迷人、超级友好。

当我有机会在日本的 Ruby Kaigi 演讲时，我的梦想实现了！这次经历超出了我的预料。我对我的谈话有许多焦虑，但一切都很顺利。我收到了一个小徽章作为感谢的表示，里面有真正的红宝石。这是一次组织得非常好的会议！会谈的水平非同寻常，非常详细地涵盖了许多复杂的话题。

**旅行。**今年我去了两次主要的旅行——印度和日本。

印度是一个如此神奇的地方，它拥有我一生中所见过的最高对比度。我从未经历过这么多不同的气味、声音和颜色争夺我的注意力。在班加罗尔漫步时，我看到一个骑着白马的家伙在几百万辆汽车的喇叭声中行驶在一条繁忙的路上。就在同一天，我被一个乞讨的小孩踢了一脚，因为我没钱了，也没什么可以给的了！尽管我看到了很多贫穷，但我也看到了很多创业精神；卖婚礼插花或摩托车旧头盔的人。在所有的事情中，我也体验了最美味的食物，并在旅行期间吃素。

我的日本之旅是从东京开始的。在那里，我沿着运河划着皮划艇去看东京的天空树；在一个传统的日本房子里用武士刀训练剑术，并从零开始学习如何准备江户寿司。继我在东京的经历之后，我最终在仙台发表了我的 Ruby 会议演讲。我旅程的最后一站是金泽，一座以武士传统和美丽花园闻名的历史名城。

我也有机会第一次带父母去波兰首都华沙。很奇怪，有时候你一辈子住在一个国家，却从来没有去过首都。我希望在不久的将来探索更多的波兰城市。

## 2。今年有什么不顺利的？

**编程。我对自己感到恼火，在我创建的许多开源项目中，我没能学习和探索新的编程语言。我真的希望扩展我在函数式语言方面的编程技能。具体来说，我打算学习用 LISP 家族语言编码，球拍是主要候选对象。**

**大会发言。**今年我已经被好几次会议拒绝了，最糟糕的拒绝可能是美国和澳大利亚的 Ruby 会议。我也被 EuRuKo 拒绝了，但我可以相当轻松地处理它，因为这是我自 2016 年以来的“传统”——申请并被拒绝。对我来说，这总是一场意志坚强的比赛，这次谁会赢呢？欧鲁科再拒绝我一次还是我放弃？

举重。就实现我的目标而言，今年糟透了。由于会议演讲和旅行，我有很长的休息时间，这让我感觉我一直在努力开始，而不是继续锻炼。尽管困难重重，我还是在大型举重比赛中取得了以下成绩:

*   深蹲 100 公斤(220 磅)，重复 3 次
*   卧推 70 公斤(154 磅)，重复 3 次
*   硬拉 80 公斤(176 磅)5 次

**博客。**我希望完成我个人网站的建设，并发表一些文章。我甚至写了一些草稿。我还没有完成任何一篇文章，以至于我很乐意与世界分享它们。我努力启动这个项目的主要原因是我对它的态度，而不是任何技术或缺乏时间。这个想法有很多自我怀疑。我无法决定哪些事情我应该重点分享，哪些事情会引起人们的共鸣。

## 3。我努力的方向是什么？

2019 年的首要想法是走出我的舒适区，体验新的想法、人和地方。多输出少自我评判！特别是，我将集中注意两个主题:

**协作。我希望与更多的人合作。我觉得在过去的一年里，我过得太像一个隐士，我需要让自己更多地接触新鲜的想法和新的人。**

**成长。**学习在 2018 年并没有进入太多的画面。我做了很多事情，但并不觉得我学到了很多。内部对话更多的是实际产生输出，并找到满足特定目标的解决方案，而不是发现新事物。现在我想多观察，为了学习而学习不同的领域，每天都在进步。

为了帮助我实现上述目标，我计划专注于:

**写作。**我真的希望能够就各种主题撰写一些有用的文章，从非常具体的教程类型“如何做 X”到更一般的抽象主题，如软件架构或开源维护。通过写作，我想发现自己的声音，理清自己的思路。

**3D 打印。**这是一项激动人心的技术发展，我并没有过多涉猎。如今，由于价格处于合理水平，我正在认真考虑给自己买一台打印机，看看我能用它做些什么。我希望能够打印出越来越复杂的结构，比如人形机器人零件。

**综合格斗(MMA)。**这似乎不符合我的性格，但我已经关注 MMA 赛事好几年了，部分是因为[Joanna jj derzejczyk](https://en.wikipedia.org/wiki/Joanna_J%C4%99drzejczyk)和她在 UFC 中的非凡表现，部分是因为我的工作伙伴。我一直对武术很好奇，但我自己没练过。吸引我的不一定是战斗技巧，而是“斗士”的概念。我认为成为一名“战士”是我们所有人的普遍向往。我们为我们的自由，我们的价值观，我们的家庭而战。我需要找个地方开始练习！

**旅行。**我一直在考虑去“附近”的地方和世界上商业化程度较低的地方旅行的想法。令人惊讶的是，尽管坐火车需要几个小时，但我从未去过威尔士。你能相信吗？我也从未去过法国，尽管我也可以坐火车去。难以置信。当然，我仍然希望保持到遥远国家的国际旅行，并在 2019 年至少进行两次主要旅行。

事情总是会变得更好，但总的来说，我对自己过去 12 个月的进步感到满意。这是我试图理解生活的最好尝试。和往常一样，没有确定的事情，只有对可能产生积极和持久变化的猜测。

这是我今年的全部收获。感谢您的阅读，希望 2019 充满变革性的体验！

* * *

*本文最初发表于[PiotrMurach.com](http://piotrmurach.com/articles/2018-annual-review)。*