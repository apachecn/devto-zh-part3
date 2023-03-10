# 商业人士需要了解的技术:测试驱动开发

> 原文：<https://dev.to/joecannatti/what-biz-people-need-to-know-about-tech-test-driven-development-37om>

这是商业人士需要了解的技术系列的一部分。它的目的是帮助非技术业务人员更好地与工程师沟通，并就他们希望工程师提供的价值提供更好的反馈。

### 什么事？

测试驱动开发(通常称为“TDD”)是软件开发人员用来完成工作的一种技术。如果你允许我打一个音乐的比方，这就像一个吉他手用拨片演奏某一部分，而不是用指弹。

如果你是一名高尔夫球手，这就像用一个 65 度的楔子代替常规的投球楔子来下沉一个切球。

如果你是一个芜菁甘蓝农民，这就像使用营养混合 75 和双重修剪，而不是使用巴里嘉宝方法。

好吧…那个芜菁甘蓝农场不是真的。我不知道如何种植芜菁甘蓝……但你会明白的。

### 什么样的手法？

编写代码可能很棘手，因为我们经常需要用相同的代码来满足一堆不同的行为。例如，假设我们正在编写一些代码来计算客户在结账时的欠款总额。

如果你只是一头扎进去开始写代码，它可能会像这样

1.  我会让它合计购物车中所有商品的价格
2.  接下来，我将调用第三方服务来计算税款。
3.  然后，我会这样做，他们的任何帐户信用被扣除。
4.  接下来，如果用户有优惠券，我会让它扣除优惠券的价值。
5.  哦，等等，他们需要在我使用优惠券之前还是之后交税吗？

这有点像卡通人物试图堵住船上的漏洞。你还记得这个吗？(YouTube 上找不到)。一个漏洞冒了出来，角色把手指伸进去堵住它。然后另一个突然出现在船的另一边，他们不得不伸出另一只手去固定那个。然后他们必须用他们的脚趾做第三个，以此类推。

有时你会让自己陷入这样一种境地:每次你让东西 A 工作，你就打破了东西 b。

在我们上面的例子中，这就像每次你使用优惠券时破坏帐户信用计算，然后当你回去修复帐户信用时破坏优惠券。

TDD 让编码者在编写代码之前指定代码需要做的所有事情。这些规范永远存在，每次代码更改时都可以重新检查。

此外，在 6 个月的时间里，当开发人员长期忘记是否应该在计算税款之前或之后取出息票价值时，测试仍然存在，以确保一切仍然正常工作。

### 这会影响最终产品

开发人员必须做一些不同的事情来进行测试驱动开发。编写难以或不可能测试的代码是可能的。当程序员使用 TDD 时，会导致代码以不同的方式设计。

TDD 影响设计的方式几乎总是积极的。我确信在某些模糊的情况下，不可测试的设计会被认为是更好的，但是我不记得在现实生活中遇到过这种情况。

在实践中，TDD 改进了代码设计。

### 对企业有什么价值？

对企业的价值来自两个方面:

1.  减少生产中的错误和缺陷
2.  更快的开发时间

### 长期 vs 短期

注意上面我说的 TDD 使开发更快。有些人会有不同的说法。他们会说 TDD 在短期内花费更多的时间，在长期内节省时间。

这在技术上是正确的，但根据我的经验，平衡点来得太快了，所以更准确地说，它总是更快。如果我正在做的代码要做一天以上，我就做 TDD。

### 先测试 vs 后测试

上面我说过，在编写代码本身之前，你要为代码编写规范。这不是唯一的方法，如果你开始做的事情不容易测试，你也可以写测试，然后修改代码。

大多数开发人员混合了先测试和后测试开发。我仍然可以称之为 TDD，尽管有些人会说如果你先写测试，它只是 TDD。

### 写好代码一定要做吗？

没有。许多公司在他们的工具带中没有这种特殊的技术。然而，没有测试作为你的代码库的一部分，这是一个非常不好的迹象。

在很大程度上，一旦开发人员学会了做好测试驱动开发，他们就再也不会回到任何其他方法。

### 可以有一些……教条

开发人员会对这些东西“充满热情”……

我不确定这是件好事。TDD 是一种工具，工具是用来帮助一个人实现目标的。TDD 本身不是一个目标。就个人而言，当一个开发人员拥有激光般敏锐的 TDD 技能时，我对他印象最深，但是要明白这只是构建伟大东西的一种方式。

### 从来没学过做的开发者

擅长测试驱动开发需要很长时间。对于一个有着多年以不同方式编写代码的经验的开发人员来说，这感觉就像试图用错误的手扔棒球。

因此，你会遇到一些对此有着非常不正确想法的人。在询问某人对 TDD 的看法之前，询问他们做了多少是有好处的。

正如我上面所说的，很少有人在掌握了窍门后，决定不再做了。

### 总结

如今，你几乎总是希望你的开发人员编写测试。工具和学习材料已经发展到可以非常容易地建立一个测试友好的开发系统。

它通过使开发团队能够更快地移动(从长期来看)和减少错误来帮助业务。

出于几个原因，与开发人员讨论这些东西可能会很棘手。他们可能对它过于热情，而忘记了它只是一个工具。如果他们从未学过如何做，他们可能会为自己辩护。一般来说，你希望有人能够谈论 TDD 的好处，但是不要让人觉得对他们来说 TDD 比产品本身更重要。

我希望这对你有帮助。我希望你回来阅读下一期的商业人士需要了解的技术。