# Scala 隐式:演示

> 原文：<https://dev.to/apium_hub/scala-implicits-presentations-4a16>

这篇文章不会深入探讨 Scala 的隐含性，而是会讨论它们是什么。这篇文章是写给那些开始学习 Scala 并需要初步了解隐含的人，也是写给那些尽管没有使用 Scala 但想知道“隐含”是什么的人。

它将处理一系列的文章，这是第一篇(也是后续文章的索引),接下来是关于 Scala 隐式的更详细和深入的文章；它们的操作和使用。

那么，让我们开始吧:

## 隐性和显性是什么意思？

当我们谈论 Scala implicit*(哦，好吧，我没想到)*时，这两个词经常被使用，就像编程世界中的几乎所有东西一样，它们是从我们生活的其他方面借用来的。

**字典定义如下:**

*   含蓄，ta Del lat。implicĭtus.包含在其他事物中，但没有表达出来。
*   很明显，晚一点。explicĭtus.明确而坚决地表达一件事的。

但是让我们看一个日常的例子。当我们彼此交谈时，我们不会明确地提到我们谈论的所有事情，但有许多事情是通过上下文来理解的。

例如，如果我们要骑摩托车出去，我让你把头盔给我，你会把我的头盔给我，但是，我没有明确地说是那个头盔。你已经通过上下文理解了，当我要求头盔时，我指的是我的，这是**暗示**。

事实上，明确提及我们所指的一切是很奇怪的*(除了给出精确指令的纯技术上下文)*。

## 又在码什么？

好吧，但这和编程有什么关系？

好吧，Scala 有一个概念和我们刚才描述的差不多。

如果我们不必显式地传递参数给函数，会发生什么？如果函数通过上下文理解它们呢？或者，如果我们不需要显式调用一个函数，而编译器通过我们所处的上下文理解了这一点？我们会想要使用它吗？这就是概念，一切都在上下文中，Scala 用不同的方式实现了隐式的概念。

## Scala 隐式有哪些类型？

**隐含参数。**

Scala 中的方法可以接收最后一个参数列表，前缀是隐式的。

```
def sendText(body: String)(implicit from: String): String = s"$body, from: $from" 
```

如果需要，可以正常调用这个参数列表:

```
sendText("hola mundo")("Apiumhub")
//res3: String = hola mundo, from: Apiumhub 
```

但是它的主要特点是你可以在你的代码中定义一个隐式的值/函数/定义，如果它在相同的上下文中…编译器会使用它！

```
implicit val sender: String = "Alice"
sendText("hola mundo")
//res4: String = hola mundo, from: Alice

implicit def sender: String = "Bob"
sendText("I love you")
//res0: String = I love you, from: Bob 
```

但是，如果编译器找不到任何具有指定类型的隐式值，它将失败:

```
sendText("hola")
//:13: error: could not find implicit value for parameter from: String
// sendText("hola")
// ^ 
```

这允许消除对需要相同参数的不同方法的调用中的代码重复，以及向组件注入协作者(依赖注入)。

## 隐式转换(隐式函数)

当一个方法需要一个类型 A，而你想给它传递一个类型 B 的值，会发生什么呢？当一个方法返回一个 C 类型而你想要一个 D 类型时会发生什么？这两个问题的答案是一样的:要么请人更改签名和实现…要么您将更改函数 A => B 来解决问题。“问题”是你必须在所有需要的地方应用这个函数，这意味着代码的重复。此外，我们必须修改代码来添加这种转换。如果我们使用泛型呢？我们不知道使用什么转换器…也许使用模式匹配…

```
object Playground {

  def createNumber: Int = scala.util.Random.nextInt

  val myNumber: String = createNumber

} 
```

这是其中的一种情况，编译器报错 _ **createNumber** _ 因为它返回 _ **Int** _ 而不是 _ **String** _。我们将创建一个隐式转换来自动完成转换:

```
import scala.language.implicitConversions
object Playground {

  implicit def Int2String(number: Int): String = number.toString()

  def createNumber: Int = scala.util.Random.nextInt

  val myNumber: String = createNumber
  val text: String = 123

} 
```

这个工具的能力几乎没有限制，并且有一些实际用途，例如在隐式转换中定义 DTO 到域的转换(反之亦然),并且忘记必须应用任何类型的转换，因为它将自动完成。

## 隐含类

参数和隐式转换是最著名的，但是还有更多类型，比如隐式类。

```
object Playground {
    implicit class MyString(x: String) {
        def mirror: String = x.reverse
        def addThings: String = x+"abcdefg"
    }
} 
```

你认为这段代码是做什么的？答案很简单(很多人会说，好吧)**扩展方法**

