# 教程:在 Rust 中使用平面缓冲器

> 原文：<https://dev.to/robert_winslow/tutorial-use-flatbuffers-in-rust-262k>

(本文[交叉贴](https://rwinslow.com/posts/use-flatbuffers-in-rust/)自[我的博客](https://rwinslow.com)。)

[FlatBuffers 项目](https://github.com/google/flatbuffers)是一个极其高效的模式版本化序列化库。在本教程中，你将学习如何在 Rust 中使用它。

要了解更多为什么我们需要另一种方式来编码数据，去读我的帖子[为什么是 FlatBuffers](https://rwinslow.com/posts/why-flatbuffers/) 。

FlatBuffers 是 Google 的一种序列化格式。它读写数据的速度真的很快:比 JSON 或 XML 快得多，通常比 Google 的其他格式，协议缓冲区还要快。它是模式版本化的，这意味着您的数据具有完整性(就像在关系数据库中一样)。FlatBuffers 支持十三种编程语言:C++、C#、C、Dart、Go、Java、JavaScript、Lobster、Lua、PHP、Python、Rust 和 TypeScript。

这篇文章将向你展示如何设置 FlatBuffers，然后在一个演示 Rust 程序中使用它。

(完全公开:我维护 Golang、Python 和 Rust FlatBuffers 代码库。)

本教程有七个简短的部分:

1.  [安装 FlatBuffers 编译器](#section-installation)
2.  [创建一个新的货物项目(如果需要)](#section-cargo-setup)
3.  [写一个 FlatBuffers 模式定义](#section-schema)
4.  [从模式中生成 Rust 访问器代码](#section-generate)
5.  [安装 FlatBuffers Rust 运行时库](#section-install-runtime)
6.  [编写一个演示 Rust 程序，对示例数据进行编码和解码](#section-demo)
7.  [了解更多并参与其中](#section-involved)

如果你想在一个地方看到所有的代码，我已经把这个项目放在了 GitHub 的仓库里。

## 1。安装 FlatBuffers 编译器

首先，让我们安装编译器。

编译器仅用于开发。这意味着您在生产环境中不再需要担心新的系统依赖性！

### 在 OSX 用自制软件安装

在我的 OSX 系统上，我使用[自制软件](https://github.com/Homebrew/homebrew)来管理软件包。要更新家酿库并安装 FlatBuffers，运行:

```
$ brew update
$ brew install flatbuffers 
```

(和往常一样，在我的博客上，我用前缀`$`表示 CLI 输入。)

我个人喜欢从官方的 Git 库安装最新的开发版本:

```
$ brew update
$ brew install flatbuffers --HEAD 
```

如果成功，您将可以从您的 shell 访问到`flatc`程序。为了验证它已经安装，执行`flatc` :

```
$ flatc
flatc: missing input files
... 
```

### 其他安装方法

如果你想从源代码安装，安装一个 Windows 可执行文件，或者为 Visual Studio 编译，请到我的帖子 [Installing FlatBuffers](https://rwinslow.com/posts/how-to-install-flatbuffers/) 获取更多信息。

## 2。创建一个新的货物项目(如果需要)

(如果您要将 FlatBuffers 添加到现有 Rust 项目，可以跳过这一步。)

使用以下命令创建一个基本的货物配置:

```
$ cargo new rust_flatbuffers_example
     Created binary (application) `rust_flatbuffers_example` package 
```

现在会有一个名为`rust_flatbuffers_example`的目录。将当前工作目录更改为:

```
$ cd rust_flatbuffers_example 
```

现在，请注意，该目录包含以下文件:

```
$ tree
.
|-- Cargo.toml
`-- src
    `-- main.rs 
```

最后，检查货物包装是否配置正确。通过运行 Cargo 自动生成的示例程序来做到这一点:

```
$ cargo run --quiet
Hello, world! 
```

如果您看不到这个输出，那么请查看关于设置 Rust 和 Cargo 的[官方文档，对您的配置进行故障排除。](https://doc.rust-lang.org/cargo/)

## 3。编写一个 FlatBuffers 模式定义

FlatBuffers 中的所有数据都由模式定义。FlatBuffers 中的模式是纯文本文件，它们在用途上类似于 Postgres 等数据库中的模式。

我们将处理构成网站用户详细信息的数据。这是一个微不足道的例子，但对入门很有帮助。下面是模式:

```
// myschema.fbs
namespace users;

table User {
  name:string;
  id:ulong;
}

root_type User; 
```

将上述代码放在名为`myschema.fbs`的文件中，该文件位于您的 Cargo 项目的根目录下。

这个模式定义了`User`，它保存了一个用户的`name`和`id`。这些类型的名称空间是`users`(这将是生成的 Rust 包的名称)。在我们的对象层次结构中，最顶层的类型是根类型`User`。

模式是 FlatBuffers 的核心部分，我们对它还只是皮毛。可以有默认值、向量、对象中的对象、枚举等等。如果你很好奇，可以去阅读关于模式格式的[文档。](https://google.github.io/flatbuffers/md__schemas.html)

## 4。从模式中生成 Rust 访问器代码

下一步是使用`flatc`编译器为我们生成 Rust 代码。它接受一个模式文件作为输入，并输出现成的 Rust 代码。

在包含`myschema.fbs`文件的目录中，运行以下命令:

```
$ flatc --rust -o src myschema.fbs 
```

这将在预先存在的`src`目录中一个名为`myschema_generated.rs`的新文件中生成 Rust 代码。这是我们的项目之后的样子:

```
$ tree
.
|-- Cargo.lock
|-- Cargo.toml
|-- myschema.fbs
`-- src
    |-- main.rs
    `-- myschema_generated.rs

1 directory, 6 files 
```

请注意，每个模式文件都会生成一个文件。

快速浏览`src/myschema_generated.rs`可以看到生成的文件有三个部分。以下是考虑不同功能组的方法:

*   读取`User`数据的类型定义和初始化器

```
pub struct User { ... }
pub fn get_root_as_user(buf: &[u8]) -> User { ... }
pub fn get_size_prefixed_root_as_user(buf: &[u8]) -> User { ... }
pub fn init_from_table(table: flatbuffers::Table) -> Self { ... } 
```

*   实例方法提供对`User`数据的读访问

```
pub fn name(&self) -> Option<&'a str> { ... }
pub fn id(&self) -> u64 { ... } 
```

*   用于创建新`User`对象的功能

```
pub fn create(_fbb: &mut flatbuffers::FlatBufferBuilder, args: &UserArgs) -> flatbuffers::WIPOffset { ... } 
```

(注意，我在上面的代码中省略了一些生存期注释。)

我们将在编写演示程序时使用这些函数。

## 5。安装 FlatBuffers Rust 运行时库

官方 FlatBuffers Rust 运行时包托管在 crates.io: [官方 FlatBuffers 运行时 Rust 库](https://crates.io/crates/flatbuffers)。

要在您的项目中使用它，请将`flatbuffers`添加到`Cargo.toml`中的依赖清单中。该文件现在应该类似于下面这样:

```
[package]
name = "rust_flatbuffers_example"
version = "0.1.0"
authors = ["rw <me@rwinslow.com>"]
edition = "2018"

[dependencies]
flatbuffers = "*" 
```

我使用`*`来获取最新的包版本。一般来说，你需要选择一个特定的版本。你可以在[货物依赖格式](https://doc.rust-lang.org/cargo/guide/dependencies.html)的文档中了解更多信息。

## 6。编写一个演示 Rust 程序来编码和解码示例数据

现在，我们将用代码覆盖默认的 Cargo“Hello World”程序来读写我们的 FlatBuffers 数据。

(我们这样做是为了简单，这样我就可以避免在这里解释货物构建系统。要了解更多关于 Cargo 项目的信息，请访问关于 Cargo 项目文件布局的官方文档。)

### 进口

首先，我们将使用`mod`语句导入生成的模块。将以下代码放入`src/main.rs` :

```
// src/main.rs part 1 of 4: imports
extern crate flatbuffers;

mod myschema_generated;

use flatbuffers::FlatBufferBuilder;
use myschema_generated::users::{User, UserArgs, finish_user_buffer, get_root_as_user}; 
```

关键字`mod`的这种用法指示 Rust build 系统使我们的程序可以访问名为`myschema_generated.rs`的文件中的项目。`use`语句使得两个生成的类型`User`和`UserArgs`可以用方便的名字被我们的代码访问。

### 写作

FlatBuffer 对象直接存储在字节片中。每个 Flatbuffers 对象都是使用我们用`flatc`编译器生成的函数构建的。

将以下代码片段附加到您的`src/main.rs` :

```
// src/main.rs part 2 of 4: make_user function
pub fn make_user(bldr: &mut FlatBufferBuilder, dest: &mut Vec<u8>, name: &str, id: u64) {
    // Reset the `bytes` Vec to a clean state.
    dest.clear();

    // Reset the `FlatBufferBuilder` to a clean state.
    bldr.reset();

    // Create a temporary `UserArgs` object to build a `User` object.
    // (Note how we call `bldr.create_string` to create the UTF-8 string
    // ergonomically.)
    let args = UserArgs{
        name: Some(bldr.create_string(name)),
        id: id,
    };

    // Call the `User::create` function with the `FlatBufferBuilder` and our
    // UserArgs object, to serialize the data to the FlatBuffer. The returned
    // value is an offset used to track the location of this serializaed data.
    let user_offset = User::create(bldr, &args);

    // Finish the write operation by calling the generated function
    // `finish_user_buffer` with the `user_offset` created by `User::create`.
    finish_user_buffer(bldr, user_offset);

    // Copy the serialized FlatBuffers data to our own byte buffer.
    let finished_data = bldr.finished_data();
    dest.extend_from_slice(finished_data);
} 
```

这个函数接受一个 FlatBuffers `Builder`对象，并使用生成的方法写入用户名和 ID。

注意，`name`字符串是用`bldr.create_string`函数创建的。我们这样做是因为，在 FlatBuffers 中，像字符串这样的可变长度数据需要在引用它们的对象之外创建*。在上面的代码中，这仍然是符合人体工程学的，因为我们可以从`UserArgs`对象中内联调用`bldr.create_string`。*

### 阅读

FlatBuffer 对象存储为字节片，我们使用生成的函数(在`myschema_generated.rs`中由`flatc`编译器为我们生成)访问里面的数据。

将以下代码附加到您的`src/main.rs` :

```
// src/main.rs part 3 of 4: read_user function
pub fn read_user(buf: &[u8]) -> (&str, u64) {
    let u = get_root_as_user(buf);
    let name = u.name().unwrap();
    let id = u.id();
    (name, id)
} 
```

该函数将一个字节片作为输入，并为`User`类型初始化一个 FlatBuffer 读取器。然后，它让我们能够访问字节片中的名称和 ID 值。

### 主要功能

现在我们把它们绑在一起。这是`main`功能:

```
// src/main.rs part 4 of 4: main function
fn main() {
    let mut bldr = FlatBufferBuilder::new();
    let mut bytes: Vec<u8> = Vec::new();

    // Write the provided `name` and `id` into the `bytes` Vec using the
    // FlatBufferBuilder `bldr`:
    make_user(&mut bldr, &mut bytes, "Arthur Dent", 42);

    // Now, `bytes` contains the serialized representation of our User object.

    // To read the serialized data, call our `read_user` function to decode
    // the `user` and `id`:
    let (name, id) = read_user(&bytes[..]);

    // Show the decoded information:
    println!("{} has id {}. The encoded data is {} bytes long.", name, id, bytes.len());
} 
```

这个函数写，读，然后打印我们的数据。注意`bytes`是带有编码数据的字节向量。这是您可以通过网络发送或保存到文件中的序列化数据。

### 奔跑吧

```
$ cargo run
Arthur Dent has id 42\. The buffer is 48 bytes long. 
```

概括地说，我们在这里所做的是编写一个简短的程序，该程序使用生成的代码来写入，然后读取一个字节片，我们在其中为一个示例用户编码数据。这个用户的名字是“Arthur Dent ”, ID 42。

## 7。了解更多并参与其中

FlatBuffers 是一个活跃的开源项目，有 Google 的支持。它是 Apache 许可的，可用于 C++、C#、C、Dart、Go、Java、JavaScript、Lobster、Lua、PHP、Python、Rust 和 TypeScript(更多语言即将推出！).

以下是一些帮助您入门的资源:

*   [GitHub 库](https://github.com/google/flatbuffers)
*   [问题跟踪者](https://github.com/google/flatbuffers/issues)
*   [正式文件](https://google.github.io/flatbuffers/)

以及我在 FlatBuffers 上的[额外博文。](https://rwinslow.com/tags/flatbuffers/)