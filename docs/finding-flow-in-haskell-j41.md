# 在 Haskell 中寻找流

> 原文：<https://dev.to/benrcongdon/finding-flow-in-haskell-j41>

在过去的几个月里，我一直在断断续续地用 Haskell 编写我的编程语言课程。我以前写过我对声明式函数语法的偏好，但是我从来没有真正花时间去学习一门纯函数式 Programming™️语言。

**TL；博士:**我很高兴我学会了 Haskell。它非常擅长表达一些用其他语言很难(或者实际上不可能)表达的程序。然而，这必然使其他类型的程序难以用 Haskell 编写。

请记住，我已经学到了足够多的 Haskell 来“变得危险”(既指足够舒适地编写适度复杂的程序，也指我足够天真地可能被误导)。对此不能全信。

### 入口点:高阶函数

我想起了我第一次遇到看起来有点像函数式编程的东西。我觉得第一次互动大概是在学 Javascript 的`Array.{map, reduce, filter}`的时候。这些高阶函数，之所以这么叫是因为它们将一个函数作为参数，让你以一种完全不同的方式思考程序的执行。

内化高阶函数的概念需要与学习递归类似的思维跳跃。如果您遵循学习编程的“传统”命令式路径，递归是一个常见的绊脚石，因为它迫使您不仅仅将函数视为过程，而是可以在其内部调用的抽象过程。高阶函数强加了一个类似的绊脚石:您必须将函数视为数据。就像你操作和传递变量一样，你也可以组合和传递函数。

一旦熟悉了`map`、`reduce`、`filter`等等，您就可以使用 HOFs 表达大量的功能。Ruby 和 Rust 中我最喜欢的部分就是它对这一小部分 Hof 的支持。

### 哈斯克尔不是`Array.map`，而是“更”

从上一节中，您可以看到我最初的函数式编程思维模型相当狭窄。就是它让你可以像处理变量一样处理函数… 嗯，有点。当然，关于函数式编程的想法也引出了“没有副作用”和“一切都是函数”的必然想法

