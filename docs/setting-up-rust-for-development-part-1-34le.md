# 为开发设置 Rust。(第一部分)

> 原文：<https://dev.to/henryong92/setting-up-rust-for-development-part-1-34le>

## T2】

This post aims to document the learning and development of my personal Rust programming mini adventure.

**注:**
以下信息来源于我自己收集和整理的零碎信息。所以这意味着你也可以从官方网站和互联网上的其他地方找到信息:)
当我找到更多的资源并填补这里提供的信息的空白/缺失时，我会更新这个帖子。

## 

### 读数:

| 链接 | 描述 |
| --- | --- |
| [https://doc.rust-lang.org/book](https://doc.rust-lang.org/book) | API 概述、安装、一般整体语言资源 |

> 下面的命令是将 Rust 安装到 Linux / Mac 系统上。

```
$ curl https://sh.rustup.rs -sSf | sh
$ rustup --update
$ rustc --version 
```

Enter fullscreen mode Exit fullscreen mode

> 就像 NodeJS 的 NPM 一样，Rust 有货物。Cargo 是 Rust 的构建系统和包管理器。大多数 Rustaceans 使用这个工具来管理他们的 Rust 项目，因为 Cargo 为您处理了很多任务，比如构建您的代码、下载您的代码所依赖的库，以及构建这些库。(我们称库为你的代码需要依赖。)
> 
> 您可以使用以下命令检查系统上当前的 Cargo 版本:

```
$ cargo --version 
```

Enter fullscreen mode Exit fullscreen mode

### 项目初始化:

> 启动 Rust 项目最简单的方法是使用以下一组命令:

```
$ touch learn_rust.rs 
```

Enter fullscreen mode Exit fullscreen mode

```
// sample starter code.
fn main() {
    println!("Hello, world!");
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ rustc build
$ rustc run 
```

Enter fullscreen mode Exit fullscreen mode

> 开始一个全新项目的更好方法是用 Cargo 初始化包管理器，以正确地开始实际的项目。

```
$ cargo init 
```

Enter fullscreen mode Exit fullscreen mode

> 要运行项目，您可以选择按原样构建项目以进行开发。
> 对于生产级优化构建，您可以选择运行一个命令，让您构建的代码运行得更高效、更快:

```
$ cargo build --release
$ cargo run 
```

Enter fullscreen mode Exit fullscreen mode

> 毫无疑问，如果您想在运行代码之前检查代码中的错误，在将代码转换为可执行文件之前，有一个命令可以先进行检查:

```
$ cargo check 
```

Enter fullscreen mode Exit fullscreen mode

### 设置新项目:

> 要设置一个新项目，请转到目录并键入以下命令:

```
$ cargo new <project name> 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

*   使用 rustup 安装 Rust 的最新稳定版本
*   更新到较新的 Rust 版本
*   打开本地安装的文档
*   写并运行一个你好，世界！直接使用 rustc 编程
*   使用 Cargo #的惯例创建并运行一个新项目