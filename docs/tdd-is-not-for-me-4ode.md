# TDD 不适合我

> 原文：<https://dev.to/joncalhoun/tdd-is-not-for-me-4ode>

> *这篇文章最初发表在 [calhoun.io](https://www.calhoun.io/tdd-is-not-for-me/) 上，在那里我写了关于围棋、web 开发、测试等等。*

这将是我第二次写关于测试的一个不受欢迎的观点，我相信这会引起一些不满。**我并不总是实践测试驱动开发**(也称为 TDD)，**我相信在很多情况下，实践 TDD 更多的是一种阻碍而不是一种好处。**

一会儿我将探讨为什么我不总是实践 TDD，但我想事先明确，这不是一篇抨击 TDD 的文章。实际上，我认为学习和尝试 TDD 对开发人员来说是非常有益的。TDD 迫使开发者从另一个角度思考开发和设计；而不是关注“我该如何实现它？”相反，他们需要退一步思考，“我该如何使用这个函数？”虽然这种变化看起来很小，但它可以导致代码库的巨大改进。

那么，我为什么要写这篇文章呢？

我写这篇文章是因为有许多开发人员在与 TDD 作斗争，结果感觉很糟糕。无论是冒名顶替综合症还是仅仅感觉他们有一个肮脏的秘密需要隐藏，这些感觉存在于比大多数人意识到的多得多的开发人员中，因为每个人都太担心了，以至于不承认他们不能让 TDD 为他们工作。当一个开发人员听到其他人谈论 TDD 是如何使他们如此高效的神奇事物时，情况变得更糟。简而言之，有许多程序员觉得自己像废物，他们不应该这样；TDD 是一个很好的学习工具，但是我可以直接说，它并不总是有效的，在很多情况下，它只是阻碍了我快速产生高质量代码的能力。

好的，现在让我们深入了解为什么我不一直练习 TDD。

## TDD 过于关注单元测试

测试驱动开发通常被认为是一个有三个步骤的过程:红色，绿色，重构。

这里的想法很简单:

*   红色步骤是你写了一个会失败的测试，因为它测试了你还没有实现的东西。
*   绿色步骤包括编写代码以通过测试
*   重构步骤是在使用测试确保不破坏任何东西的同时，将代码重构为更易维护的东西。在此步骤中，不添加任何新功能。

我试着严格遵循 TDD，但有时感觉就像是巨大的痛苦；它时不时地让我慢下来，而不是帮助我更有效率。

我真的不知道为什么会这样，直到我读了 Jeffrey Way 的这篇 twitter 帖子:

> ![Jeffrey Way profile image](img/d3b5bd3b14d345669e7e00c353ae4ee2.png)杰弗里之道@杰弗里 _ 之道![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)我敢打赌，由于传统的教学方式，我花了两倍的时间才熟悉 TDD。我读过的所有东西的 90%都集中在单元测试上。20:52PM-04 2019 年 3 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1102673086799855616)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1102673086799855616)38[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1102673086799855616)180

我这里只包括第一条推文，但你应该去读完整条。这很值得你花时间。

杰弗里用语言表达了我已经挣扎了相当长一段时间的事情；当我们如此关注单元测试，而完全忽略每个开发人员迟早都会遇到的更复杂的场景时，TDD 就很难学习和掌握。

在我发现使用 TDD 很麻烦的几乎每一个案例中，它几乎总是源于我试图对一些代码进行单元测试，而我需要模拟天底下的一切。

TDD 应该帮助我们从实现中后退一步，转而关注如何使用代码，但是当我写单元测试时，需要模拟出一堆依赖，这不再是真的。我被迫再次开始考虑实现细节，比如“我的代码需要访问数据库吗？”以及“编码呢？我们需要支持多种输出格式吗？我应该为测试注入一个模拟编码器吗？”

以这种方式思考代码对我来说不太自然。在我开始使用它们之前就开始考虑我将需要什么依赖是没有好处的。相反，当我退一步写一个特性测试时，我工作得更好。类似于，“当我用 JSON `{"price": 123, ...}`到`/orders`路径时，我希望得到下面的 JSON，它的 ID 与`ord_[a-zA-Z0-9]{8,}$`模式匹配。”