哈斯克尔确实大量使用了霍夫。它使用了很多函数(尽管[并不是所有东西都是函数](http://conal.net/blog/posts/everything-is-a-function-in-haskell)),事实上，在 Haskell 程序中很难出现无意的副作用。但是，把 Haskell 说成“如果我们采用`Array.map`并围绕它开发一种语言会怎么样”是没有意义的。

我认为 Haskell 的独特和有用之处在于一组完全不同的特性:

*   模式匹配
*   不变
*   代数数据类型

仅这三个特性(以及一些必需品，如功能组合)就已经为尝试 Haskell 提供了一个令人信服的理由。

#### 模式匹配

一流的[模式匹配](http://learnyouahaskell.com/syntax-in-functions)可能是我在 Haskell 中最喜欢的事情。举下面的例子:

```
mySearch :: Eq t => [t] -> t -> Bool
mySearch (x:xs) target = (x == target) || mySearch xs target
mySearch [] _ = False

>>> mySearch [1,2,3] 2
True
>>> mySearch [1,2,3] 4
False
>>> mySearch [] 10
False 
```

`mySearch`检查一个元素(`target`)是否出现在列表中(这是第一个参数)。第一行模式匹配非空列表。如果列表非空，`x`是列表中的第一个元素，`xs`是列表的其余部分。如果列表为空，将使用第二种情况(`[]`模式匹配空列表)，我们返回`False`。

列表上的模式匹配本身就很棒。您还可以对特定值和代数数据类型进行模式匹配。

```
isMeaningOfLife :: Maybe Int -> Bool
isMeaningOfLife (Just 42) = True
isMeaningOfLife _ = False

>>> isMeaningOfLife Nothing
False
>>> isMeaningOfLife (Just 42)
True 
```

当然，这只是触及了可能性的表面。您可以独立地对每个变量进行模式匹配，并且对于一个给定的函数有大量不同的情况。这在更复杂的程序中变得非常强大，并且是将复杂函数分解成简单函数的一种很好的速记方式。(很多用 Haskell 写的递归函数感觉像写数学证明；“有 3 种情况需要考虑…”)

关于为什么模式匹配在 Haskell 中如此重要，我最喜欢的理论是，对于纯函数式程序来说，函数的输出与其输入绑定得更紧密。由于副作用是被禁止的，一个函数真正能做任何事情的唯一方法就是返回一些东西。类似地，函数式编程在语法上更具声明性。模式匹配使得*编写声明性纯函数变得更加容易*。模式允许你在不同形式的输入下描述一个函数的行为，而不需要太多的语法模板。

#### 永恒性

Haskell 表达式是不可变的。一旦你构造了一个数据，它就不能被改变。更改数据(即追加到列表、向字典添加关键字等)。)创建新的数据结构。这防止了大类的错误。你永远不需要关心其他线程或函数改变数据结构的内部状态，因为，嗯，*它们不能*。

其他语言社区也采用了不变性，或者作为库(特别是 Google 的 Java 的 [Guava](https://github.com/google/guava) 和 Javascript 的 [immutable-js](https://github.com/immutable-js/immutable-js) )或者作为语言默认设置(比如 Rust)。

不变性可能是一个很难处理的问题。(即“我只想进行就地数组操作！”)然而，我认为 Haskell 基本上逃脱了惩罚，因为它在任何地方都使用了不变性。当改变内部状态不是一个选项时，它就变得不那么诱人了——你开始以不需要可变性的方式思考。

#### 代数数据类型

“代数数据类型”是那些听起来可怕的短语之一，但解释起来却没有什么害处。

简单地说，有*乘积类型*(类似于元组或结构)和*总和类型*(有点像带有关联值的`enum`)。

```
-- Example Product Type
data Point = Point Int Int

-- Example Sum Type
data MyMaybe a = Nothing
               | Just a 
```

产品类型`Point`是包含 2 个整数的类型。例如，我们可以将原点表示为`(Point 0 0)`。

Sum 类型类似于枚举，因为给定的类型有一组有效的符号形式。`MyMaybe`在类型`a`上被参数化(就像 OOP 语言中的`List<T>`)。它可以具有`Nothing`或`Just a`的形式。这是描述可选值的一种有用方式。例如，如果我们想要描述一个可选的整数，我们可以有一个函数，如果操作成功，返回`Just 42`，否则返回`Nothing`。

Sum 类型特别强大，因为它们可以很容易地用来表示语法树:

```
data Exp = IntExp Int
         | PlusExp Expression Expression
         | IfExp Exp Exp Exp
         | GreaterThanExp Exp Exp

>>> parse "if 1 > 0 then 2 else 3"
(IfExp (GreaterThanExp (IntExp 1) (IntExp 0)) (IntExp 2) (IntExp 3)) 
```

请注意，sum 类型可以是递归的。在`PlusExp`中有 2 个子表达式，每个都有自己的内部语法树。

回想一下，我们可以在代数数据类型上进行模式匹配，这意味着如果我们想为如上所示的语法树编写一个求值器，我们可以非常容易地做到:

```
eval :: Expression -> Int
eval (IntExp i) = i
eval (PlusExp e1 e2) = v1 + v2 where
    e1 = eval e1
    e2 = eval e2
... 
```

我非常喜欢在 Haskell 中使用 ADT(尤其是结合模式匹配)。它们是表示特定类型数据的一种令人惊讶的好方法，它允许类型检查器代表您对程序的正确性进行大量的推理。

Rust 的[类型枚举](https://doc.rust-lang.org/rust-by-example/custom_types/enum.html)和 Swift 的[关联值枚举](https://docs.swift.org/swift-book/LanguageGuide/Enumerations.html)让我深受鼓舞，这两个枚举在非功能设置中提供了相似的功能。

### `<*>``<$>``>>=`【啊呀！

因此，随着关于 Haskell 的滔滔不绝的讨论结束，我应该提到一些让我感到沮丧的地方。

Haskell 语法肯定是由 C 派生的，所以重新学习基本语法会有一点负担。然而，当你开始稍微深入 Haskell 的语言特性时，真正的奇怪之处就来了。

对于其他语言的程序员来说，Haskell 的类型系统的基础应该是熟悉的。函数接受类型化的输入并产生类型化的输出。然而，也有[类型类](http://learnyouahaskell.com/types-and-typeclasses)，有点像类型的协议或特征。

常见的类型类有[函子](https://wiki.haskell.org/Functor) / [应用型](https://wiki.haskell.org/Applicative_functor)、[可折叠型](https://wiki.haskell.org/Foldable_and_Traversable)和[单子型](https://wiki.haskell.org/Monad)。由于这些类型类经常使用(并且有经常使用的相关操作)，Haskell 有许多语法操作符，允许您轻松地调用类型类函数。`<$>`与函子的`fmap`相同，`>>=`是单子的`bind`。还有更多的操作符，包括我到目前为止提到的每一个操作符的一些变体。结果可能有点符号汤，很难一眼读懂。

我的意思是，这是一个非常现实的问题(如果是开玩笑的话)，[“你怎么读`<*>`？”](https://stackoverflow.com/questions/3242361/haskell-how-is-pronounced)

值得称赞的是，Haskell 有一个非常优秀的 REPL，当你感到困惑时，可以向它询问操作符的类型签名。

```
>>> :t (>>=)
(>>=) :: Monad m => m a -> (a -> m b) -> m b 
```

还有一种担心是，有些类型的程序不适合用函数式的方式编写。对于任何涉及超级繁重的系统编程、文件 IO 或 GUI 交互的东西，我不认为我会想使用 Haskell。

我也对 Haskell 中[调试的故事感到有点惊讶。这……并不惊人(在我看来)。您仍然可以使用](https://wiki.haskell.org/Debugging) [`Debug.Trace`](http://hackage.haskell.org/package/base/docs/Debug-Trace.html) 进行`printf`调试，但是将`trace`作为一个垫片放在函数调用之间感觉比`printf`语句更突兀。值得称赞的是，Haskell 的 REPL 非常棒，因此您可以在那里轻松调试较小的组件。GHCI (Haskell 的 REPL)也支持断点。不过可以肯定地说，就像你在学习 FP 时需要重新学习编程的部分内容一样，你也需要重新学习调试的部分内容。

### 暂且不提:范畴/类型理论

作为一个题外话，人们不禁会注意到，一般的 Haskell 程序员似乎比一般的 Python 开发人员对类别和/或类型理论(还有形式方法)更感兴趣。

类型理论一直是我“感觉”应该感兴趣的东西，但更多的是从知识的角度，而不是出于任何实际的原因。

冒着听起来不屑一顾的风险，我认为你可能会忽略大多数关于"[单子只是内函子](https://stackoverflow.com/questions/3870088/a-monad-is-just-a-monoid-in-the-category-of-endofunctors-whats-the-problem)范畴中的幺半群"的讨论，并且在 Haskell 中仍然相处得很好。

## 结论

另外，我发现 Haskell 的编程体验真的很令人愉快。当在一个非常适合 Haskell 优势的领域工作时，我发现很容易进入一种流畅的状态。我不觉得我是在和类型检查器战斗；更确切地说，感觉它让我保持诚实。

我确信这并不能一概而论，但是当我在 Haskell 中工作时，我感觉如果我的代码通过了类型检查，我有理由相信我写了一个“正确的”程序。许多人对 Rust 的类型检查器也有这种感觉。很清爽的感觉。当使用 Go 或 Python(或 Javascript，就此而言)工作时，编写代码感觉就像是战斗的上半场——您需要在运行时练习它，以建立对其正确性的信心。当然，语义错误在 Haskell 中很容易犯。但是，作为一个基线，我对“一次性”Haskell 代码比对用其他语言编写的代码更有信心。

我很高兴我花时间学习了 Haskell。它从一个与我以前使用的语言非常不同的角度来编写程序，它使我更加适应类型系统的一些方面，这些方面在非函数式语言中不容易探索。

### 进一步阅读

*   [函数式编程简介(Haskell Wiki)](https://wiki.haskell.org/Functional_programming#Immutable_data)
*   [不变性太棒了(周一早晨哈斯克尔)](https://mmhaskell.com/blog/2017/1/9/immutability-is-awesome)