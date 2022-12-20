# 前端开发人员的 Rust:模块

> 原文：<https://dev.to/kennethlarsen/rust-for-frontend-developers-modules-19p8>

不久前，我决定开始学习 Rust。虽然它有很多有用的在线资源和一个非常友好的社区，但仍然有一些事情让我觉得很奇怪。这是因为 Rust 是一个与我习惯的前端心智模型非常不同的心智模型。

这意味着对很多人来说显而易见的事情对我来说并不明显。因此，我将尝试从一个前端开发人员的角度来记录这些事情。这次是关于使用模块。

* * *

Javascript 使用任何类型的导入或模块都是相当简单的。如果你有一个包含几个 JavaScript 文件的非常基本的 HTML 页面，你可以用一个`script`标签来添加它们。

如果您有更好的设置，您可以像这样导入 JavaScript 文件(或模块):

`require('cool-module')`甚至`import { function } from 'cool-module'`。

如果你使用一个像 Ember.js 这样的框架，有一个分离代码的标准，它也超越了这些模块导入。组件是独立的代码块，可以像这样调用:`<CoolComponent @arg="wow" />`。

关键是，有很多方法可以在前端分离代码，以确保您的代码库是可管理的。

## 模块带锈

当我试图制作 Ember-CLI 的 [Rust 克隆时，我很快发现`main()`功能已经失控。我想将代码分割成单独的文件，以确保我可以维护我想要添加的所有功能。](https://github.com/kennethlarsen/rember-cli)

这里有一些事情需要记住。

### 我的`npm install`呢？

好消息是有一个等价的系统来分发和使用包。在铁锈的世界里，这叫做[板条箱](https://crates.io/)。板条箱是铁锈社区的注册处。要从这个注册表或发布中使用包，你必须使用`cargo`包管理器。

如果你想像`npm install -g cool-module`一样全局安装一个包，那么你可以运行`cargo install cool-module`。

但是如果你想为你的项目安装一个包呢？老实说，我不确定是否有与`npm install --save-dev cool-module`等同的说法，因为所有的文档都提出了不同的东西。

文档化的方法是将包名和版本添加到项目的`Cargo.toml`中。这个文件相当于`package.json`。然后，当您构建项目时，它将获取依赖项。

现在，您必须记住将它导入到您需要的地方，如下所示:

`use package_name::module_name{function_name}`。

### 分离顾虑

回到我的主要问题。我想将代码分成不同的模块，以便在需要时导入。

我的项目目前是这样的:

*   包含运行 CLI 工具和处理用户输入的基础知识。
*   `new.rs`包含与用于生成新 Ember 项目的`new`命令相关的所有内容。
*   用于类似实用程序的功能，如创建一个进度条，并用用户输入的名称替换占位符值。

首先，我发现可以从包管理器中使用相同的导入方式，但是对于本地文件。我想使用来自`new.rs`的`create_new_application()`函数。

在`main.rs`年，我尝试了`use rember::new::{create_new_application}`

但是很快得到一个错误消息，说没有找到`rember::new`。

原来这些内部模块都要先声明。因为我正在构建一个 CLI 工具，所以它们必须放在一个名为`lib.rs`的文件中，如下所示:

`mod new;`

这使得我可以在`main.rs`中使用`new.rs`。

现在我有了另一个问题。我想在`new.rs`中使用`utils.rs`中的一个函数，并假设我必须使用完全相同的方法。但是现在我得到了模块没有找到的错误。

我仍然不太明白为什么，但是看起来对于没有在`main.rs`中导入的模块，他们必须使用`super`来代替包名`rember`。所以在`new.rs`里看起来是这样的:

`use super::utils::{create_progress_bar};`而不是`use rember::utils::{create_progress_bar};`。