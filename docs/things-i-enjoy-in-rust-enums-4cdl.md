# 我喜欢 Rust 中的东西:Enums

> 原文：<https://dev.to/jonstodle/things-i-enjoy-in-rust-enums-4cdl>

**我喜欢 Rust 里的东西:**

1.  **枚举**
2.  [错误处理](https://dev.to/jonstodle/things-i-enjoy-in-rust-error-handling-2jkh)
3.  [不变性](https://dev.to/jonstodle/things-i-enjoy-in-rust-immutability-b9g)
4.  [Impl](https://dev.to/jonstodle/things-i-enjoy-in-rust-impl-1doj)
5.  [宏](https://dev.to/jonstodle/things-i-enjoy-in-rust-macros-21fp)

在我的同事开始谈论 Rust 之后，我在过去的几个月里一直沉浸在这个话题中。我一直想学一门更低级的语言，但是 C++或者类似的东西总是让我望而生畏，甚至不敢开始。

我以前听说过 Rust，也听说过一些伟大的事情，但是没有抽出时间去研究它。我终于接受了挑战。好家伙，我很高兴我做到了。

很多人谈论 Rust 的借用系统，或者它有多快，或者严格的类型系统。所有这些都是伟大的事情，但这不是我在这里要写的。我对伊努斯感到兴奋。

枚举是一组预定义的值或事例。在 C#中，枚举大多被美化`int` s，Java 中的枚举有些类似，但不常用。然而，在 Rust 中，它们也是其他数据的包装器。

如果你在 Swift 或者很多函数式编程语言中遇到过枚举，比如 F#，你可能对枚举在 Rust 中的用法很熟悉。

在 Rust 中，你可以像在其他语言中一样定义一个枚举。假设我们想要定义一个函数的可能结果，该函数解析一个字符串并尝试检测存储在该字符串中的数据类型:

```
enum ParseResult {
    Text, // The data is plain text
    Number, // The data is a number
    Boolean, // The data is a boolean value
    Error, // The data is unrecognized, parsing failed
}

fn parse_string(value: &str) -> ParseResult {
    // Code
 } 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但是我们还想返回从字符串中提取的值。使用 Rust 中的枚举，您可以为枚举的不同大小写赋值。让我们重新定义一下`ParseResult`:

```
enum ParseResult {
    Text(String),
    Number(f64),
    Boolean(bool),
    Error(String), // The error message is returned as a string
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，`parse_string`既可以返回存储在字符串*中的数据类型，也可以返回数据本身*。为了处理这个结果，您将使用`match`，它类似于许多其他类 C 语言中的`switch`，但是具有更大的灵活性:

```
match parse_string("42") {
    Text(text) => print!("The content was some text: {}, text),
    Number(number) => print!("The content was a number: {}", number),
    Boolean(boolean) => print!("The content was a boolean: {}", boolean),
    Error(error) => print!("Unable to parse the string: {}", error),
} 
```

Enter fullscreen mode Exit fullscreen mode

枚举非常有用，它们被用来在 Rust 中表示`null`。虽然很多类似 C 的语言都有引用一块没有存储值的内存的概念，Rust 却没有。如果您想表达该值可能存在，也可能不存在，您可以使用一个`Option<T>`。`Option<T>`是一个枚举，有两种情况:`Some(T)`，表示值存在；而`None`，代表没有值存在。

要使用存储在`Option<T>`中的值，您必须考虑它可能不存在的可能性，即`None`。Rust 通过公开多个函数让你*解开*，或者从`Option<T>`中提取值，让这变得简单。当没有值的时候，你可以指定一个默认值，一个执行或者崩溃程序的函数，等等。

像在 Rust 中那样使用枚举是非常有用的，我喜欢它们。

 :一般来说，Rust 没有空指针，但是在*不安全*代码中有空指针。