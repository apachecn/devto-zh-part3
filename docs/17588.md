# 函数式编程中的抽象层次

> 原文：<https://dev.to/tzemanovic/abstraction-levels-in-functional-programming-5cd2>

*2018 年 12 月 29 日更新*:我曲解了实现居民的意思，所以把讲它的那句话划掉了。谢谢布莱恩。

教授和学习函数式编程可能具有挑战性。许多人已经有了一些编程的经验，这通常与 FP 概念有很大的不同。与其他语言的类比有时弊大于利(你可能听说过当你开始学习 FP 时，你必须“忘记”一些东西)。我很高兴看到越来越多的人学习函数式编程，并发现让更多的人可以使用函数式编程的努力令人鼓舞。我认为榆树在其中有很大的作用。

我是 Brian McKenna 与 Haskell 在这方面所做工作的粉丝(我个人最喜欢的是他在 Haskell 构建 Sonic 的流)。他最近关于[高级类参数化](https://brianmckenna.org/blog/higher_kinded_parametricity)的帖子让我思考抽象及其在 FP 教学中的作用。该参数涉及函子类型 class 和下面的函数(不要与起源于 Algol，C 并在许多流行的 OOP 语言中发现的 void 类型混淆):

```
void ::
  Functor f =>
  f a
  -> f () 
```

Enter fullscreen mode Exit fullscreen mode

对于任何精通 Haskell 的人来说，这是一个非常基本的例子，因为 Functor 是一个出现在很多地方的基本构件。对于那些不太熟悉的人来说，这可能会带来许多新的挑战。

打开 [hoogle](https://www.haskell.org/hoogle/) ，你可能会找到[函子](http://hackage.haskell.org/package/base-4.12.0.0/docs/Prelude.html#t:Functor)的定义。

> 函子类用于可以映射到…

好的，Functor 是一个类，但是这些类和 OOP 类完全不同。假设您对`Maybe`数据类型(在一些语言中称为可选)的概念有所了解，那么如果我们从抽象阶梯的底层开始，一路向上，会发生什么呢？

Haddock 告诉我们，比如 void 用 unit 代替了一个`Maybe Int`的内容(unit 用`()`表示)。在一种基本形式中，我们可以把这表达为:

```
void :: Maybe Int -> Maybe ()
void Nothing = Nothing
void (Just _) = Just () 
```

Enter fullscreen mode Exit fullscreen mode

但是对于这个函数来说，参数是`Maybe Int`、`Maybe Bool`还是`Maybe a`并不重要，所以我们引入了参数多态:

```
void :: Maybe a -> Maybe ()
void Nothing = Nothing
void (Just _) = Just () 
```

Enter fullscreen mode Exit fullscreen mode

以小写字母开头的类型是多态的，这意味着我们可以将另一个类型放在它的位置上。事实上，如果我们将`a`替换为`Int`，我们将得到与前一级完全相同的结果。

接下来，我们可以展示对`Maybe`类型的操作是很常见的，我们用它的内容做一些事情，所以我们定义:

```
fmap :: (a -> b) -> Maybe a -> Maybe b
fmap _ Nothing = Nothing
fmap f (Just a) = Just (f a) 
```

Enter fullscreen mode Exit fullscreen mode

第一个参数接受应用于第二个参数内容的函数(如果它有任何内容)。

我们现在可以用它来实现我们的 void 函数:

```
void :: Maybe a -> Maybe ()
void = fmap $ const () 
```

Enter fullscreen mode Exit fullscreen mode

在 Elm 中，这是我们到达抽象天花板的地方(注意，在 Elm 中，哈斯克尔的`const`被命名为`always`，`$`被命名为`<|` ) :

```
void : Maybe a -> Maybe ()
void = Maybe.map <| always () 
```

Enter fullscreen mode Exit fullscreen mode

或者对于`List`数据类型:

```
void : List a -> List ()
void = List.map <| always () 
```

Enter fullscreen mode Exit fullscreen mode

您必须为您想要使用该函数的每种数据类型实现这一点，这可能很繁琐。但是，这真的比在 Haskell 中更复杂吗？~~我们对于`Maybe`的`void`实现仍然只有一个居民，对于`List`也是如此。~~

这是否会阻止 Elm 变得不可思议地实用，或者这可能是当前最著名的 web 库 React 的一个可行的替代方案？我有理由认为它在许多方面都超过了 React。那些使用 Elm 的人会因为能够使用纯 FP 构建伟大的东西而感到鼓舞。

当然，Haskell 可以将抽象提升到另一个层次，这要感谢当今的传奇人物 Philip Wadler 和 Stephen Blott 在 1988 年首次引入的类型类的奇妙概念，作为驯服特定多态性的一种方式:

```
void :: Functor f => f a -> f ()
void x = () <$ x 
```

Enter fullscreen mode Exit fullscreen mode

它可以走得更远。毕竟，类型类是建立在数学语言范畴理论之上的。但是，即使是数学家也可能与抽象斗争。我推荐 Eugenia Cheng 的作品，她用最通俗易懂的方式解释了数学和范畴理论。下面这段话来自她的*如何烘焙圆周率:一个可食用的数学探索*。

> 高级数学家有时达到其抽象极限的时刻是范畴理论。当青少年需要 x 和 y 的时候，他们的反应非常类似，他们说他们不明白这一点，并抵制任何进一步的抽象。

作为程序员，我们很幸运有学习这些概念的背景。

如果类型类是工具箱的重要组成部分，那么您可能会发现使用 Elm 限制。然而，对于新手来说，这可能是一个可怕的领域，所以有同情心是很重要的，尤其是如果你使用 Haskell 教授 FP 的话。不管怎样，这不应该是唯一的选择。如果 Elm 开发人员需要进一步追求抽象，他们肯定会回到 Haskell、PureScript 或其他更高级的类型系统。但是我希望 Elm 将来会发展到实现类型类。

我们都有不同的最有效的学习方法。你之前的经历对你学习函数式编程有影响吗？如果计划生育社区在教授计划生育的共同目标上更加团结，不是更有建设性吗？请分享你的想法。

* * *

1.  [超快的 html](https://elm-lang.org/blog/blazing-fast-html-round-two)↩

2.  [小资产无头痛](https://elm-lang.org/blog/small-assets-without-the-headache)↩

3.  [程美琪著](http://eugeniacheng.com/math/books/)