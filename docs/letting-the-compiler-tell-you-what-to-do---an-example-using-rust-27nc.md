# 让编译器告诉你该做什么——一个使用 Rust 的例子

> 原文：<https://dev.to/dmerejkowsky/letting-the-compiler-tell-you-what-to-do---an-example-using-rust-27nc>

*最初发表于[我的博客](https://dmerej.info/blog/post/letting-the-compiler-tell-you-what-to-do/)。*

如果你曾经用编译语言(C，C++，Java，…)编写过代码，你可能已经习惯了编译器错误消息，你可能认为这里只有防止你犯错。

嗯，有时候你也可以使用编译器错误消息来*设计和实现*新特性。让我用一个用 Rust 编写的简单命令行程序向您展示。

# 举个例子

以下是我们目前为止编写的代码:

```
use structopt::StructOpt;

#[derive(Debug, StructOpt)]
struct Opt {
    #[structopt(long = "--dry-run")]
    dry_run: bool,
}

fn main() {
    let opt = Opt::from_args();

    let dry_run = opt.dry_run;
    println!("dry run: {}", dry_run);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 [structopt 机箱](https://github.com/TeXitoi/structopt)实现了一个`--dry-run`选项。

现在我们想添加一个`--color`选项，它可以有以下值:`never`、`always`和`auto`。

但是 structopt(也不是 clap，它所依据的)难道*不是*有“选择”的概念，像`argparse`或者`docopt`。

所以我们*假装*有，我们写:

```
enum ColorWhen {
    Always,
    Never,
    Auto,
}

#[derive(Debug, StructOpt)]
struct Opt {
    #[structopt(long = "--dry-run")]
    dry_run: bool,

    #[structopt(
        long = "--color",
        help = "Whether to enable colorful output."
    )]
    color_when: ColorWhen,
}

fn main() {
  let opt = Opt::from_args();
  let dry_run = opt.dry_run;
  let color_when = opt.color_when;

  println!("dry run: {}", dry_run);
  println!("color: {}", color_when);
} 
```

Enter fullscreen mode Exit fullscreen mode

*注:这有时被称为“一厢情愿的编程”，可以用在各种情况下*。

无论如何，我们尝试编译这段代码，却面临着一堆编译错误。

这就是奇迹开始的地方。我们将在不改变 structopt 工作方式的情况下完成这项工作，只需要 T2 一个接一个地读取和修复编译器错误。准备好了吗？我们走吧！

## 错误 1

```
color_when: ColorWhen,
   | ^^^^^^^^^^^^^^^^^^^^^ `ColorWhen` cannot be formatted using `{:?}`
   |
   = help: the trait `std::fmt::Debug` is not implemented for `ColorWhen`
   = note: add `#[derive(Debug)]` or manually implement `std::fmt::Debug` 
```

Enter fullscreen mode Exit fullscreen mode

我们按照要求去做，并添加`#[derive(Debug)]`注释:

```
#[derive(Debug)]
enum ColorWhen {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯，那有什么容易的。让我们继续下一个错误。

## 错误 2

```
 | #[derive(StructOpt)]
  | ^^^^^^^^^ the trait `std::str::FromStr` is not implemented for `ColorWhen` 
```

Enter fullscreen mode Exit fullscreen mode

编译器告诉我们，它不知道如何将命令行参数(字符串)转换为枚举。

我们真的不记得`FromStr`特征包含了什么。我们可以查阅文档，但是我们也可以写一个空的实现，看看会发生什么:

```
impl std::str::FromStr for ColorWhen {

} 
```

Enter fullscreen mode Exit fullscreen mode

## 错误 3

同样，编译器告诉我们该做什么:

```
not all trait items implemented, missing: `Err`, `from_str`
  --> src/main.rs:10:1
   |
10 | impl std::str::FromStr for ColorWhen {}

