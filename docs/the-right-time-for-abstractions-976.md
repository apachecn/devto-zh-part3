# 抽象的正确时间

> 原文：<https://dev.to/pbeekums/the-right-time-for-abstractions-976>

[![](img/4b6211a2ce6dadfe78dfb14f91971281.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--StBXILhs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2018/goodtiming.jpeg)

抽象是软件开发的关键部分。虽然我们开发人员经常[抽象太多](https://dev.to/pbeekums/abstraction-for-the-sake-of-abstraction-1fe4)，但是如果没有抽象，今天构建软件将会花费成倍增加的时间。如今很少有人编写自己的代码来存储数据。我们改用数据库。网络服务器也是如此。和 HTTP 库。和 JSON 编码器/解码器。

然而，像任何事情一样，我们可以拥有太多的好东西。我们过度使用抽象的地方通常涉及到产品的业务逻辑。当构建一个抽象时，有一个固有的假设，即几乎所有可能的用例都被考虑到了。对于像数据库这样的纯技术抽象来说，这是一个不错的假设，因为有几十年的软件项目可以作为用例。

对于一个产品的业务逻辑，构建一个抽象需要这样一个假设，即你既知道你的用户想要什么，也知道产品在未来几年将如何发展。

这些都是巨大的假设。

尽管我们可以依靠以前的经验来构建技术抽象，但是对于业务逻辑抽象来说，几乎没有经验可以依靠。上一个让你说“耶！这太神奇了！”？

许多与其他产品相似的新产品通常都有一些不同之处。90 年代有很多搜索引擎，但 PageRank 让谷歌成为最好的。Mastadon 可能看起来像 Twitter，但去中心化是一个巨大的差异(是否足以让它像 Twitter 一样成功是另一个问题)。有很多博客平台，但 dev.to 以最快的速度脱颖而出。我把 Maleega 的作为唯一一个不试图帮助你到达零收件箱的电子邮件客户端。

复制另一个没有有意义的差异化产品通常不会带来成功。必应仍然落后于谷歌(实际上我已经尝试给它一个机会)。Google+向脸书认输了。大家对 AWS 都有一场硬仗。

这使得业务逻辑的抽象变得困难。一个成功的产品通常包括做一些不同于现有产品的事情。在极少数情况下，人们能马上猜出区别是什么。我们其余的人必须进行实验，看看什么能引起用户的共鸣。

在不知道这些实验结果的情况下构建抽象概念是一个巨大的错误，这种错误我已经见过无数次了。通常是这样的:

*   产品已定义
*   抽象是为用例 A、B、C、D 而构建的，只是为了确保:E
*   用户开始使用产品，并希望用例 F 得到优化
*   开发者抱怨这个系统不是为 F 设计的

这里最好的情况是，团队不知何故混了过去，并构建了用户想要的产品。也许有一个抽象的重构。也许是建立了变通办法。无论哪种方式，旨在节省未来开发时间的抽象实际上已经成为技术债务。

最糟糕的情况是，由于技术上的困难，产品的改进没有进行，这给竞争对手创造了一个更好的机会。

再多的技术经验也弥补不了不知道产品的发展方向。出于这个原因，推迟围绕业务逻辑构建抽象直到方向变得更加清晰是有意义的。当一个产品被使用后，模式就会出现，然后可以构建抽象来匹配这些模式。我构建的最好的抽象是对现有逻辑的重构。有些人讨厌重构，但我认为不需要不惜一切代价避免重构。