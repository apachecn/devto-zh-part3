# 从铁锈开始

> 原文：<https://dev.to/vijesh_salian/starting-with-rust-2713>

我从事编程已经超过 10 年了，但是我只精通一门语言。即使我可能会使用其他语言，但除了我的母语之外，我不能说自己精通任何其他语言。这让我很不舒服。学习一门不同的语言会给你一个非常需要的关于编程的不同视角。所以我决定学习其他语言。有太多的语言可供选择。我决定选择两种语言，这两种语言给我的面向对象思维思维带来了轻微的反直觉。我选了 F#和 Rust。我不是语言设计爱好者，所以我不会谈论语言的优缺点。作为一名程序员，我想充分了解这门语言，以便能够构建可靠的系统。为了避免已知的陷阱，需要掌握这门语言的专业知识。在这篇文章中，以及接下来的几篇，我将会写一些关于铁锈的内容。这个职位将涵盖介绍和安装的铁锈。

Rust 从 Mozilla 内部开始了它的旅程。[https://research.mozilla.org/rust/](https://research.mozilla.org/rust/)。它是一种开源编程语言，仍然由 Mozilla 赞助。https://github.com/rust-lang/rust[。](https://github.com/rust-lang/rust)

众所周知，Rust 是一种系统编程语言。据我所见，它也是一种通用编程语言。那么你能用铁锈建造什么呢？您可以构建 web 应用程序、分布式应用程序、嵌入式应用程序等等。如果你愿意，你甚至可以构建自己的操作系统。*铁锈有什么特别的？* Rust 是作为 C 和 C++的替代系统编程语言从零开始构建的，但没有这些语言的已知缺陷，尤其是在内存管理方面。它是一种现代语言，解决了许多问题，使得构建系统具有高度弹性，这是其他编程语言很难实现的。据说，如果你写的代码可以在 Rust 中编译，你就可以不用做微优化就能写出高效的代码。

*你怎么开始生锈的*
从这里给你的机器安装铁锈[https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install)。

如果您在 Windows 上，请确保在 path 系统变量中添加 Rust path。

`rustup`是一个命令行工具，管理 Rust 版本和开发所需的其他相关工具。

通过在 shell 中运行以下命令来更新 Rust 安装。

```
rustup update 
```

运行这个来检查 Rust 版本。如果它显示一个版本，那么它已经正确安装。

```
rustc --version 
```

注意单词 version 前面的--。这很重要；这是命令的一个参数。我第一次尝试这个命令时没有——得到了这个奇怪的错误。

```
error: couldn't read version: The system cannot find the file specified. (os error 2)

error: aborting due to previous error 
```

所以，如果你看到这个错误，那么再次检查语法，你可能会错过。还要注意我在 Windows 10 上。根据您的操作系统，您可能会有不同的体验。

好了，现在给我看看代码！

让我们做一个强制性的 Hello World 节目。

创建一个您希望该程序驻留的目录。

```
mkdir HelloRust
cd HelloRust 
```

创建一个名为 main.rs 的文件，键入以下代码并保存该文件。

```
fn main() {
    println!("Hello Rust");
} 
```

转到 shell 并运行命令来编译 rust 代码。

```
rustc main.rs 
```

然后运行主可执行文件。在 Windows 上是 main.exe 文件。你应该看看哈罗·拉斯特。

如果你看一下代码，你会看到它定义了一个主函数并打印了一个字符串。如果你以前一直在编码，这个语法是显而易见的。可能有些东西看起来不太熟悉！印刷之后。嗯，这是我们可能需要在以后的时间点覆盖的东西。这篇文章不是为此而写的。

如果你想了解 Rust，官方文档是很棒的。Rust 官方网站有一整本写得很好的书是免费的。你可以在这里找到它。[https://doc.rust-lang.org/book/title-page.html](https://doc.rust-lang.org/book/title-page.html)

如果你想知道我关于 Rust 的实验，那么请关注这个空间。感谢您的关注。

干杯！🍺