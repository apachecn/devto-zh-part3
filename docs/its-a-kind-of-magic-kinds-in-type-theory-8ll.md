# 这是一种魔法:类型理论中的种类

> 原文：<https://dev.to/riccardo_cardin/its-a-kind-of-magic-kinds-in-type-theory-8ll>

*原贴于:[大泥球](http://rcardin.github.io/)T3】*

我不是函数式编程专家，也不是类型理论专家。在我涉足 Scala 世界一段时间后，我现在正试图进入 Haskell 的世界。当我读到物种的时候，我记得我很惊讶，也有点害怕。类型有没有可能有类型？那么这意味着什么呢？在这篇小小的介绍性文章中，我将试着向自己解释我在 Haskell 旅行中所了解的各种事物。

## 所有悲伤的开始

为了简单起见，我们从 Java 编程语言开始。在 Java 中，你可以定义一个类(一种类型),它接受另一种类型作为输入参数。它被称为*通用类*。例如，思考到`List<T>`类型。在处理`List`时，有一个*类型参数* `T`应该提供给编译器，以获得具体可用的类型。然后，我们有`List<Integer>`、`List<String>`、`List<Optional<Double>>`等等。

如果我们不提供具体的类型作为类型参数的值呢？比如说`List<List>`呢？好吧，Java 编译器将这种类型转换成更安全的`List<List<Object>>`，警告你正在以非泛型的方式使用`List`。是的，`List<Object>`是一个可怕的具体类型。

因此，Java 类型系统似乎只允许用具体类型填充类型参数。很公平。Scala 编程语言呢？Scala 在游戏中引入了一个特性，叫做**高级类型(HKT)** 。HKTs 允许我们定义如下内容。

```
trait Functor[F[_]] {
  def map[A, B](fa: F[A])(f: A => B): F[B]
} 
```

那个奇怪的符号`F[_]`是什么意思？这意味着`Functor`是*什么的*(实际上是一个类型类)，该类型参数可以用任何其他接受且仅接受一个类型参数的泛型类型填充，即`List[T]`、`Option[T]`等等。

Scala 的类型观来自于 Haskell 的类型系统是如何被思考的。同样在 Haskell 中，您可以定义一个*仿函数*。

```
class Functor f where  
    fmap :: (a -> b) -> f a -> f b 
```

不同的系统，相同的语义。

因此，在一些编程语言中，我们需要一种方法来区分可用的类型。正如我们刚刚看到的，有些类型是具体的，有些类型需要一些信息才能变得具体。类型理论对我们有帮助，有**种**，即类型的类型。

然而，首先，我们需要通过种类的定义来指定我们需要用来修路的小砖块。

## 类型和其他玩艺

### 值

从头开始，我们找到了*值*。值是我们的程序评估的信息或数据。值的例子有`1`、`"hello"`、`true`、`3.14`等等。程序中的每个值都属于一个*类型*。一个值可以赋给一个名字，这个名字叫做*变量*。

### 类型

*类型*给我们的程序提供了如何在运行时处理值的信息。换句话说，他们将*约束*设置为值。说一个变量有类型`bool`、`e: bool`，意味着它只能有两个值，即`true`或`false`。

> 因此，类型是对表达式在运行时的值的编译时约束。

在静态类型编程语言中，值的类型定义是在编译时进行的。在编写代码时，必须给每个值(或变量)一个类型。在动态类型编程语言中，值的类型是在运行时确定的，即在程序执行期间。

在函数式编程语言中，函数是一阶公民，每个函数也有一个关联的类型。例如，接受两个整数并返回其和的函数`sum`，在 Scala 中有类型`(Int, Int) => Int`，在 Haskell 中有类型`Int -> Int -> Int`。

### 类型构造函数

正如我们之前所说的，可以有*参数化*的“类型”。*泛型类型*使用*类型参数*来利用这个特性。因此，我们可以使用类型参数定义一个“类型”，比如`List[T]`，其中`T`可以用我们想要的任何具体类型来实例化。

使用类型参数是为了证明这样一个事实，比如说，`List[Int]`的行为与`List[String]`的行为非常相似。它只不过是对类型进行泛化和抽象的一种方式。

因此，同样,(值)构造函数将一个值列表作为输入来生成新值，*类型构造函数*将一个类型作为输入来创建新类型。`List[T]`、`Map[K, V]`、`Option[T]`等都属于类型构造函数。在 Java 中，我们称它们为*泛型*。在 C++中，他们称之为*模板*。

现在，我们有两种不同“种类”的对象处理类型:前者由具体类型表示；后者通过类型构造函数。我们刚刚说过我们需要定义一个类型的类型:O

## 某种(怪物)

*种类*是或多或少与类型相关的对象类型。一个种类更多的是一个 *arity* 说明符。在它们最简单的形式中，我们使用字符`*`来指代种类。

`*`是那种简单而具体的类型。具体类型是一种不带任何参数的类型。

类型构造函数呢？让我们后退一步。(值)构造函数只不过是一个函数，它接受输入的一组值并返回一个新值。

```
class Person(name: String, surname: String) = {
    //...
} 
```

以上只是一个`(String, String) => Person`类型的函数

现在，将这个概念提升到类型构造函数:值现在由类型表示，而(值)构造函数由类型构造函数表示。然后，类型构造函数是一个函数，它接受一组类型的输入并产生一个新的类型。

想一个清单。它的定义是`List[T]`。因此，它需要一个输入类型来产生一个具体的类型。它类似于只有一个参数的函数。`List`、`Option`等等，有种类`* -> *`。`Map[K, V]`有种类`* -> * -> *`，因为它需要输入两个具体类型来产生一个具体类型。

类型构造函数类似于函数的事实通过使用*与*结合来模拟情况，在这种情况下会出现不止一个参数。

在 Haskell 中，你可以使用函数`:k`向编译器询问类型的种类。

```
ghci> :k List  
List :: * -> * 
```

然而，一个问题应该出现在你的脑海中:种类到底有什么用？答案是 *typeclasses* 。

### 类型类

类型类是一个并非所有编程语言都有的概念。例如，它存在于 Haskell 和(在某种程度上)Scala 中，但不存在于 Java 中。

> 一个类型类定义了一些行为，能够以那种方式行为的类型是那个类型类的实例。所以当我们说一个类型是一个类型类的实例时，我们的意思是我们可以使用类型类用那个类型定义的函数。

类型类与 Java 中的`interface`概念非常相似。在 Scala 中，它是使用一个`trait`获得的。在 Haskell 中，有一个专用的构造，即`class`构造。在 Haskell 中，对于一个类型可能具有的许多特性，都有类型类:`Eq`类型类标记所有被检查是否相等的类型；`Ord`类型类标记了所有可以比较的类型；`Show` type 类由可以在标准输出中漂亮打印的类型使用。

我们之前定义的`Functor`实际上是一个类型类。函数定义了类类型的行为。

类型类不是 Scala 的本地公民，但是它们可以被很好地模拟。下面的例子声明了类型类`Show`。

```
trait Show[A] {
  def show(a: A): String
} 
```

虽然在 Haskell 中，类型类有本机支持，所以编译器会识别它们并自动生成与之相关的二进制代码，但在 Scala 中，你需要改进一些复杂的机制，包括*隐式*(参见 Scala 中的[类型类](https://blog.scalac.io/2017/04/19/typeclasses-in-scala.html)以获得更多关于这个主题的细节)。

因此，要让一个类型属于标准库中包含的一个类型类，在 Haskell 中你可以简单地在类型定义中声明它，使用`deriving`关键字。

```
data List a = Empty | Cons a (List a) deriving (Show, Read, Eq, Ord) 
```

一般来说，要使一个类型属于一个类型类，你必须提供该类型类的方法(行为)的实现。下面的代码展示了一个由`Maybe`类型(Scala 中的`Option`)实现的`Functor`类型类的可能实现。如你所见，有专门的语言结构，如`instance`和`where`，来支持类型类。

```
instance Functor Maybe where  
    fmap f (Just x) = Just (f x)  
    fmap f Nothing = Nothing 
```

总之，描述抽象行为，类型类本质上也是抽象的。实现抽象的方法是使用类型参数。通常，类型类在由类型参数表示的类型上声明一些约束。

问题是类型类可以声明一些奇怪的类型参数。**对种类类型的推理，允许我们理解哪种类型可以用来实现类型参数。**

我们来做一个简单的例子。回头看看`Functor`类型类定义，类型`f`必须有一个种类`* -> *`，这意味着类型类请求只接受一个参数的类型。函数`fmap`将`f`应用于`a`型和`b`型，这两种类型都是具体类型，没有其他说明。如前所述，这类对象类似于`List`、`Maybe`(Scala 中的`Option`，Java 中的`Optional`)、`Set`类型构造函数。

如果我们想将这样的类型类应用到`Either`类型构造函数中呢？在 Haskell 和 Scala 中都定义为`Either[L, R]`，定义了两个类型参数，分别是*左*和*右*。对于我们给出的类的定义，`Either`有类`* -> * -> *`。`Either`的种类和`Functor`类请求的类型参数不兼容，所以我们需要*部分应用* `Either`类型，以获得一个具有种类`* -> *`的新类型。

为了完整起见(对于那些了解 Haskell 语法的人来说)，使`Either`成为`Functor`的成员的部分应用程序会产生如下结果。这里，我们映射了*右*类型参数的情况。

```
instance Functor (Either a) where  
    fmap f (Right x) = Right (f x)  
    fmap f (Left x) = Left x 
```

总之，种类让我们知道如何使用类型类和类型构造函数来获得具体的类型，共享它们的行为。

## 结论

我们通过类型理论精选的旅程已经结束。在这个过程中，我们看到了许多不同的概念。其中许多应该更详细，但一个帖子是不够的。我们应该明白，只有当我们必须在 Scala 或 Haskell(或任何提供这种类型版本的编程语言)中管理更高级的类型时，我们才需要种类。

种类有助于理解哪种类型可以成为类型类的成员。长话短说:)

## 参考文献

*   [Scala:更高级的类型](https://www.atlassian.com/blog/archives/scala-types-of-a-higher-kind)
*   [创建我们自己的类型和类型类](http://learnyouahaskell.com/making-our-own-types-and-typeclasses#the-functor-typeclass)
*   [函子、适用函子和幺半群](http://learnyouahaskell.com/functors-applicative-functors-and-monoids)
*   [种类(类型理论)](https://en.wikipedia.org/wiki/Kind_(type_theory))
*   [类型理论中的正确术语:类型、类型构造函数、种类/排序和值](https://softwareengineering.stackexchange.com/questions/255878/correct-terminology-in-type-theory-types-type-constructors-kinds-sorts-and-va)
*   [Scala 中的类型类](https://blog.scalac.io/2017/04/19/typeclasses-in-scala.html)
*   [在函数式编程语言中，类型构造函数可以被认为是类型吗？](https://stackoverflow.com/questions/54620961/can-type-constructors-be-considered-as-types-in-functional-programming-languages)