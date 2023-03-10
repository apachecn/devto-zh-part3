# 理解 Rust 中的基本寿命注释

> 原文：<https://dev.to/cuichenli/understand-basic-lifetime-annotation-in-rust-5en9>

rust 中的终身注释是一个相对独特的概念，很难理解(至少对我来说)。花了一些时间，只是想分享我的理解。

TL；速度三角形定位法(dead reckoning)

*   每个引用都应该有一个生存期。
*   编译器想要知道每个引用的生存期。
*   当返回值是引用时，编译器可能不知道它的生存期。
*   所以，我们应该指定它。

好，我们从`The Book`的例子开始。

```
fn longest(x: &str, y: &str) -> &str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
} 
```

这段代码不会被编译。错误信息如下:

```
error[E0106]: missing lifetime specifier
 --> src/main.rs:1:33
  |
1 | fn longest(x: &str, y: &str) -> &str {
  |                                 ^ expected lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but the signature does not say whether it is borrowed from `x` or `y` 
```

嗯，据`The Book`说，原因是:

> Rust 分不清返回的引用是指`x`还是`y`

基于这种说法，我开始思考，如果我不归还与`x`和`y`有关的东西会怎么样？所以我写了这段代码:

```
fn longest(x: &str, d: & str) -> & str {
    "ddd"
} 
```

这次我得到了和以前一样的错误和帮助信息。于是我开始思考求助信息的背后是什么。然后我注意到，在最开始，它表明

> 此函数的返回类型包含一个借用值...

所以也许这应该是主要原因，而不是那些`x`和`y`的东西🤔？好的，它建议借用值，是的，我打算返回一个借用值`&str`。但是借用的价值从何而来，返回值应该有多长的生命周期？记住根据`The Book`，每个引用都应该有一个寿命。

我猜从编译器的角度来看，这个借用的值主要有两个来源。第一个是这个函数得到的参数，第二个是在函数中创建的任何值(当然也可以是全局变量，比如常量)。那么这些情况的寿命是多少呢？让我们逐一调查。我们从函数中创建的返回值开始。

如果我们想返回一个对内部作用域变量的引用，这个引用应该有一个很长的生存期。否则，一旦函数范围结束，引用就会被删除。这让我想起了`static`生存期说明符(当然也可以是`'a`)。根据文档，这个说明符意味着变量的生命周期与整个项目一样长。所以我把代码改成了

```
fn longest(x: &str, d: & str) -> &'static str {
    "ddd"
} 
```

幸运的是，这个程序编译并返回给我预期的结果🆒。但是如果我想返回对一个`String`的引用呢？所以我把代码改成了

```
fn longest(x: &str, d: & str) -> &'static str {
    &String::from("ddd")
} 
```

这次我得到了一些不同的错误信息，即

```
2 |     &String::from("ddd")
  |     ^-------------------
  |     ||
  |     |temporary value created here
  |     returns a reference to data owned by the current function 
```

这个错误消息很容易理解，一旦函数的作用域结束，在堆中为这个`String`结构创建的内存将被丢弃。在这种情况下，引用将成为一个悬空引用，这违反了编译器的规则。

好，现在让我们看看从参数中获取引用。同样，让我们考虑一下编译器看到签名时会怎么想。它看到了两个参数，但问题是这两个参数可能有不同的寿命。在这种情况下，编译器应该使用哪个生存期来创建引用？我们不知道，编译器也不知道，这就是它抱怨的原因。在这种情况下，我们应该指定它们的寿命。所以我们可以粘贴从`The Book`提供的代码。

```
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
} 
```

基于上面的代码，我们可以看到我们在告诉编译器`x`、`y`和返回值的生命周期都是`'a`。`'a`只是一个通用语句，它讲述了`x`和`y`的寿命。同时，它还告诉编译器返回值的寿命至少应该和 lifetime `a`一样长。因此，编译器会很高兴。它得到了所有需要的信息。

需要注意的一点是，我们通常会给所有参数加上`'a`，这是不必要的。在上面的例子中，因为返回值可能是`x`或`y`，直到运行时才知道。但是如果我们确定哪一个将被返回，比如说`x`，我们可以只指定`x`T5 的生存期

```
fn longest<'a>(x: &'a str, y: str) -> &'a str {
    x
} 
```

我们还可以为不同的参数指定不同的生存期，但是应该注意，返回值的生存期只能是一个。

```
fn longest<'a, 'b>(x: &'a str, y: &'b str) -> &'b str {
    y
} 
```

因此，总之，我们应该通知编译器每个引用的生存期，否则，它会报错。虽然有些时候编译器可以推断出来，但有些时候我们应该显式地告诉它。

[铁锈教程-寿命说明符讲解](https://www.youtube.com/watch?v=QoEX-Vu-R6k)

[生锈寿命](http://www.charlesetc.com/rust-lifetimes/)

[验证具有生存期的引用](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html#validating-references-with-lifetimes)