# 最好的软件工程原理是什么？

> 原文：<https://dev.to/luminousmen/what-are-the-best-software-engineering-principles--3p8n>

软件开发原则——它是一些具体的规则和建议，如果工程师们想写出漂亮的、可理解的和可维护的代码，他们需要在程序实现过程中遵循这些规则和建议。没有一根魔棒可以让你将变量、类和函数的组合转换成理想的代码，但是有一些技巧和提示，可以帮助工程师确定他做的事情是否正确。

让我们看看这个基本建议。下面的一些原则是 Python 特有的，但大多数不是。

## 量两次切一次

我认为这是最重要的原则。如果你从这篇文章中学到了一个原则，应该是这个。我们，~~开发人员/开发人员/架构师/经理~~人们挣扎于缺乏关注、愚蠢的错误和错别字、个人问题、坏心情和冷咖啡。这些都无关紧要——你需要解决一个问题。对作为工程师的我来说，这个原则意味着**选择正确的问题来解决，选择正确的方法来解决问题，选择正确的工具来解决问题，对构建的解决方案有信心**。

但是这种信心不能来自代码本身，它应该来自[各种测试](https://luminousmen.com/post/ode-to-unit-tests)，来自[经常运行它们](https://luminousmen.com/post/continuous-Integration-continuous-delivery)，来自[为问题建立正确的解决方案](https://luminousmen.com/post/basic-architecture-post)等等。这就是工程本身。我想我自己还没有准备好用恰当的语言来描述它。

## 不要重复自己([干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

这是一个非常简单但非常有用的原则，即在不同的地方重复相同的代码是一个坏主意。这主要是因为需要进一步维护和修改代码。如果一些代码片段在程序中的几个地方重复，那么很可能会出现两种灾难性的情况:

1.  即使对源代码做很小的修改，也需要在几个地方修改相同的代码。这需要额外的时间、努力和注意力(有时这并不容易)
2.  第一项接着第二项。您或您团队中的其他开发人员可能会意外错过其中一个修复，并面临应用程序中的后续错误。这些错误可能会令人沮丧，因为你听说这样的错误已经被修复。

在这方面，有一个建议——如果任何代码在清单中出现两次以上，那么它应该放在一个单独的方法中。这是一个一般性的建议，事实上，你需要考虑创建一个单独的方法，即使你第二次遇到重复。

style = " display:block；文本对齐:居中；"
data-ad-layout = " in-article "
data-ad-format = " fluid "
data-ad-client = " ca-pub-4665632381152577 "
data-ad-slot = " 7970039881 ">

## 奥卡姆剃刀

这是一个非常普遍的想法，它来自于哲学。这一原则得名于奥卡姆的英国修道士威廉。这个[原理说](https://en.wikipedia.org/wiki/Occam%27s_razor):没有必要，实体是不会被相乘的。在工程中，这个原则被解释为——不需要创建不需要的额外实体。所以，你总是需要首先考虑增加一个方法/类/工具/过程/等等的好处。毕竟，如果你增加了一个方法/类/工具/过程/等等，除了增加复杂性之外没有任何好处，那还有什么意义呢？

## 保持简单愚蠢([吻](https://en.wikipedia.org/wiki/KISS_principle))

[![KISS](img/f33f6c110e9685ced62bac7131c3cbba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e4PrVLwp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1p7haqe0wki9x51irqno.jpg)

这是一个非常相似的原则，但它有一个稍微不同的含义。这个原则要求代码应该简单，如果可能的话不要有任何复杂的结构，否则会使代码的调试和维护过于复杂。此外，对于另一个程序员来说，理解代码逻辑会更加困难，这反过来也需要额外的时间和精力。因此，总是尽可能地使用简单的构造来解决问题，而不需要大量的分支、深度嵌套和过度工程化的类结构。通过这样做，你将为你自己和你的同事简化生活，因为复杂会滋生错误。记住彼得·欣金斯说过的话:“简单永远比功能性好。”

## 你不会需要它的( [YAGNI](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it)

许多程序员都有这个问题。从开发过程的最开始就希望立即实现所有必要的(有时甚至是不必要的)功能。也就是说，开发人员从一开始就将所有可能的方法添加到类中并实现它们，而在将来甚至可能永远不会使用它们。因此，根据这个建议，首先只实现您需要的功能，将来如果需要，再增加功能。这样你就可以节省调试代码[的精力、时间和精力，而这些代码并不是真正需要的](https://en.wikipedia.org/wiki/Worse_is_better)。

## 前期大设计

在开始开发功能之前，有必要首先考虑[应用程序架构](https://luminousmen.com/post/basic-architecture-post)并设计整个信息系统，直到相当小的细节，然后才根据之前准备的计划实施。[原则](https://en.wikipedia.org/wiki/Big_Design_Up_Front)有存在的权利，但最近对他的批评颇多。这主要是由于在设计和开发期间计划的过时。在这方面，您仍然需要进行后续更改。但它也有无可辩驳的优势，通过适当的设计，您可以显著降低进一步调试和修复错误的成本。此外，这样的信息系统通常更简洁，在架构上也更正确。

## 避免过早优化

> “过早的优化是编程中所有罪恶(或者至少是大部分罪恶)的根源”——唐纳德·克努特

优化是一个非常正确和必要的过程，它可以加快程序的工作速度，同时减少系统资源的消耗。但凡事都有它的时间。如果在开发的早期阶段进行优化，那么**弊大于利**。这主要是因为优化代码的开发需要开发人员花费更多的时间和精力，并且可能会更加复杂。在这种情况下，通常有必要首先验证所选择的开发方法的正确性。因此，一开始，使用简单但不是最优的方法更有利可图。在未来，评估这种方法在多大程度上降低了应用程序的工作速度，转向一种更快或资源更少的算法。此外，在最初实现最优算法的时候，需求可能会发生变化，代码会被丢弃。所以没必要在过早优化上花时间。

## 最小惊讶原则

这个原则意味着你的代码应该是直观和明显的，并且在代码审查期间不会让另一个开发人员感到惊讶。例如，如果这个方法叫做“制作 cookies”，但结果你得到的是一个比这个代码更坏的土豆(很明显)。

style = " display:block；文本对齐:居中；"
data-ad-layout = " in-article "
data-ad-format = " fluid "
data-ad-client = " ca-pub-4665632381152577 "
data-ad-slot = " 7970039881 ">

## S.O.L.I.D

[![SOLID](img/badd757753c3c6b5c13bcef679015682.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y7NIq2WP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/agulsg1y21onynqekm7b.jpg)

“ [SOLID](https://en.wikipedia.org/wiki/SOLID) ”其实是一组面向对象的设计原则。“SOLID”中的每个字母代表一个原则，即:

*   [单一责任](https://en.wikipedia.org/wiki/Single_responsibility_principle)声明每个模块或类应该对软件提供的功能的单一部分负责，并且责任应该完全由类封装；
*   [开-关](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)表示软件实体(类、模块、函数等。)应该对扩展开放，但对修改关闭；
*   [Liskov 替换](https://en.wikipedia.org/wiki/Liskov_substitution_principle)声明继承的类应该补充，而不是替换基类的行为；
*   [接口分离](https://en.wikipedia.org/wiki/Interface_segregation_principle)声明不应该强迫任何客户端依赖它不使用的方法；
*   [依赖倒置](https://en.wikipedia.org/wiki/Dependency_inversion_principle)说程序员应该在接口层工作，而不是在实现层。

当一起应用时，这些原则帮助开发人员创建易于维护和随时间扩展的代码。

## 德米特里定律

[这个原则](https://en.wikipedia.org/wiki/Law_of_Demeter)的主要思想是在类之间划分责任区域，并将逻辑封装在类、方法或结构内部。根据这一原则，我们可以强调几项建议:

1.  类或实体应该是独立的
2.  需要尽量减少不同类之间的连接数量(又名[耦合](https://en.wikipedia.org/wiki/Coupling_(computer_programming)))
3.  相关的类应该在同一个模块/包/目录中(又名[内聚](https://en.wikipedia.org/wiki/Cohesion_(computer_science)))

通过遵循这一原则，应用程序变得更加灵活、易于理解和维护。

## 结论

开发伙伴们，让我们成为工程师吧！让我们考虑设计和构建健壮且实现良好的系统，而不是生长有机怪物。列出的原则在本质上是高度相关和相连的。当然不是我创造的，但是一个小提醒不痛不痒，至少我的记忆力肯定不是很完美。

* * *

**感谢您的阅读！**

有什么问题吗？请在下面留下您的评论，开始精彩的讨论！

查看我的博客或来打个招呼👋在[推特](https://twitter.com/luminousmen)或订阅[我的电报频道](https://t.me/iamluminousmen)。
做好你的计划！