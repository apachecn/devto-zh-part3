# 我喜欢 Rust 中的东西:宏

> 原文：<https://dev.to/jonstodle/things-i-enjoy-in-rust-macros-21fp>

**我喜欢 Rust 里的东西:**

1.  [枚举](https://dev.to/jonstodle/things-i-enjoy-in-rust-enums-4cdl)
2.  [错误处理](https://dev.to/jonstodle/things-i-enjoy-in-rust-error-handling-2jkh)
3.  [不变性](https://dev.to/jonstodle/things-i-enjoy-in-rust-immutability-b9g)
4.  [Impl](https://dev.to/jonstodle/things-i-enjoy-in-rust-impl-1doj)
5.  **宏**

在 Rust 之前，我从未使用过带有宏的编程语言。我听说过它，也看到过一些关于它的热烈讨论，但我从来没有真正研究过他们是怎么做的。现在我知道了-我爱他们！

Rust 中的宏实现了有时被称为*元编程*的功能:代码编写其他代码。当你在 Rust 中使用一个宏时，你实际上是在调用一个函数，这个函数接受输入并根据输入生成更多的代码。这样做的结果就是提供给编译器的内容。

这些宏很像某些软件中的键盘宏或可编程宏:它们让您自动执行重复的任务。

当然，我自己没有编写过任何宏，但是我很高兴能够使用来自标准库和外部机箱的便利宏。

让我们从*这本书来看这个例子，它展示了`vec!`宏是如何工作的:* 

```
// vec! macro defined
#[macro_export]
macro_rules! vec {
    ( $( $x:expr ),* ) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x);
            )*
            temp_vec
        }
    };
} 

// vec! macro in use
let v: Vec<u32> = vec![1, 2, 3]; 
```

Enter fullscreen mode Exit fullscreen mode

`macro_rules!`定义了`vec!`宏接受的输入类型(可变数量的输入)，它创建了一个新的`Vec`实例，`push`将输入传递到`Vec`并返回。

此外，截至 2018 年版的 Rust，已经可以创建与`#[derive()]`一起使用的宏，这使得任何人都可以创建可以作用于`struct`的宏。就像`#[derive(Clone)]`如何使一个结构实现`Clone`特征一样，你可以创建一个宏，为任何结构自动实现你自己的特征。

讨厌编写大量的 boiler plate 代码来将您的结构转换为 JSON 或将 JSON 转换为结构？板条箱里有宏指令！通过一个简单的`#[derive(Serialize，Deserialize)]您的 struct 支持被序列化和反序列化为 JSON，然后返回。这再简单不过了。

有了宏，就有了更多的乐趣。*