missing `Err`, `from_str` in implementation
note: `Err` from trait: `type Err;`
note: `from_str` from trait:
  `fn(&str) -> std::result::Result<Self, <Self as std::str::FromStr>::Err>` 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个[关联类型](https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#specifying-placeholder-types-in-trait-definitions-with-associated-types) `Err`，和一个`from_str()`函数。

让我们从 Err 类型开始。我们需要告诉用户无效的`--color`选项，所以让我们使用一个带有包含描述的`InvalidArgs`结构的枚举:

```
#[derive(Debug)]
enum FooError {
  InvalidArgs { details: String },
} 
```

Enter fullscreen mode Exit fullscreen mode

注意编译器是如何几乎“强迫”我们拥有自己的错误类型的，这是一个非常好的实践！

反正随着`from_str`函数。

```
impl std::str::FromStr for ColorWhen {
    type Err = FooError;

    fn from_str(s: &str) -> Result<ColorWhen, FooError> {
        match s {
            "always" => Ok(ColorWhen::Always),
            "auto" => Ok(ColorWhen::Auto),
            "never" => Ok(ColorWhen::Never),
            _ => {
                let details = "Choose between 'never', 'always', 'auto'";
                Err(FooError::InvalidArgs { details: details.to_string() })
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 错误 4

```
error[E0599]: no method named `to_string` found
  for type `FooError` in the current scope 
```

Enter fullscreen mode Exit fullscreen mode

所有定制的错误类型都应该可以转换成字符串，所以让我们实现它:

```
impl std::string::ToString for FooError {
    fn to_string(&self) -> String {
        match self {
            FooError::InvalidArgs { details } => details.to_string(),
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它编译！

让我们检查错误处理:

```
$ cargo run -- --color foobar
error: Invalid value for '--color <color_when>':
  Choose between 'never', 'always', 'auto' 
```

Enter fullscreen mode Exit fullscreen mode

让我们检查一个有效的选择:

```
$ cargo run -- --color never
dry run: false color: Never 
```

Enter fullscreen mode Exit fullscreen mode

有用！

## 默认

还有一个小问题:我们没有对`color_when`字段使用`Option`，所以实际上需要`--color`命令行标志:

```
$ cargo run
error: The following required arguments were not provided:
    --color <color_when> 
```

Enter fullscreen mode Exit fullscreen mode

不能怪铁锈。这是我们的错误，因为我们没有首先使用可选的 ColorWhen 字段。

让我们试着用`Option<>`代替
来解决这个问题

```
// ...
struct Opt {
    // ...
    #[structopt(
        long = "--color",
        help = "Wether to enable colorful output"
    )]
    color_when: Option<ColorWhen>,
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，因为我们没有对`opt.color_when`做任何事情，只是打印了它，所以一切仍然正常:)

## 错误 5

我们是否尝试过像这样直接使用选项:

```
fn force_no_color() {
  // ...
}

fn main() {
  let color_when = opt.color_when;

  match color_when {
    ColorWhen::Never => force_no_color(),
    // ..
  } 
```

Enter fullscreen mode Exit fullscreen mode

编译器会告诉我们我们的错误:

```
ColorWhen::Never => force_no_color(),
^^^^^^^^^^^^^^^^ expected enum `std::option::Option`, found enum `ColorWhen` 
```

Enter fullscreen mode Exit fullscreen mode

我们将被迫处理默认值，例如:

```
let color_when = color_when.unwrap_or(ColorWhen::Auto); 
```

Enter fullscreen mode Exit fullscreen mode

## 侧注

通过利用`default`特征:
，我们可以使用另一个很酷的技巧来达到同样的结果

```
#[derive(Debug)]
enum ColorWhen {
    Always,
    Never,
    Auto,
}

impl std::default::Default for ColorWhen {
    fn default() -> Self {
        ColorWhen::Auto
    }
}

fn main {
    let color_when = opt.color_when.unwrap_or(ColorWhen::default());
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码有点长，但我觉得它可读性更强，也更能“揭示意图”。

(旁注结束)

## 结论

我希望这让您对一个好的编译器能做什么有了新的认识，或者至少感受一下编写 Rust 是什么样子的。

我把这种新的工作流程称为“编译器驱动的开发”，我发现它很好地补充了其他众所周知的工作流程，比如 TDD。

*最后注意:老实说，我们也可以通过阅读文档获得更好的结果:例如，我们可以使用一个[定制字符串解析器](https://docs.rs/structopt/0.2.14/structopt/#custom-string-parsers)来代替`FromStr`样板文件，并在我们的[定制错误](https://doc.rust-lang.org/rust-by-example/error/multiple_error_types/define_error_type.html)上实现显示特征。好医生也很重要…*

干杯！

* * *

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。