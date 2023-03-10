# 测试——从你最害怕的代码开始

> 原文：<https://dev.to/uilicious/testing-start-with-the-code-that-scares-you-most-5dpn>

<figure>

[![Let software architecture decisions be simpler than Brexit](img/815be17ac03509ea9ec5c1af3845923e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--khFDO81Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iaxak1j8uyvjl96htde6.png)

<figcaption>Let software architecture decisions be simpler than Brexit</figcaption>

</figure>

# 选择的悖论

作为软件工程师的第一份工作，我有幸(或不幸——取决于你如何看待它)在一家初创公司工作，那里的口号是快速行动，打破常规。工程的重点是尽可能快地发布特性，测试是在产品上完成的。很自然地，由于一群大多是新培养的软件工程师，我们很快就积累了技术债务。为了加快速度，很多代码在每一层都用胶带粘在一起(打个比方)。

不用说，bug 很频繁，客户投诉也是如此。业务团队对工程师感到沮丧，说“你们不测试吗？”。

哎哟，但他们是对的。

我们没有进行足够的测试，而且我们大多数人也承认我们可能在测试方面做得很差，因为工作太枯燥了，而且开发人员对他们自己的工作有点缺乏远见。

自动化测试听起来是个好主意。

好吧，那么...我们从哪里开始，我们应该怎么做，我们应该使用什么工具？

<figure>

[![Here's comes the ideology wards](img/cbaa5029587dff9c2d6b61f61a82ec5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e3G1SQGJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w14io90n2300ss9wkoql.jpeg)

<figcaption>Engineers can get very intense with their ideology wars</figcaption>

</figure>

有些人认为最好的方法是从单元测试开始，因为这样我们可以快速地将故障隔离到单个组件。其他人认为覆盖大部分代码会花费太长时间，我们应该从集成测试开始，然后再进行单元测试。

一些人认为测试驱动开发(TDD)是正确的方法。其他人认为 TDD 太费时间了，而且是浪费时间，因为新特性的需求经常变化。

然后还有工具。

团队陷入了决策瘫痪,并决定推迟讨论(通常是在混乱再次爆发的时候)。

# 从哪里开始？就从最恐怖的代码开始。

好事情是，在创业公司工作，一个人有很多自主权和尝试的机会。(或者这可能是一件坏事，当你有太多的初级开发人员陷入闪亮的物体陷阱时...).我继续前进，开始编写测试，并将它们连接到我们的构建过程中。

当您有如此多的积压工作时，自动化测试听起来像是一项艰巨的任务，但是所有的旅程都是从一个单独的步骤开始的。

我发现了一个很好的决定从哪里开始的经验法则。

确定你最害怕触碰的代码。

[![Complex Code](img/a7358abad6782b6d4eb50caf430b45ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q3_WrSUL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dm7i57klknna5m3wjdo7.png)

为什么？

很有可能，你害怕接触这些代码，因为:

*   **很复杂**，因为有很多条件句，可能没有很好的文档记录。
*   **这对业务至关重要**。

我开始为我们的计费模块编写测试。计算客户应支付多少费用的计算很复杂。不幸的是，管理团队也经常想要改变计费模式。如果计算是错误的，那么，我们将失去大量的收入和客户的信任。我真的不想让自己被解雇，也不想承担经济损失...

一旦你弄清楚要测试什么，实际上就很容易弄清楚如何测试以及使用什么工具。

对于计费模块，我用 JUnit 设置了单元测试，因为我们有一个 Java 代码库，将测试与我们的 Maven 构建挂钩非常简单，所以每次开发人员试图编译应用程序时都会运行这些测试。API 测试在技术上可以覆盖相同范围的测试，但我认为获得关键功能的即时反馈更重要。

一旦测试启动并运行，我不再害怕重构代码来提高可读性和可维护性。当我转移到其他项目并最终转移到另一份工作时，将我的工作移交给另一个开发人员对我来说并不困难。我可以简单地指向单元测试，说这是你的文档，这是代码的工作方式。回想起来，这让我意识到这句话**“遗留代码是未经测试的代码”**是多么正确。

所以，如果你正在考虑自动化测试，但是听起来让你不知所措，试着从最可怕的代码开始。一旦你吃了那只青蛙，事情就变得简单了。