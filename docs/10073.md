# 你不知道 TDD

> 原文：<https://dev.to/anortef/you-dont-know-tdd-47a>

好吧，也许你认为你知道 TDD 是什么，但是让我告诉你一个故事。

几年前，有一个年轻的开发人员认为，即使她/他有很多东西要学，她/他至少有一个相当坚实的基础。她/他知道耦合是不好的，类应该是单一责任的，类内部的方法也应该是单一责任的，以便产生可维护和可扩展的软件。

这一点，我们大多数人都能对这个故事产生共鸣，笑自己那么幼稚。当然，我们认为我们现在写的是解耦代码，但事实是大多数开发人员不知道 TDD 是什么，他们写的是耦合代码，几年前由于实践的原因，耦合度降低了，但耦合度仍然没有降低。证明就在你公司的仓库里，以及每次开发人员需要改变代码库时，他们是如何颤抖的。

## TDD 不是单元测试，但单元测试是 TDD。

TDD，顾名思义，是在测试的辅助下开发软件的实践。其中一个工具是单元测试，但是如果你只使用它们，你会得到这样的结果:

> ![unknown tweet media content](img/7d24cc4d1f2d9a8c6986042918faaecc.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/CYsaEZiW8AIMesY.mp4" type="video/mp4"></video>![DEV Community 👩‍💻👨‍💻 profile image](img/0cc738a1b90f8eb9a1c7757065047be3.png)DEV 社区👩‍💻👨‍💻[@ thepracticaldev](https://dev.to/thepracticaldev)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)2 单元测试。0 集成测试2016 年 1 月 14 日下午 16:26[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=687672086152753152)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=687672086152753152)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=687672086152753152)

集成测试的目的是检查您的代码总体上是否有意义，是否做了预期的事情。

那么，单元测试的目的是什么呢？

单元测试大多数人认为它们的存在是为了确保类之间的契约，他们是对的，但是这些测试也允许开发者通过监听测试来确保代码的质量。

## 听测试。

假设没有意外的复杂性，一个单元测试不应该花费超过三五行代码和几分钟来编写。如果你的测试很长或者很难写，这是一个耦合的信号。

测试通常有点像“给 ClassA.cat()一个 X，它会用 X*Y 调用 ClassB.fox()，然后你写调用和断言，这取决于哪种语言和框架，这需要两行或三行。如果 ClassA.cat()做的不仅仅是乘法，那么你的测试将会变得越来越难，因为 ClassA 做的事情越多，你就需要拿出输出来进行断言，如果你的代码做了很多事情，确保断言值将会变得更加困难。

## 将 TDD 应用于每日代码。

红绿重构很棒，但并不适合所有人，也不适合所有场景。

我是这样做的:我写了一个测试，上面写着“ClassA.cat()调用 ClassB.fox()”。我将这些测试称为设计测试，并使用它们来构建类的一般结构。然后，我编写指定接收的值和预期输出的类，并设计哪个类做什么的细节，在这个阶段，通常设计测试会被修改，因为存在一些隐藏的耦合。

在这些愉快的测试之后，我编写了一些测试来处理错误，还编写了一些其他值的测试来确保不会发生奇怪的事情。然后，我编写集成测试。

差不多就是这样。还有，对不起我的英语。