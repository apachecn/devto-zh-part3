# Rust 编程语言中的模块

> 原文：<https://dev.to/saiumesh/modules-in-rust-programming-language-495m>

[![Alt text of image](img/0f82937eef71bc36a8b11005098db4a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7lW6kNc0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2AwL9FvRCwlO8X0ysJ8348kw.png)

如果你是 rust 新手，我强烈建议你去 rust 网站查看 rust 文档，它们非常棒。

模块是组织代码并在必要时重用代码的较好方法之一。

Rust 模块系统与我在 JavaScript、GoLang 和 Python 等语言中使用过的完全不同。当我开始学习这种模式时，我并不真的喜欢它，但是在使用了一段时间后，现在我真的喜欢它的工作方式。

你可以在这里找到示例代码。

首先，我们将了解如何安装外部模块(板条箱)。要安装，你所要做的就是进入 **Cargo.toml** 添加板条箱名称，然后用命令 **cargo run** 运行你的项目。一旦你运行这个命令，货物将安装到你的项目板条箱。你的 **Cargo.toml** 大概会如下图。

```
[package]
name = "rust-mod-example"
version = "0.1.0"
authors = ["saiumesh"]
edition = "2018"

[dependencies]
uuid = { version = "0.7", features = ["serde", "v4"] } 
```

现在使用板条箱，可能如下😁

```
 // main.rs

// this is external crate, which we installed via cargo.toml
use uuid::Uuid;

fn get_uuid() -> String {
    Uuid::new_v4().to_string()
}

fn main() {
    let uuid = get_uuid();
    println!("the uuid is {}", uuid);

} 
```

现在让我们创建一个本地模块，看看我们如何使用它。

在我们写任何进一步的代码之前，有两种方法可以在 rust 中创建 mod，
你认为在 rust 中每个文件和文件夹都是 mod。

首先我们用**文件**
创建 mod

```
 // filemod.rs

use uuid::Uuid;

// pub indicates that, we access this function outside of it's self mod

pub fn get_uud() -> String {
    Uuid::new_v4().to_string()
}

// this is private function which cannot be accessed outside
fn cannot_access() {
    println!("I cannot be called!!");
} 
```

```
 // now in main.rs we will import/define mod by as following 

// now, we have created file called 'filemod'
// which is local mod goes by filename
mod filemod;
use filemod::get_uud;

fn main() {
    // local module which goes by filename
    let file_mod_uuid = get_uud();
    println!("local module uuid is {}", file_mod_uuid);

} 
```

现在让我们创建一个 mod，用**文件夹**

如前所述，folder 可以是一个 mod，只是略有不同。

当我们用文件夹创建 mod 时，该文件夹应该有名为 **mod.rs** 的文件

这有助于 rust 将这个文件夹识别为其中一个模块。

```
Folder/{mod.rs, filename.rs} 
```

这可能有点混乱，但是，相信一旦你熟悉了它，我肯定你会爱上它。

现在让我们创建名为 **foldermodule** 的文件夹，在该文件夹中我们首先创建 **mod.rs** ，然后创建 **foldermodulefile.rs**

```
foldermodule/mod.rs

// now make file module public
pub mod foldermodulefile; 
```

```
foldermodule/foldermodulefile.rs

use uuid::Uuid;

pub fn folder_module_uuid() -> String {
    Uuid::new_v4().to_string()
} 
```

现在让我们在 main
中导入这个模块

```
 // now , we have created folder called 'foldermodule'
// which is local mod goes by module called 'foldermodule'
mod foldermodule;
// you use following syntax for importing multiple functions from same mod
use foldermodule::foldermodulefile::{ folder_module_uuid, mod_to_mod_function };

fn main() {

 // local folder module
    let folder_mod_uuid = folder_module_uuid();
    println!("folder module uuid is {}", folder_mod_uuid);

} 
```

到目前为止，我们已经知道了如何在 main 中使用文件/文件夹模块，现在让我们看看如何从其他 mod 中调用 mod 函数

现在让我们创建模型

```
//modtomod/mod.rs
pub mod something; 
```

```
//modtomod/something.rs
pub fn mod_to_mod() -> String {
    String::from("Hey!! mod")
} 
```

从其他 mod 调用 mod 函数，有一点不同，你不能只写**使用 modulename::function_name** ，那会抛出范围外错误。

因为 main.rs 将所有东西绑定在一起，所以需要如下定义 main.rs 中的每个模块。

```
main.rs

// till now we have seen using local modules in main file
// now let's see how to do mod to mod rather mod to main
// to use mod in other mod first we need to define called mod in
// main, so that caller mod can have it's scope
mod modtomod; 
```

现在让我们开始我们的**文件夹模块**模块调用其他模块函数

要从其他模块调用一个 mod 函数，我们将使用下面的语法。

```
 // now let's call other local mod
use modtomod::something; 😢😢

// now let's call other local mod
use crate::modtomod::something; 😁😁 
```

您调用与下面代码
相同的函数

```
// now let's call other local mod
use crate::modtomod::something;

pub fn mod_to_mod_function() {
    println!("hey!! from other mod {}", something::mod_to_mod());
} 
```

希望我对 Rust 语言中的模块有所了解。如果您有任何疑问或建议，可以在我的 twitter 上联系我。在那之前，我会在其他岗位上看到你。和平❤❤

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[saiumes 535](https://github.com/saiumesh535)/[rust-modules-example](https://github.com/saiumesh535/rust-modules-example)

### 生锈的模块

<article class="markdown-body entry-content container-lg" itemprop="text">

# rust 模块-示例

文章发表[此处](https://dev.to/saiumesh/modules-in-rust-programming-language-495m)

</article>

[View on GitHub](https://github.com/saiumesh535/rust-modules-example)