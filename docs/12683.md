# 剖析一个类型类

> 原文：<https://dev.to/mmenestret/anatomy-of-a-type-class-440j>

[查看我博客上的文章[这里](http://geekocephale.com/blog/)

我将尝试在解剖图谱中，将我最近在一系列文章中经常解释的函数式编程的基本概念进行分组。

这里的想法是为需要解释的人提供一个位置，并通过尽我所能解释它们来增加我自己对这些主题的理解。我会努力让读者感受到一种直觉，一种对概念的感觉，而不是我的解释的完美、严格的正确性。

*   第一部分:[剖析函数式编程](https://dev.to/mmenestret/anatomy-of-functional-programming-1bpg)
*   第二部分:[解析一个代数](https://dev.to/mmenestret/anatomy-of-an-algebra-3cd9)
*   第三部分:[解剖一个铅字类](https://dev.to/mmenestret/anatomy-of-a-type-class-440j)
*   第四部分:[半群和幺半群的解剖](https://dev.to/mmenestret/anatomy-of-semigroups-and-monoids-22i8)
*   第五部分:[函子剖析与范畴理论](https://dev.to/mmenestret/anatomy-of-functors-and-category-theory-2gf0)
*   第 6 部分:剖析无标签的最终编码——即将到来！

# 动机

## 数据/行为关系

对于数据/行为关系，OOP 和 *FP* 有两种不同的方法:

*   *面向对象编程*通常**通过将数据和行为**混合成类来组合它们:
    *   将数据存储为内部状态
    *   公开作用于它并可能使它变异的方法
*   *函数式编程*旨在通过以下方式将数据与行为完全**分离:**
    *   在一侧定义类型( [ADT](https://dev.to/mmenestret/anatomy-of-an-algebra-3cd9) ，不公开任何行为，只保存数据
    *   函数将这些类型的值作为输入，作用于它们并输出这些类型的值(保持输入值不变)

你可以查看[对函数式编程的剖析](https://dev.to/mmenestret/anatomy-of-functional-programming-1bpg)中的例子。

## 多态性

*多态*的总体思想是通过使用更通用的代码来增加代码的重用。

有几种多态性，但由*类型类*处理的是*特别多态性*，所以我们将重点放在那一种上。

*特定多态性*由以下人员在*维基百科*上定义:

> 特殊多态是一种多态，其中多态函数可以应用于不同类型的参数，因为多态函数可以表示许多不同的和潜在异构的实现，这取决于它所应用的参数的类型

它是一种机制，允许以这样的方式定义函数，即实际的*函数行为*将取决于它所应用的参数类型。

更清楚地说，*特设多态*避免了您必须定义`printInt`、`printDouble`、`printString`函数，一个`print`函数就足够了。

我们的`print`将依赖于*特定的多态*来根据它们所应用的参数类型做出不同的行为。

这样做的目的主要是通过操作*接口*(作为一个概念，该层允许元素进行通信，而不是像在 *Java 接口*中那样，即使它们的目的是对该概念进行编码)来进行编程，公开共享的、公共的行为或属性，并使用这些行为，而不是为这些接口抽象的每个具体类型编写不同的函数实现。

您的`print`函数可能需要一个`Printable`接口，为其参数抽象出打印它们的能力。

*   *面向对象编程*经常使用**接口子类型**通过让具体的类继承暴露所需共享行为的接口来允许多态性
*   *函数式编程*，愿意将数据和行为强烈分离**偏爱类型类**，允许向现有类型添加行为，而不必修改它们，也不必事先计划它们将需要这些函数。

# 什么是类型类？

一个*类型类*可以从字面上描述为一类类型，一组类型，它们共享共同的能力。

它代表了一组类型所共有的东西的抽象，就像*“会打招呼的东西”*抽象出每一个有能力打招呼的具体类型，或者*“有花瓣的东西”*可能抽象出现实世界中的花朵。

它的作用与面向对象程序设计中的接口相同，但不仅仅如此:

*   它允许向现有类型添加行为(甚至是超出我们代码库范围的类型)
*   它允许条件接口，我的意思是，如果`A`也是类型类`T2`的成员，我们可以把`A`编码为类型类`T1`的成员

# 如何在 Scala 中实现？

*类型类*并不特定于 *Scala* ，在许多函数式编程语言中都可以找到。
它们在 *Scala* 中不是一级构造，因为它在 *Haskell* 中可能是，但它仍然可以很容易地完成。

在 *Scala* 中，它由以下代码编码:

1.  一个 *trait* 暴露了*类型类*的*【契约】*，*类型类*要抽象的东西，
2.  我们希望成为那个*类型类*实例的每个类型的那个特征的*具体实现*。

我们为*“会打招呼的类型”*设计的*类型类*将是:

```
trait CanGreet[T] {
    def sayHi(t: T): String
} 
```

代表具有能力`sayHi`的所有类型`T`。

给定一个:

```
case class Player(nickname: String, level: Int)
val geekocephale = Player("Geekocephale", 42) 
```

我们现在创建一个`CanGreet` *特征*的`Player`实例，作为我们*类型类*的实例。

```
val playerGreeter: CanGreet[Player] = new CanGreet[Player] {
    def sayHi(t: Player): String = s"Hi, I'm player ${t.nickname}, I'm lvl ${t.level} !"
} 
```

感谢我们所做的，我们现在可以定义通用函数，例如:

```
def greet[T](t: T, greeter: CanGreet[T]): String = greeter.sayHi(t) 
```

`greet`是多态的，从这个意义上来说，它将对任何一个`T`起作用，只要它得到那个类型的`CanGreet`的一个实例。

```
greet(geekocephale, playerGreeter) 
```

然而，这使用起来有点麻烦，我们可以利用 *Scala* 的*隐式*功能让我们的生活变得更容易(并且更接近其他语言的*类型类*机制)。

让我们重新定义我们的`greet`函数，并使我们的`CanGreet`实例*隐含*。

一些卫生指南来自哈斯克尔 T2 类型的班级:

*   使您的*类型类*实例处于活动状态
    *   要么是在*类型类的*伴随对象中
    *   或者在类型的*中你的类型`T`的同伴对象*，这里的`Player`
*   对于一个给定的*类型类*，每种类型有不超过**个**实例(在 *Scala* 中尤其如此)

*类型类*和类型伴随对象是*类型类*实例的好地方，因为当一个函数需要类型`TC[T]`的隐式时，它们的作用域都被检查，其中`TC`是你的*类型类特征*而`T`是你寻找的*类型类*实例的类型。

```
object Player {
    implicit val playerGreeter: CanGreet[Player] = new CanGreet[Player] {
        def sayHi(t: Player): String = s"Hi, I'm player ${t.nickname}, I'm lvl ${t.level} !"
    }
}

def greet[T](t: T)(implicit greeter: CanGreet[T]): String = greeter.sayHi(t) 
```

现在，我们可以调用我们的`greet`函数，而不需要显式传递`CanGreet`实例，只要我们有一个*隐式*实例用于我们在作用域中使用的类型`T`(我们有，`playerGreeter`)！

```
greet(geekocephale) 
```

总而言之，这里是我们到目前为止写的所有代码:

```
trait CanGreet[T] {
    def sayHi(t: T): String
}

case class Player(nickname: String, level: Int)

object Player {
    implicit val playerGreeter: CanGreet[Player] = new CanGreet[Player] {
        def sayHi(t: Player): String = s"Hi, I'm player ${t.nickname}, I'm lvl ${t.level} !"
    }
}

def greet[T](t: T)(implicit greeter: CanGreet[T]): String = greeter.sayHi(t) 
```

# 可选化妆品

这一部分对于理解*类型类*如何工作绝对不是强制性的，它只是对我们之前看到的内容的常见语法补充，主要是为了方便，可以跳过它直接得出结论。

```
def greet[T](t: T)(implicit greeter: CanGreet[T]): String 
```

是完全一样的东西，可以重构为:

```
def greet[T: CanGreet](t: T): String 
```

函数签名看起来更好，我认为它更好地表达了`T`到*的*"需要"*" be "*`CanGreet`的一个实例，但有一个缺点:我们失去了通过名称引用我们的`CanGreet`隐式实例的可能性。
为此，为了从隐式范围中调用我们的实例，我们可以使用`implicitly`函数:

```
def greet[T: CanGreet](t: T): String = {
    val greeter: CanGreet[T] = implicitly[CanGreet[T]]
    greeter.sayHi(t)
} 
```

为了减少麻烦，您通常会看到一个带有`apply`方法的*类型类*特征的伴随对象:

```
object CanGreet {
    def apply[T](implicit C: CanGreet[T]): CanGreet[T] = C
} 
```

它完全按照我们在上一个`greet`函数实现中所做的那样，允许我们现在重写我们的`greet`函数如下:

```
def greet[T: CanGreet](t: T): String = CanGreet[T].sayHi(t) 
```

`CanGreet[T]`正在调用伴随对象`apply`函数(`CanGreet[T]`实际上被去糖化为`CanGreet.apply[T]()`，作用域中的隐式实例被传递给了`apply`)从隐式作用域中调用`T`的`CanGreet`实例，我们可以通过调用`.sayHi(t)`立即在`greet`函数中使用它。

最后，你可能还会看到*隐式类*，为我们的*类型类*调用*语法*，它持有我们的*类型类*允许的操作:

```
implicit class CanGreetSyntax[T: CanGreet](t: T) {
    def greet: String = CanGreet[T].sayHi(t)
} 
```

允许我们的`greet`函数以更方便的 OOP 方法方式被调用:

```
geekocephale.greet 
```

总而言之，这里是我们用这些升级写的所有代码:

```
trait CanGreet[T] {
    def sayHi(t: T): String
}

object CanGreet {
    def apply[T](implicit C: CanGreet[T]): CanGreet[T] = C
}

implicit class CanGreetSyntax[T: CanGreet](t: T) {
    def greet: String = CanGreet[T].sayHi(t)
}

case class Player(nickname: String, level: Int)

object Player {
    implicit val playerGreeter: CanGreet[Player] = new CanGreet[Player] {
        def sayHi(t: Player): String = s"Hi, I'm player ${t.nickname}, I'm lvl ${t.level} !"
    }
} 
```

# 类型班级奖金

## 后验子型

类型类比经典的 *OOP* *子类型*提供更多。

*类型类*允许添加行为到**现有类型**(包括不是你的类型):

```
import java.net.URL

implicit val urlGreeter: CanGreet[URL] = new CanGreet[URL] {
    override def sayHi(t: URL): String = s"Hi, I'm an URL pointing at ${t.getPath}"
} 
```

我们刚刚给`java.net.URL`增加了可以打招呼的功能！

## 条件接口

你可以定义条件类*类型类*实例:

```
implicit def listGreeter[A: CanGreet]: CanGreet[List[A]] = new CanGreet[List[A]] {
    override def sayHi(t: List[A]): String = s"Hi, I'm an List : [${t.map(CanGreet[A].sayHi).mkString(",")}]"
} 
```

通过要求`[A: CanGreet]`，我们刚刚声明了`List[A]`在`CanGreet` *类型类*的实例中当且仅当`A`是`CanGreet`的实例。

为了向您展示我们可以进一步推动条件行为，我们可以做一些完全无用的事情，比如:

```
implicit def listGreeter[A: CanGreet: MySndTypeClass](implicit c: MyThirdTypeClass[String]): CanGreet[List[A]] = ??? 
```

在这里，我们请求，`List[A]`作为`CanGreet`的实例:

*   `A`是的实例
    *   `CanGreet`
    *   `MySndTypeClass`
*   `String`是`MyThirdTypeClass`的一个实例(我不得不承认，这非常愚蠢)。

# 工装

*   [simular um](https://github.com/mpilquist/simulacrum)是一个很棒的库，通过使用注释，它可以在编译时为你生成*类型类*和语法内容，从而极大地减少了样板文件
*   Magnolia 也是一个很棒的库，可以自动派生出你的*类型类*实例

我们没有讨论*类型类*的派生，这是一个更高级的话题，但是基本思想是，如果你的类型`A`和`B`是一个*类型类*的实例，并且如果你有一个由`A`和`B`组合而成的类型 C，比如:

```
case class C(a: A, b: B) 
```

或者

```
sealed trait C
case class A() extends C
case class B() extends C 
```

它使类型`C`自动成为你的*类型类*的一个实例！

# 更多材料

如果你想继续深入，可以在我的 [FP 资源列表](https://github.com/mmenestret/fp-ressources)中找到一些有趣的东西，特别是:

*   [掌握类型分类归纳](https://www.youtube.com/watch?v=Nm4OIhjjA2o)
*   [类型类，终极特设](https://www.youtube.com/watch?v=2EdQFCP5mZ8)
*   [Scala 中的类型类](https://blog.scalac.io/2017/04/19/typeclasses-in-scala.html)
*   隐含、类型类和扩展方法

# 结论

最后，我们看到了为什么*类型类*有用，它们是什么，它们是如何在 *Scala* 中编码的，以及一些可能帮助你使用它们的修饰和工具。

我们经历了:

*   如何创建一个提供共享行为的*类*

    *   不需要**事先知道**(我们不扩展我们的类型也不修改它们)
    *   而**不必将该行为混合到数据**(case 类本身)中
*   如何创建我们的*类型类*的新实例

*   如何通过设计多态函数**来利用多态，定义多态函数**并为任何类型`T`工作，只要提供所需的*类型类*的(隐式)实例！

我会尽量保持博客更新。如果有任何我应该纠正的补充、不精确或错误，或者如果您需要更多的解释，请随时通过 Twitter 或邮件联系我！

* * *

编辑:感谢 [Jules Ivanic](https://twitter.com/guizmaii) 的评论:)