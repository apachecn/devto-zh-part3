# 生锈寿命，生锈新手的高墙

> 原文：<https://dev.to/takaakifuruse/rust-lifetimes-a-high-wall-for-rust-newbies-3ap>

# 学锈难

我最近开始学习 Rust。这种语言很有名，但同时也声名狼藉。每个人都说它的学习曲线很陡，一些概念(主要是借款、所有权和寿命)很难掌握。

让我解释一些与生命周期相关的概念。(不要假设我是一生的专家，即使我决定写这篇文章，我觉得我还是不明白这个概念。此外，这篇文章只是书和名词的释义。)

如果你想在你的机器上运行你的代码，用 Rust 1.31.1，当前(2019 年 1 月)stalbe 运行。

大部分的代码都是从[书](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html)、[诺米肯](https://doc.rust-lang.org/nomicon/lifetimes.html)那里抄来的。

# 一生基础知识

这个不编译，但是

```
fn main() {
    {
        let r;

        {
            let x = 5;
            r = &x;
        }

        println!("r: {}", r);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这确实编译了

```
fn main() {
    let r;

    let x = 5;
    r = &x;

    println!("r: {}", r);
} 
```

Enter fullscreen mode Exit fullscreen mode

为什么？Rust 的变量存在于它们的作用域内(在“{”和“}”之间)。如果它们超出了边界，编译器会杀死它们，就像监狱一样。

第一个代码的问题是 var r，r 有一个从 x 借来的值，我们要求 Rust 在最后一行打印出来。注意，x 在把他的值借给 r 后不久就死了，所以，在最后一行，我们基本上是想打印出一个不存在的值。Rust 大叫错误。

解决办法很简单。删除作用域，让 r an x 一直存在到主函数结束。

根据 nomicon 的说法，铁锈决定了每个变种的寿命。

这个简单的代码

```
 fn main() {
    let x = 0;
    let y = &x;
    let z = &y;
} 
```

Enter fullscreen mode Exit fullscreen mode

只是一个糖衣炮弹。如果我们去糖，它看起来像这样。

```
fn main() {
    'a: {
        let x: i32 = 0;
        'b: {
            let y: &'b i32 = &'b x;
            'c: {
                // ditto on 'c
                let z: &'c &'b i32 = &'c y;
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

诺米肯说生命只是一个区域。是的，它看起来像代码块。

(不幸的是，终身边界是不可见的，也没有办法做到。我希望它是。Nashenas88 正在进行可视化项目。祝你好运！！)

# 功能和寿命

这段代码不编译

```
#[allow(unused_variables)]
fn main() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let result = longest(string1.as_str(), string2);
}

fn longest(x: &str, y: &str) -> &str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

Rust 说...

```
error[E0106]: missing lifetime specifier
  --> src/main.rs:11:33
   |
11 | fn longest(x: &str, y: &str) -> &str {
   |                                 ^ expected lifetime parameter
   |
   = help: this function's return type contains a borrowed value, but the signature does not say whether it is borrowed from `x` or `y` 
```

Enter fullscreen mode Exit fullscreen mode

最长的函数要么返回 x，要么返回 y。没人知道到底是哪个。

在大多数情况下，Rust 试图根据简单的规则来假设 var 的生命周期(这被称为 lifetime elission)。在这种情况下，规则不适用。因此，我们需要手动添加生存期，让我们开始吧。

这将编译

```
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

给 x 和 y 不同的寿命怎么样？

这是行不通的，因为这个函数只返回 1 个结果。结果来了 1 个一生，但是哪个一生，x 的还是 y 的(我们不能说一个或者两个都是)？

# 终身子类型

让我们试试另一个例子。在这个例子中，我推断解析器包含上下文对象，parse_context 执行解析工作。

```
struct Context(&str);

struct Parser {
    context: &Context,
}

impl Parser {
    fn parse(&self) -> Result<(), &str> {
        Err(&self.context.0[1..])
    }
}

fn parse_context(context: Context) -> Result<(), &str> {
    Parser { context: &context }.parse()
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们编译，我们会得到错误。

```
error[E0106]: missing lifetime specifier
 --> src/lib.rs:1:16
  |
1 | struct Context(&str);
  |                ^ expected lifetime parameter

error[E0106]: missing lifetime specifier
 --> src/lib.rs:4:14
  |
4 |     context: &Context,
  |              ^ expected lifetime parameter

error[E0106]: missing lifetime specifier
  --> src/lib.rs:13:50
   |
13 | fn parse_context(context: Context) -> Result<(), &str> {
   |                                                  ^ help: consider giving it an explicit bounded or 'static lifetime: `&'static`
   |
   = help: this function's return type contains a borrowed value with an elided lifetime, but the lifetime cannot be derived from the arguments 
```

Enter fullscreen mode Exit fullscreen mode

再加一辈子吧。

```
struct Context<'a>(&'a str);

struct Parser<'a> {
    context: &'a Context<'a>,
}

impl<'a> Parser<'a> {
    fn parse(&self) -> Result<(), &'a str> {
        Err(&self.context.0[1..])
    }
}

fn parse_context<'a>(context: Context<'a>) -> Result<(), &'a str> {
    Parser { context: &context }.parse()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们会得到错误....

```
error[E0515]: cannot return value referencing function parameter `context`
  --> src/lib.rs:14:5
   |
14 |     Parser { context: &context }.parse()
   |     ^^^^^^^^^^^^^^^^^^--------^^^^^^^^^^
   |     |                 |
   |     |                 `context` is borrowed here
   |     returns a value referencing data owned by the current function 
```

Enter fullscreen mode Exit fullscreen mode

变量上下文只在 parse_context 函数中可用。因此，在函数结束时，解析器将引用一个不存在的变量。

我们做什么呢

我们需要两辈子。
这将编译(仅在 1.31.1 上，其他版本不编译。)

```
#![allow(dead_code)]

struct Context<'s>(&'s str);

struct Parser<'c, 's> {
    context: &'c Context<'s>,
}

impl<'c, 's> Parser<'c, 's> {
    fn parse(&self) -> Result<(), &'s str> {
        Err(&self.context.0[1..])
    }
}

fn parse_context(context: Context) -> Result<(), &str> {
    Parser { context: &context }.parse()
} 
```

Enter fullscreen mode Exit fullscreen mode

解析器包含上下文，解析器通过“解析”方法引用上下文。

这意味着上下文必须比解析器存在的时间更长。所以，我们至少应该告诉 Rust，两个生命周期是不同的。

(虽然我在写“我们需要两次生命”，但我还是不明白为什么我们需要两次生命。单一生存期意味着上下文和解析器同时死亡。有什么问题！)

(你认为阅读非词法生存期的 RFC 对理解生存期有好处吗，尽管它非常冗长？)