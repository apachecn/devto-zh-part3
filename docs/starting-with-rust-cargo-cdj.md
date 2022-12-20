# 从生锈的货物开始

> 原文：<https://dev.to/vijesh_salian/starting-with-rust-cargo-cdj>

在上一篇文章中，我们看到了如何编译一个 rust 文件。rs 文件)与 rust 编译器。这是你感兴趣的帖子。

[![vijesh_salian image](img/4613f5db1ac3ed6c0a5a662a8f37f31e.png)](/vijesh_salian) [## 从铁锈开始

### vijesh Salian 6 月 4 日 193 分钟阅读

#rust #programming #learning](/vijesh_salian/starting-with-rust-2713)

在现实世界中，程序员编写的应用程序可以由几个文件组成。这些文件被组合在一起，我们称之为项目。一个应用程序中有多个项目是很常见的。在大多数情况下，不止一个开发人员参与构建应用程序。每个项目都有明确的责任。承担类似职责的 Rust 文件通常在同一个项目中。一个 Rust 文件可能依赖于另一个文件是很常见的。一个 Rust 项目依赖于另一个项目也很常见。如果项目 X 依赖于项目 Y，那么 Y 是项目 X 的一个依赖项，一个 Rust 项目可以有多个依赖项，它应该列出并知道它们。然而，依赖关系不会也不应该知道依赖它的项目。有时候一个项目可能有一个依赖项，这个依赖项是由团队/公司之外的开发人员编写的。他们可以在中央存储库中提供依赖项，以便您可以下载和使用它们。

因此，在使用 Rust 时，你将会编译、下载依赖项、构建很多东西。这是货物进入画面的地方。如果你一直在用其他语言编程，所有这些可能看起来是显而易见的，你可能已经知道 Cargo 能做什么。

如何使用货物？

首先让我们检查货物是否安装正确。点燃你的外壳。

```
cargo --version 
```

如果你看到一个版本号，你就可以走了。如果没有，搜索引擎是你的朋友。

```
cargo new hello_rust 
```

这是创建新项目的命令。我们创建了一个名为 hello_rust 的新项目。这将创建一个带有项目名称的目录。如果你查看目录，你会发现 Cargo 用 git repo 初始化了它。如果你愿意，你可以选择不这样做。然后你会发现一个 Cargo.toml 文件。该文件是项目的配置文件。在项目目录中，您还会发现另一个名为 src 的目录。这包含了 Rust 代码文件。

Cargo.toml 文件包含关于项目的元数据和依赖项列表(如果有的话)。

src 目录包含一个您熟悉的 Rust 文件。

好吧，我还能对货物做些什么？

我们可以用货物来构建和运行我们的代码。Cargo 知道如何使用 rustc 编译器。

在您的 shell 中，在您的项目目录下运行

```
cargo run 
```

这个命令只需要一个命令就可以构建和运行您的项目。现在这看起来可能微不足道，但是随着你的项目越做越大，货物的好处变得更加明显。这样，Cargo 为所有大小项目提供了构建和管理依赖关系的统一方式。

如果您想快速检查您的代码是否编译，但不想运行它，那么使用这个命令。

```
cargo check 
```

如果您想编译和构建一个可执行文件，但不想运行它，那么使用这个命令。

```
cargo build 
```

如果你想发布你的程序，使用这个命令在发布模式下创建一个可发布的程序。

```
cargo build --release 
```

很好，那我还能拿货物做什么呢？

如果你想了解更多关于货物的知识，你会发现货物手册内容广泛。点击此处获取货物手册。[https://doc.rust-lang.org/cargo/index.html](https://doc.rust-lang.org/cargo/index.html)

感谢您的关注。
干杯🍺