```
import Playground.MyString
"123456".mirror
//res1: String = 654321
"123456".addThings
//res2: String = 123456abcdefg 
```

像所有的隐式方法一样，它有其局限性，但也有很多实用性:你如何向一个类添加额外的行为，这些行为可能是也可能不是你的？构成还是传承，对吧？如果这是最后一个，你不能延长它？你有一篇作文，这可能意味着做一个包装纸，等等。

```
final class Author(private val name: String) {
  def sayname: String = name
}

object Playground {
  implicit class MyAuthor(x: Author) {
    def shoutname: String = x.sayname+"!!!"
  }
}

import Playground.MyAuthor

val author:Author = new Author("Jack")
author.shoutname
//res7: String = Jack!!! 
```

像这样，我们可以以一种自然的方式与作者一起工作。

## 隐含对象

这是最常用的隐式形式，同时也是最少使用的隐式形式。它允许创建和使用在 stdlib 和库中广泛使用的 typeclasses，但是除了 typeclasses 之外，它们的使用实际上是不存在的。当请求与该对象类型相同的隐式参数时，编译器可以传递的隐式对象。这就像是对象之间相互理解的隐式参数。

```
object Playground {

  trait MyTrait {
    val number: Int
    def transform(number:Int): String
  }

  implicit object MyObject extends MyTrait {
    val number: Int = 1
    def transform(number:Int): String = number.toString
  }

  def doSomething(implicit theTrait: MyTrait) = theTrait.transform(theTrait.number)

}

import Playground._

doSomething
//res1: String = 1 
```

你可能会想，你看不到很多用例。好吧，稍后你会看到使用这些隐式对象能得到什么。

### 他们有什么问题？

**暗码**

隐含是一个非常强大的工具。一旦你开始发现它们，你可能会想要虐待它们。这可不好，相信我，我经历过。一段代码中隐含的东西被滥用了，这是你能找到的最难理解、遵循和调试的东西。一切都是魔法(在许多情况下是黑魔法)，事情发生了，乍一看你无法控制它。有可能你留了一个隐式来声明，然后一切都编译了，因为有人在那个范围内声明了一个隐式值，并且类型一致，相信我，调试是犯罪。

**模棱两可的隐含定义**

隐式类型是基于所需的类型来寻找的，这意味着我们不能有两个具有相同类型共享范围的隐式类型，编译器会不知道该怎么办！

```
object Playground {
  implicit val anumber: Int = 1
  def plus1(implicit number: Int): Int = number+1
}

import Playground._
implicit val myNumber: Int = 0
plus1
//:1: error: ambiguous implicit values:
// both value anumber in object Playground of type => Int
// and value myNumber of type => Int
// match expected type Int
// plus1
// ^ 
```

尽管你想用它们做不同的事情，但是如果它们有相同的类型，你不能让两个隐含共享同一个范围。这让我们得出一个结论:我们需要小心原始的痴迷。如果我们以一种特定的方式打字，用特定的类型而不是原语，我们可以避免这种类型的问题。

## 最后一句话:Scala 隐含

总之，我们消除了隐含的样板文件，我们编写了更少的代码，我们在编译中解决了所有问题，然而一切都不是完美的。代码有时很难理解(Kotlin 的作者已经明确决定不在语言中实现它们)。

我们可以说:“你必须适度地使用它们”，但是对我们来说，问题不在于*适度与否，而是如何以及何时使用它们的标准和模式，因为这是一个架构决策。*

在本系列的最后，我们将提到一些标准示例。

## 关于暗示的推荐读物

如果你想轻松阅读隐式，你可以阅读下一章，在这一章中我们将讨论 Scala 隐式的**模式，编译器如何寻找它们，以及在 stdlib 和 scalaz 等书店中找到的例子。此外，您可以阅读这些建议:**

*   Scala 正在运行:capítulo 8.3
*   Scala 举例:capítulo 15
*   solo para valientes:[https://www . artima . com/pins1ed/implicit-conversions-and-parameters . html](https://www.artima.com/pins1ed/implicit-conversions-and-parameters.html)

如果你对 Scala implicits 或者软件开发感兴趣，我强烈推荐你订阅我们的每月简讯！

## 如果你觉得这篇关于 Scala 隐式的文章很有趣，你可能会喜欢…

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

[Scala Generics III:通用类型约束](https://dev.to/apium_hub/scala-generics-iii-generalized-type-constraints-58km)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

Scala implicits 的帖子[:演示文稿](https://apiumhub.com/tech-blog-barcelona/scala-implicits/)首先出现在 [Apiumhub](https://apiumhub.com) 上。