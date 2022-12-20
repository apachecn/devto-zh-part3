# Kotlin 介绍(第 1 部分):变量、类型和函数

> 原文：<https://dev.to/mkbaldwin/introduction-to-kotlin-part-1-variables-types-and-functions-193k>

## 什么是科特林？

如果你正在读这篇文章，那么你可能已经听说过[科特林](https://kotlinlang.org)，但是你可能想知道它是什么，为什么你会感兴趣。Kotlin 是一种现代的跨平台编程语言，由 JetBrains 开发和支持。2017 年，谷歌宣布 Kotlin 是 Android 开发的首选语言。

Kotlin 代码可以针对 Java 虚拟机(JVM)、JavaScript 或本机进行编译。本文中讨论的所有语言概念对于所有三个平台都应该是通用的，但是主要关注 JVM 支持，并且将与 Java 进行比较。

Kotlin 语言从一开始就考虑到了 Java 兼容性。用 Kotlin 编写的代码可以 100%与 Java 互操作。这意味着您可以轻松地将 Kotlin 合并到现有的 Java 项目中，并且/或者利用 Java 库和工具的强大生态系统。

[![Java + Kotlin = Heart](img/84a1f97d21f0767457c220cc5fe6c705.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7LdGL5gc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/v2opraung3zfnwdmjkfu.png)

注意，本文假设读者已经掌握了一些编程知识。

## 变量和类型

变量使用`var`关键字声明，或者对于不可变(只读)的变量使用`val`关键字声明。应该优先使用不可变的`val`。

```
var mutableVariable: String = "Default Value"
val immutableVariable: String = "The Value" 
```

Enter fullscreen mode Exit fullscreen mode

Kotlin 支持变量赋值时的类型推断。换句话说，在某些情况下，编译器可以通过上下文判断出变量的类型。因此，前面显示的代码可以简化为排除类型定义。

```
var mutableVariable = "Default Value"
val immutableVariable = "The Value" 
```

Enter fullscreen mode Exit fullscreen mode

Kotlin 包括非常强大的编译时类型检查和空安全。默认情况下，所有变量都不可为空。如果你想让一个变量允许空值，它必须通过添加一个`?`来指定为类型的一部分。

```
var nullableVariable: Int? = null 
```

Enter fullscreen mode Exit fullscreen mode

因为变量的可空性是显式定义的，所以当调用的代码不安全并且需要空检查时，Kotlin 编译器会生成错误。虽然科特林型系统被设计成试图阻止`NullPointerExceptions`，但它仍然有可能在[某些情况下](https://kotlinlang.org/docs/reference/null-safety.html)。

与 Java 不同，Kotlin 中的所有基本类型都被视为对象。String、Int、Long、Float、Double 等基本类型。被提供。请注意，虽然这些类型在 Kotlin 代码中被视为对象，但编译器可能会在编译后将它们表示为适当的 JVM 原语。

## 功能

Kotlin 中的函数是使用关键字`fun`定义的。

```
fun add(a: Int, b: Int): Int {
    return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在示例函数中，参数声明看起来很像变量声明。声明参数时，省略了`var` / `val`关键字。函数参数总是不可变的。

返回类型遵循函数参数。如果函数没有返回值，那么返回`Unit`类型。在这种情况下，可以省略返回类型。

```
fun sayHello(name: String) {
    println("Hello $name!")
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个示例函数中，您会注意到 Kotlin 中一个额外的便利特性，字符串插值。在我们的例子中，`$name`被替换为参数中的值。因此，字符串`"Hello $name!"`相当于 Java 代码中的`"Hello " + name + "!"`。

Kotlin 中的许多语言特性旨在使代码更加简洁，更易于读写。如果一个函数只有一个返回值的表达式，我们可以缩短代码，写一个单表达式函数。

```
fun add(a: Int, b: Int) = a + b 
```

Enter fullscreen mode Exit fullscreen mode

单表达式函数允许我们通过省略类型并允许编译器从表达式中推断来进一步减少代码的编写。

函数参数可以被赋予默认值，调用代码可以省略参数。这是在函数定义中通过给任何参数赋值直接完成的。

```
fun message(message: String = "Hello", name: "World"){
  println("$message $name!) } 
```

Enter fullscreen mode Exit fullscreen mode

可以为任何参数提供默认值。在`message`函数中，如果您只想覆盖`name`的默认值，那么必须使用命名参数。这是通过在函数调用中包含参数名并赋值来实现的。下面的示例代码显示了调用该函数的多种方式。

```
message(name = "Reader") // Prints "Hello Reader!"
message("Hey", "Reader") // Prints "Hey Reader!"
message("Hey") // Prints "Hey World!"
message() // Prints "Hello World!" 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来呢？

在本系列的第二部分中，我们将讨论 Kotlin 条件句和循环。另外，[科特林参考文献](https://kotlinlang.org/docs/reference)涵盖了本文中更详细讨论的所有项目
。