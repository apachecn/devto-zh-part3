# 锈鳞蟒:基础实验

> 原文：<https://dev.to/tuned/rust-scales-python-basic-experiment-49h0>

[![pic by https://twitter.com/MittermeierFlx](img/2ec3a0456b9fc782735e4eefe78d6cb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FmB4D8Rv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qpvdvmwlck64foz2lgb2.jpg)

## 点点滴滴

对于一个中级/高级 Python 工程师来说，从/到 C 和 C++的 Python 绑定可能是最热门的话题之一，他们试图对剖析和优化以及 Python 虚拟机的内部功能有所了解。这些低级系统语言可能是理解 Python 堆栈和更广泛的语言家族的伙伴。

这个家族中最新最引人注目的成员可能是 [Rust，这是一种 Mozilla 支持的语言，自 2015 年以来提供了一种稳定的 API](https://en.wikipedia.org/wiki/Rust_%28programming_language%29):围绕它的采用有很多热情，在本文中，我尝试了一个实验，可能会提出一个原因；除了许多其他优势之外，你已经可以在[中读到许多关于](https://medium.com/search?q=rustlang)的其他文章。我发现促使我学习 Rust 的主要原因是:

*   从设计上来说，它是**多范例**:

(类似 C 的结构，除了静态类型之外还有功能工具等等)最流行的语言都是以一种范式为参考来设计的，并试图通过添加到各自的标准库中来增加对其他范式的支持。不幸的是，这最终导致了一个让许多工程师不满意的组合；单一范例设计的结果通常是表达可能性的限制。

*   它的设计考虑了整个工作流程/生命周期的**用户体验**:

(从安装到发布，通过测试、文档、包管理)大多数语言在达到临界质量以证明自动化/生产力工具的开发之前，它们的核心能力已经增长了；这导致了在开发体验的不同阶段使用的工具之间的差距、冲突和质量差异。

*   目前，它提供了与 [WebAssembly (Wasm)](https://en.wikipedia.org/wiki/WebAssembly) 的最佳集成水平，并且可能在以下方面:

WebAssembly 已经达到了一个稳定的接口，它现在是一个标准；它将是构建网络软件的平台。Rust 提供了对 Wasm 的直接访问，就像我们在这个实验中要展示的一样简单。

让我们看看启动一个项目，在 Rust 中创建一个函数，在 WebAssembly 中编译库，最后从 Python 中调用这个函数(耶！没有关于所有内存安全陷阱和编译标志的 C 绑定的工作！Python 会调用原生 WASM 模块)。

## 简单即重要

您可以通过安装以下软件来尝试这种实验:

*   *:工具链管理器，导航 Rust 的生态系统的多样性*
**   *货物*:包装经理*   rustc :编译器，与你的机器的稳定的 Rust 工具链一起工作。*

 ****rustup* 为你安装这一切！**

让我们开始:

.1.添加 Wasm 工具链:

```
 rustup target add wasm32-unknown-unknown 
```

Enter fullscreen mode Exit fullscreen mode

.2.创建新项目并更改目录:

```
cargo new medium-xp && cd medium-xp 
```

Enter fullscreen mode Exit fullscreen mode

.3.在`src`目录下创建一个`lib.rs`文件。所有代码都放在`src`，`lib.rs`是 Rust 保留根库代码的地方。

.4.`Cargo.toml`是你的包配置文件，把这个部分添加到`Cargo.toml`文件:

```
[lib]
crate-type = [“cdylib”] 
```

Enter fullscreen mode Exit fullscreen mode

这是为了将项目定义为一个动态库

.5.是您的入口点模块，我们这次不打算使用它，因为我们正在创建一个库，而不是二进制文件。在`lib.rs`中为我们的函数添加以下代码:

```
#[no_mangle]
pub extern fn simple_add(a: i32, b: i32) -> i32 { a + b} 
```

Enter fullscreen mode Exit fullscreen mode

.6.为 Wasm 编译库:

```
cargo build --release --target wasm32-unknown-unknown 
```

Enter fullscreen mode Exit fullscreen mode

现在你会发现你的库在`target/wasm32-unknown-unknown/release`中编译成了 WASM 模块！

.7.在 Python 方面:

*   为 Python 安装 [**wasmer**](https://github.com/wasmerio) 扩展(在 Python 3.6 上测试):

```
 python3.6 -m pip install wasmer 
```

Enter fullscreen mode Exit fullscreen mode

*   使用以下脚本创建一个`run-wasm-from-python.py`文件:

```
from wasmer import Instance
path = '<relative path to your Wasm file>'
with open(path, ‘rb’) as bytecode:
    wasm_bytes = bytecode.read()
    instance = Instance(wasm_bytes)
    result = instance.exports.simple_add(12, 12)
    print('Modules exported from Rust: ')
    print(instance.exports)  # this will print function's name
    print('call simple_add(12, 12): ')
    print(result)  # 24 
```

Enter fullscreen mode Exit fullscreen mode

*   使用`python3.6 run-wasm-from-python.py`运行此脚本

如果你做的一切都正确…

```
Modules exported from Rust: 
["simple_add"]
call simple_add(12, 12): 
24 
```

Enter fullscreen mode Exit fullscreen mode

这展示了使用 Wasm 使 **Python 与编译后的 Rust 互操作在技术上是如何可行的。由于 Rust 强大的设计和工具链每一步令人难以置信的工作，这非常简单。正如你所看到的那样，一切都在高水平的生产力下顺利进行。我将尝试优化 Rust 的繁重计算负载，同时保持 Python 的动态方法处于较高水平的优缺点留给您考虑。这是最近才有可能的，因为所有相关的接口和工具都已经稳定下来，这要归功于全世界开源开发者所做的大量工作。**

## 参考文献

*   Github 上的完整存储库
*   一定要看看这个 Reddit
*   [Wasmer 在这里](http://wasmer.io)

## 在同一系列中

*   新一集- > [一些-例子-1](https://dev.to/tuned/rust-scales-python-some-examples-1-2dj5)
*   新一集- > [烧瓶-实验](https://dev.to/tuned/rust-scales-python-flask-experiment-4l4l)*