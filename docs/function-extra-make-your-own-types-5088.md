# 制作你自己的类型

> 原文：<https://dev.to/drbearhands/function-extra-make-your-own-types-5088>

*TL；DR:创建非常具体的类型，这样就没有人会对它们使用错误的函数。*

如果您在 Elm 社区呆过一段时间，您会知道创建自己的枚举类型比使用现有的同构类型更好。例如，如果你有一种可以是黑色或白色的颜色，习惯上会创造一种新的类型:

```
type Color = Black | White
[...]
color : Color 
```

Enter fullscreen mode Exit fullscreen mode

而不是使用标准的布尔类型:

```
colorIsBlack : Bool 
```

Enter fullscreen mode Exit fullscreen mode

尽管后者需要更少的代码行。

我甚至不得不查找 Elm 是否使用了`Bool`或`Boolean`,因为我从来没见过它。

这样做的主要原因是它使代码更容易理解，但这不是唯一的理由。

在 [functional fundamentals](https://dev.to/drbearhands/a-series-on-functional-fundamentals-48mb) 系列中，我已经[提到过](https://dev.to/drbearhands/functional-fundamentals-types-as-propositions-programs-as-proofs-56gh)函数类型系统如何证明函数*将*返回一个特定的类型，例如一个错误。

如果你对同构数据使用相同的类型，例如，在上面的例子中，你使用一个布尔值而不是一个颜色，那么这个证明只能支持数据同构，而不能支持语义。

一个有点做作的例子:

如果你考虑相对论，你不应该把两个速度相加(光速是最大值等等)。但是如果你只是用`Float`作为速度的类型，你就有一个这样的运算正确性的证明。事实上，就数据同构而言，它*是*正确的，但就语义而言是不正确的。

相反，你可以定义你自己的速度类型和函数。在 Elm 中看起来有点像这样:

```
module RelativityTheory exposing (Velocity, Energy, accelerate)

type Velocity = Velocity Float
type Energy = Energy Float

accelerate : Energy -> Velocity -> Velocity
[...] 
```

Enter fullscreen mode Exit fullscreen mode

通过不暴露`Velocity`的内部结构，就像你暴露`Velocity(...)`一样，你可以防止你或你的队友将来犯任何错误。除非，你知道，他们足够努力，实际上修改了说明。

这个概念类似于 OOP 原理中的封装。

这些错误经常发生的更常见的例子是:距离是用千米、英里、米还是英尺来表示的？旋转是以度、半径还是以圈为单位？这个字符串的编码是什么？

语义类型区分可以帮助解决这些问题。