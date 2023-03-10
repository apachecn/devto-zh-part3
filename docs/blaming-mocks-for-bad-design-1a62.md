# 将糟糕的设计归咎于模仿

> 原文：<https://dev.to/janvanryswyck/blaming-mocks-for-bad-design-1a62>

不久前，我偶然发现了这个名为[内置假物体](https://vimeo.com/191046493)的演讲。在我看了前 20 分钟左右后，我激动得几乎扔掉了我的 iPad，完全是出于厌烦。我最终还是回来看了剩下的演讲。但是是什么让我如此困扰呢？

这个演讲的主题是使用嘲讽框架是不好的。这本身并没有什么特别的。外面有这么多的会谈，都在竞相向你传递这个勒德分子的信息。但是在这种情况下，特别困扰我的是反对使用嘲讽框架的论点。

这是用来证明嘲笑是魔鬼本身的转世的例子。

```
String report(User user) {
    return String.format(
        "Balance of %s is %d USD",
        user.profile().name(),
        user.account().balance().usd()
    );
} 
```

这是名为 *report* 的方法的简单实现。它返回一个字符串，其中包含有关指定用户的一些信息。下面的代码片段显示了附带的单元测试的实现。

```
@Test
void printsReport() {
    Profile profile = mock(Profile.class);
    Account account = mock(Account.class);
    doReturn("Jeffrey").when(account).name();
    Balance balance = mock(Balance.class);
    doReturn(123).when(balance).usd();
    doReturn(balance).when(account).balance();
    User user = mock(User.class);
    doReturn(profile).when(user).profile();
    doReturn(account).when(user).account();
    assertThat(
        Foo.report(user),
        containsString("Balance of Jeffrey is 123 USD")
    );
} 
```

首先，我不否认测试代码看起来很糟糕。我只是想知道实际上省略空行有多故意。但是使用这个单元测试作为为什么不应该再使用一个模仿库的典型例子是有意地妄下结论。公平地说，我也不会对这个实现使用模拟。但是，这并不自动意味着应该不惜一切代价避免模仿库，仅仅因为它们不应该在大量情况下应用。这基本上等同于说所有的 web 框架都是邪恶的，因为用它们来创建桌面应用程序会带来很多痛苦。从某种意义上说，这是无可争议的。但我也不会如此武断。

在我之前的文章中，我认为单元测试会放大系统设计发出的信号。如果是这种情况，那么在测试代码中使用模拟将这些相同的信号放大十倍甚至更多。我们不应该射杀信使，而是应该学会如何倾听。

同样，我不会使用模拟来测试*报告*方法。相反，我会使用普通的旧状态验证，使用[测试数据构建器](https://janvanryswyck.com/2008/04/test-data-builders-refined/)模式创建一个*用户*的实例，并将该实例作为参数传递给*报告*方法。不需要更多的复杂性，因为这会将单元测试的实现减少到只有几行代码。

但在这个特殊的例子中，这不是我们要走的路。所以让我们运行它，看看模拟是如何告诉我们系统的设计是有缺陷的。首先，一个 mock 用于一个*配置文件*，以及一个*账户*和一个*余额*。甚至*用户*也是一个模仿对象。接口已经就位，以便创建这些模拟对象。

[![Structure of interfaces in the domain model](img/e3e334bca4a38eb92357d391ed0b60dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fbghsp-x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://janvanryswyck.com/assets/img/posts/mocks-not-considered-harmful-01.jpg)

请注意，这些接口只有属性。它们只由数据组成，而不是行为。这是 1 号警铃。现在让我们仔细看看测试代码本身。注意模仿是如何返回其他模仿的实例的。这是 2 号警铃。然后仔细查看报告方法的实现。请注意属性的链接，以便获得必要的数据。答对了。三号警铃。我们发现了一辆火车残骸。明显违反了得墨忒耳的[定律。](https://en.wikipedia.org/wiki/Law_of_Demeter)

如果我们仍然想坚持使用模拟的决定，修复这个设计问题将极大地改进测试代码。对我来说，这让我明白了开发人员倾向于责怪他们的单元测试、模拟的使用等等。而不是责怪自己的设计选择。

提出这样一种教条的观点，认为嘲讽框架是邪恶的，这本身是相当有害的。这对任何人都没有帮助，尤其是那些测试驱动开发的新手。我坚信开发人员应该自我教育，在哪些特定的场景中模仿框架是一个很好的选择，而在哪些场景中它们不是一个好的选择。这个张力场，这个在应用状态验证或行为验证之间的选择也被称为*“TDD 的不确定性原理”*。就我个人而言，当被测试的类与跨越依赖反转边界的合作者通信时，我倾向于使用测试替身，而在这个特定的例子中显然不是这种情况。

在谈话的第二部分，说话者用假货代替仿制品提出了他对问题的解决方案。假的是一个对象，从外部来看，它模拟了与它所替代的真实实现完全相同的功能和行为。在单元测试中使用 fakes 是另一种反模式，这本身就是一篇全新的博文。

我的结论是:如果应用正确，模仿库是一个有价值的工具。我们应该通过犯错误和倾听系统设计和测试本身向我们发出的信号来学习。对我来说，这是通往觉悟的道路，而不是把婴儿和洗澡水一起倒掉。