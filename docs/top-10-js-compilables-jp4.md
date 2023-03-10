# 前 10 名:JS 可编译性

> 原文：<https://dev.to/areknawo/top-10-js-compilables-jp4>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

JavaScript 对于许多用例来说都很棒。这是一种设计非常好的语言。但事实是它缺少一些特性。JS 中不存在任何类型安全和自定义操作符——至少在编写本文时是这样。然而，使用任何种类编译步骤都可以很容易地实现这些和其他语法糖。你可能也不太喜欢 JavaScript，但仍然想编写 web 应用程序。当然，你可以使用像 **Emscripten** 这样的工具将你最喜欢的语言(这里是 C/C++，但也有一些选项，比如 Java)编译成供浏览器使用的 JavaScript。但是还有其他选择。大量的编程语言为 JS 编译目标提供了一流的/唯一的支持。这些可以吸引你的语法和其他功能，如编辑器支持。信不信由你，还真不少。在这篇文章中，我将向你介绍我列出的以 JS 为编译目标的**十大编程语言**。请记住，我还没有全面尝试过它们...还没有。这主要是基于我的第一印象，所以...我希望这会让你感兴趣。😉

[![img](img/d65b10aa81798c394dcac5fb0da3a68f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7EqI4Nbi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-25-22-23-49.png)

# 10。 [Scala.js](http://scala-js.org/)

**Scala.js** 允许你将 **Scala** 编程语言编译成 JavaScript。有了它，你可以用一个强类型系统编写一个现代的、**后 ES6 风格的**代码。这意味着强大的 IDE 支持和特性，如类、模块、类型和宏，用于扩展开箱即用的语言！Scala.js 宣称拥有接近原生的 js 性能，并通过 facades types 系统支持标准 JS 库。如果你已经用 Scala 编程了，这可能会适合你。此外，项目页面为那些来自 ES6 背景的人提供了一个很好的指南。我们来总结一些基本的区别。

```
// Comment
/*
Multiline comment
*/
var mutable: String = "variable"
val immutable = "constant"

def exampleMultiplyFunction(x: Double, y: Double): Double = x * y

abstract class Logger() {
  def log(msg: String): Unit = {
    println(msg)
  }
}
class ConsoleLogger() extends Logger() {
    override def log(msg: String = ""): Unit = {
        if (msg != "") println(msg) else println("console.log equivalent")
    }
}
new ConsoleLogger().log() 
```

Enter fullscreen mode Exit fullscreen mode

这可能不是 Scala 可能性的全部展示，甚至不是有效的*“Hello World！”*但我认为这可以让大多数程序员对 Scala 的语法有一个基本的了解。当然，你也可以利用带有大量内置类型和结构的整个 Scala 标准库。想了解更多 Scala 的好处，你可以访问 Scala 语言网站或者 Scala.js 的网站，我认为不用说，在编写纯 Scala 时，你可以通过它的 **JVM** 和 **LLVM** 目标来实现跨平台代码。

[![img](img/37975c4dc3eaea023f4e6d925cf3a2f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s8ZrEwYg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-25-22-24-31.png)

# 9。[哈克斯](https://haxe.org/)

Haxe 是一个真正的跨平台的 T2 解决方案，用于动态和交互式的应用开发。它可以被编译成许多其他语言，包括 JS。正因为如此，Haxe 有许多用例——游戏、服务器后端、网站、专业软件和计数。该网站提供了大量令人印象深刻的详细资料。至于语言本身，它有很好的面向对象和类型安全的语法(很好的 IDE 支持),还有各种其他特性，比如**元数据**和编译器的**宏**。这一次，妥妥的*“你好世界！”*可以用。😁

```
class Main {
  static public function main():Void {
    trace("Hello World");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我可以说，Haxe 的语法远远优于 JS，这主要归功于类型系统、宏和所有编译器的优点。对于跨平台应用和游戏来说，Haxe 似乎是一个不错的选择。

[![img](img/bb35b56dde8a6ba1de9aae9c99535423.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qGF_kpfF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-25-22-28-33.png)

# 8。[镖](https://dartlang.org/)

由于**的颤动框架**，Dart 最近变得相当受欢迎。Flutter 可以用来轻松开发 **Android** 和 **iOS** 应用。而且居然是用 Dart 写的！那就去飞镖吧。这是一种由 Google 创建的基于 VM 的语言，因此它可以在其作为服务器后端的 **VM** 上运行，当 JS 编译时可以在 web 上运行，并且可以在其他 **x86** 和 **ARM** 平台上运行(特别是对于使用 Flutter 的移动设备)。它的语法受 C、C#和 Java 以及大量标准库的启发。下面的例子摘自[Dart pad](https://dartpad.dartlang.org/)(Dart online playground)斐波那契举例:

```
void main() {
  var i = 20;
  print('fibonacci($i) = ${fibonacci(i)}');
}

int fibonacci(int n) {
  return n < 2 ? n : (fibonacci(n - 1) + fibonacci(n - 2));
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码清楚地显示了 Dart 类型化和面向对象的本质。同样有趣的是 Dart 对异步编程的关注，其结构类似于**未来**和**流**。除此之外，IDE 支持 Dart 拥有的所有类型化语言特性。所有这些都使 Dart 成为要求苛刻的 web 和移动应用程序的完美选择。

[![img](img/5229f2c67e2b420d261d11d9f7c8e6b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oHH0CsjW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-25-22-30-35.png)

# 7。[榆树](https://elm-lang.org/)

与这个列表中的前几个位置不同， **Elm** 只可编译 JS 和其他 web 语言(HTML)。它的主要目标是利用定制的、高性能的**虚拟 DOM** 实现 web 应用程序开发。Elm 很好地实现了它的目的，以一种良好的、可读的形式将 HTML 和 JS 结合起来。

```
import Html exposing (text)

who = "World" -- comment

main =
  text ("Hello " ++ who ++ "!") 
```

Enter fullscreen mode Exit fullscreen mode

除此之外，Elm 语法可能需要一些时间来适应。它的类型系统和 IDE 支持将对此有所帮助。

[![img](img/62e2312dd4be710c21cddb9be01258e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XaEMCmc5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-25-22-31-45.png)

# 6。 [Imba](http://imba.io/)

Imba 是另一种专注于网络应用开发的语言。我认为它是一个年轻的榆树竞争对手。凭借其**内存化 DOM** ，Imba 声称比今天所有的虚拟 DOM 实现都要**快**。

```
# Comment
console.log "Hello World"

tag HelloWorld
    def render
        <self> "Hello World"

Imba.mount(<HelloWorld>) 
```

Enter fullscreen mode Exit fullscreen mode

在 Imba 中，DOM 节点是*“一等公民”*，这样你就可以像操作其他变量一样操作它们。有点可悲的是 Imba 没有强类型系统，意味着 IDE 支持有点差。此外，事实上，它是作为 React 竞争对手进行比较和提供的，这有点滑稽。你知道，因为 React 是一个**库**而 Imba 被宣传为一种**语言**。😂

[![img](img/014459336a8e28c1d44bcce911301cb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jmLfm4Uo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-25-22-33-22.png)

# 5。[尼姆](https://nim-lang.org/)

回到更标准的编程语言，我们遇到了 **Nim** 。Nim 是静态类型的、**垃圾收集的**语言，可以编译成 C、C++和 JS。它拥有受 Python 启发的基于**缩进的**语法，以及对**元编程**和**宏**的强大支持。一般来说，这一切都归结于出色的性能、有趣的语法和编辑器支持。

```
import strformat
type
  Person = object
    name*: string # Field is exported using `*`.
    age: Natural  # Natural type ensures the age is positive.

var people = [
  Person(name: "John", age: 45),
  Person(name: "Kate", age: 30)
]

for person in people:
  # Type-safe string interpolation.
  echo(fmt"{person.name} is {person.age} years old") 
```

Enter fullscreen mode Exit fullscreen mode

IMHO Nim 看起来是一个有趣的项目，正在积极开发，值得一看。👍

[![img](img/9c4ca7e5d9e60da3957afdb203ef3c31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xBYqcIDq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-25-22-33-51.png)

# 4。 [ClojureScript](https://clojurescript.org/)

**ClojureScript** 是一个 [Clojure](http://clojure.org/) 到 JavaScript 的编译器。ClojureScript 完全接受了**函数式编程**的思想。因此，它拥有大量内置的**不可变的**数据结构。这为面向对象的程序员提供了一种新的思维方式。语法可能看起来有点笨拙，因为几乎所有的东西都是函数，但是假以时日，你会习惯的。🙃

```
(ns  hello-world.core)  (println  "Hello world!") 
```

Enter fullscreen mode Exit fullscreen mode

如果您打算尝试一般的函数式编程技术，这可能值得一看。

[![img](img/453d79368c8f85eb44378e0ca9043178.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Re0Y30hr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-25-22-34-22.png)

# 3。[原因](https://reasonml.github.io/)

开最好的三家，我们有**理由**。Reason 是由脸书开发的语言，专注于类型安全和互操作性。它基于 JS 和 **OCaml** 生态系统。由于理由来自于 **React** 背后的家伙，它得到了全面的支持。类型系统很好，一般的开发体验很棒。✌Also，Reason 已经被很好的设计了，让 JS 开发者感觉很熟悉，同时保留了 OCaml 最好的特性，包括**类型系统**。

```
let rec fibonacci = n =>
  switch (n) {
  | 0 | 1 => 1
  | n => fibonacci(n - 1) + fibonacci(n - 2) 
  }

for (x in 1 to 10) {
  Js.log(fibonacci(x));
} 
```

Enter fullscreen mode Exit fullscreen mode

[![img](img/faa11e3f2646d690d4c33ce021efd2c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wo7xMALh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-25-22-35-17.png)

# 2。[科特林](https://kotlinlang.org/)

**Kotlin** 是一种基于 JVM 的编程语言，支持 **Android** 开发、JS、原生 **LLVM** 二进制文件，当然还有 **JVM** 。它有一个令人敬畏的类型系统，强大的互操作性和出色的编辑器支持( **JetBrains** 项目)。这个项目最近很受欢迎。这不是没有原因的。你知道，跨平台、漂亮的语法和大量的特性加上良好的性能会带来巨大的成功。

```
class Greeter(val name: String) {
    fun greet() {
        println("Hello, $name")
    }
}

fun main(args: Array<String>) {
    Greeter(args[0]).greet()
} 
```

Enter fullscreen mode Exit fullscreen mode

[![img](img/da40add1cbeba07fb73e980a020b62ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mkfvdOcr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-25-22-35-35.png)

# 1。[打字稿](http://typescriptlang.org/)

我可能有点偏见。谈到 JS 可编译性，TypeScript 是我最喜欢的语言。它可能不是跨平台的，也没有任何特殊的功能，但它只打算做一件事——为 JS 提供**类型的系统——而且它做得很好。它还允许使用新的 **ES-Next** 标准的一些功能，这些功能在当时的浏览器中还不被支持。除了类型之外的语法是纯 JS，这就是我喜欢它的原因。至于类型本身——它们负责 TS 和 JS 项目的绝对交错的 IDE 支持。还有什么好说的？🎉** 

```
export class Fibonacci {
    index : number;
    fibonacciNumber : number;

    public calculateFibonacciNumber() {
        this.fibonacciNumber = this.calculate(this.index);
    }
    private calculate(i : number) : number{
        return (i <= 2) ? 1 : this.calculate(i -1 ) + this.calculate(i -2);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 还有更多...

这只是我对最佳 JS *【可编辑】*的个人观点。我希望我至少让你对其中的一些感兴趣，或者说服你去尝试一个。相信我，还有很多更棒的编程语言。许多提供的功能远远超出了 JS。也许其中一种会成为你的首选语言？总之，这是我的清单。考虑在下面的评论中建议其他选项。另外，**分享这篇文章**，这样其他人会觉得更容易。更多内容**在 Twitter 上关注我**或在我的脸书页面 上关注我 **[。🦄](https://www.facebook.com/Areknawos-Blog-770620059945105/)**