这种类型的测试不仅非常容易编写——我们只需启动一个应用程序，点击终点，然后检查结果——而且还能让我回到正确的心态。我现在在想有人会如何实际使用这些代码；我在考虑另一个开发人员与我的 API 交互，或者一个真实的人填写表单并提交它。

这显然也有例外。例如，如果我写一个函数来分解数字，TDD 可能会导致问题的合理解决。这里的关键部分是，我们并没有真正关注模仿和依赖；相反，我们正在编写一个独立的函数，假设我们没有陷入第二个陷阱，即我们必须在任何时候都编写绝对最少的代码，那么 TDD 就可以在这种情况下大放异彩。

我也不是说你不应该在嘲笑的地方写单元测试。这些测试可以在许多环境中提供价值。我只是发现自己在编写单元测试时没有经常实践 TDD。相反，我对我的特性测试进行单元测试，然后按照我认为合适的方式编写单元测试。

## 不是所有的代码都应该一次写一个测试用例。

通常使用以下规则来教授 TDD:

1.  除非是为了通过失败的单元测试，否则不允许编写任何产品代码。
2.  不允许编写任何超过足以导致失败的单元测试；编译失败就是失败。
3.  除了足以通过一个失败的单元测试之外，您不允许编写更多的产品代码。

这些规则是罗伯特·c·马丁(鲍勃大叔)写的，可以在网站上找到:[butunclebob.com](http://butunclebob.com/ArticleS.UncleBob.TheThreeRulesOfTdd)T3】

虽然这在很多情况下都行得通，但我完全不同意所有代码都应该这样写的观点。

我期望在这里发生的是有人链接我一个视频，一个博客，或其他一些例子，开发者使用 TDD 来推导一些复杂的算法。一个流行的例子是[编写一个函数来确定一个数](https://www.youtube.com/watch?v=AoIfc5NwRks)的因子。我也看过作者探索是否有可能通过 TDD 衍生出类似 quicksort 的东西的文章。

当然，如果这些更复杂的算法可以通过 TDD 推导出来，那么它一定在所有情况下都有效，对吗？

虽然 TDD 有时可以用来推导出一个合理的算法，但我也见过无数这样的例子，它以完全相反的方式工作。通过使用 TDD，开发人员得到了一种非常慢、低效的算法，有时甚至会错过边缘情况，因为几乎不可能想到每一种边缘情况，更不用说测试所有情况了。

事实是，在有些情况下，您实际上需要坐下来考虑一个函数的输入和输出，而不仅仅是期望的输入和输出。最容易掌握的例子可能是排序算法——虽然您可能从 TDD 方法中得到 quicksort，但是您也可以很容易地得到一个更慢、效率更低的算法。即使你想出了一个有效的快速排序，大多数标准库使用比这稍微复杂一点的东西，因为对较小的列表使用快速排序不是那么有效。

除了算法之外，有时不断的上下文切换只会损害整体生产力。虽然对于许多开发人员来说，不断地编写一个测试，然后一两行产品代码，再次测试，重构，然后重复可能会很好，但我个人认为这种不断的上下文切换会分散注意力。我发现当我:

1.  编写几个测试用例，演示我期望的基本功能。
2.  花点时间想想我该如何实现这个功能。
3.  实现一个粗略的版本，让我的测试通过。
4.  必要时重构。

这与 TDD 非常相似，但又不完全相同，我确信如果我按照我的 TDD 版本来教它，许多人会告诉我我“做错了”。🤷‍♂️

## 包装完毕

我发现 TDD 有时是有益的，我并不是说我们应该放弃它。相反，我试图传达的是，陷入这一套定义什么是 TDD，什么不是 TDD 的严格规则是一个错误。

相反，我们应该从 TDD 中吸取教训，并以对我们自己最有效的方式应用它们。如果这意味着我们最终会违反一些规则，那就这样吧。毕竟，TDD、敏捷或者任何开发过程的目标都是让我们更好地工作，如果他们没有这样做，那么有些事情需要改变。

## 想学围棋？

对学习或练习围棋感兴趣？查看我的免费课程- [Gophercises -萌芽中的地鼠编程练习](https://gophercises.com)。

我还有一些高级课程，涵盖了用 Go 进行 [Web 开发和用 Go](https://www.usegolang.com/) 进行[测试，你也可以去看看。](https://testwithgo.com/)