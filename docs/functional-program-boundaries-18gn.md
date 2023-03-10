# 功能程序边界

> 原文：<https://dev.to/drbearhands/functional-program-boundaries-18gn>

这篇文章是“功能基础”系列文章的一部分。参见[简介和索引](https://dev.to/drbearhands/a-series-on-functional-fundamentals-48mb)。这也是你可以要求特定主题的地方

注意，我不是这些主题的权威人物，如果你看到你认为可能不正确或不完整的东西，请用评论指出来，让我们防止错误扩散:-)

* * *

到目前为止，您应该已经对(纯)函数式编程的优势有了很多了解:

*   安全组合(容易重构，保证集成，*真正的*单子...)
*   强类型系统(没有运行时错误，没有[后门](https://dev.to/ben/npm-package-discovered-to-have-bitcoin-stealing-backdoor-j7i)
*   显式数据依赖性(适用于各种优化)

现在是时候谈谈这个陷阱了。
功能代码不做任何事情。它只是一个值。甚至`readLn "Hello, World!"`(Haskell 中的`print("Hello, World!")`等价物)也只是一个`IO ()`类型的*值*。

程序需要被评估，至少需要被应用到输入中。它需要与世界互动，不幸的是，这是没有功能的，至少不是以对我们有用的方式。

在发生这种情况的地方，我们有一个函数式程序边界，在这里函数式世界结束，命令式世界开始。Haskell 使用`do`块*跨越了这一界限(编辑:这不完全准确，见评论)*，Elm 拥有特定于网站的 Elm 架构。

在边界上，运行时异常变得可能，组合变得重要，类型系统通常更加通用。

界限越大，函数式编程的用处就越小。

然而，界限是可以改变的。函数式编程只关心数据转换，而不太关心数据转换发生在哪里或者何时发生。

大数据应用利用了这一点，将边界扩展到计算机网络，而不是单台计算机。AWS lambda 和 Google Functions 使用了类似的概念，将命令式程序视为纯函数。事实上，在数组、列表或字典上映射一个纯函数是令人尴尬的并行*。*

 *相反，Elm 使用 TEA，通过将问题分割成功能部分，将命令胶抽象成命令和消息，隐藏了命令世界。这样，程序员只关心功能性的东西。

简而言之:函数式语言、库或框架如何表示一个问题会极大地影响你的函数边界有多大，通常边界越小越好。

## 进一步阅读:

*   [为什么哈斯克尔没有接管世界](https://pchiusano.github.io/2017-01-20/why-not-haskell.html)
*   [测试中的边界](https://www.destroyallsoftware.com/talks/boundaries)*