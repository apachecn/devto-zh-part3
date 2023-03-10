# Rust 中我喜欢的东西:错误处理

> 原文：<https://dev.to/jonstodle/things-i-enjoy-in-rust-error-handling-2jkh>

**我喜欢 Rust 里的东西:**

1.  [枚举](https://dev.to/jonstodle/things-i-enjoy-in-rust-enums-4cdl)
2.  **错误处理**
3.  [不变性](https://dev.to/jonstodle/things-i-enjoy-in-rust-immutability-b9g)
4.  [Impl](https://dev.to/jonstodle/things-i-enjoy-in-rust-impl-1doj)
5.  [宏](https://dev.to/jonstodle/things-i-enjoy-in-rust-macros-21fp)

我已经说过我很喜欢《Rust》中枚举的用法。它们使得表达多个状态*和*状态的相关数据变得容易。这一点被很好地利用的一个地方是错误处理。

一般来说，程序中会出现两种类型的错误:可以恢复的错误，即尽管操作失败，程序仍可以继续执行；以及你**不能**恢复的错误，即你的程序立即退出。

很多流行的编程语言没有很好的方法来区分这两种错误，但是在 Rust 中这做得很好。对于任何无法恢复的错误，使用`panic`宏。这将强制您的程序立即退出。对于所有其他的错误，那些你**可以**恢复的错误，使用一个枚举:`Result<T, E>`。

`Result<T, E>`有两种情况:`Ok(T)`表示操作成功，`Err(E)`表示操作失败。以这种方式进行错误处理有两个非常好的副作用:

首先，很容易看出哪些函数可能会“抛出”一个错误(或异常，正如某些语言中所知):它们返回`Result<T, E>`！这使得函数自文档化，这再次意味着您不必依赖(可能)过时或不完整的文档。

第二个副作用是，它迫使你考虑如何处理可能的错误。一个返回`Result<T, E>`的函数，迫使你写某种代码来处理出错的可能性。最简单的“处理”方法是在`Result<T, E>`上调用`unwrap`。如果`Result<T, E>`是一个错误，那么`unwrap`会让你的程序*死机*，如果不是错误，那么返回`Ok(T)`的值。

更有可能的是，您将求助于另一种方法来处理可能的错误，要么使用一个`match`表达式，要么使用`Result<T, E>`上的一个便利函数。

例如，函数`unwrap_or`允许您在`Result<T, E>`出错时指定默认值。当您不想要复杂的错误处理时，这使得您的代码简洁。我们来比较两个例子:

```
// C#
string value = "Default value";
try
{
        value = SomeOperationThatMightFail();
}
catch
{
        // Ignored
}

// Rust
let value = some_operation_that_might_fail()
        .unwrap_or("Default value") 
```

Enter fullscreen mode Exit fullscreen mode

即使您压缩了 C#代码片段的格式，或者创建了一些帮助器函数来清理代码，习惯性 Rust 代码如此简洁的事实确实吸引了我。

我可以假设，如果我正确处理每一个`Result<T, E>`，我的代码只会在*崩溃*时崩溃，在这种情况下，我什么也做不了，这是我喜欢的事情。