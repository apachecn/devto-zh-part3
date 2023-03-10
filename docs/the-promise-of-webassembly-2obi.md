# WebAssembly 的承诺

> 原文：<https://dev.to/ryan_levick/the-promise-of-webassembly-2obi>

[WebAssembly](https://webassembly.org) (wasm)是为 web 制作的汇编二进制格式。wasm 最初是为了在 web 浏览器中运行本机代码，最近已经扩展到能够在浏览器之外运行。

在本帖中，我们将仔细研究什么是 WebAssembly 以及围绕它的相关技术，并且我们将探索这对于一个特定的用例——功能即服务(FaaS)又称为无服务器——可能意味着什么。

## 定义术语

WebAssembly 是虚拟机的程序集。我在这里指的是‘虚拟机’的原始意义——一个概念上的机器——一个只在理论上存在的机器。您可以为该概念性机器编写代码，然后编译器可以将其编译成在真实主机上运行的程序集。

[WebAssembly 系统接口](https://wasi.dev) (WASI)是一个规范，允许 WebAssembly 二进制程序通过一个定义良好的跨平台接口与主机的操作系统对话。这允许 WebAssembly 二进制文件做一些有用的事情，比如写文件和打开 TCP 套接字。

Fastly 的 [Lucet](https://github.com/fastly/lucet) ，Mozilla 的 [Wasmtime](https://github.com/CraneStation/wasmtime) ，以及 [wasmer](https://github.com/wasmerio/wasmer) 都是通过一个名为 [Cranelift](https://github.com/CraneStation/cranelift) 的库进行提前(AOT)编译，将 WebAssembly 代码变成原生汇编的编译器。Lucet 和 Wasmtime 也是实现 WASI 规范的运行时，允许这些二进制文件以统一的、跨平台的方式与主机对话。

## 优于现有技术

那么这与现有技术有何不同呢？

### 三伯星拥有细粒度权限

WebAssembly 二进制文件是完全沙盒化的，这意味着它们只能通过 WASI 访问主机的资源。

还有其他系统也是在应用程序级别沙箱化的——例如，node 到 v8。这里的区别在于你的控制水平。一方面，v8 是完全沙箱化的，不提供与主机系统对话的方式。Node 是打开 v8 的一种方式，允许它进入主机系统。不幸的是，node 以一种不受控制和管理的方式向应用程序完全开放了主机系统。

WASI 和各种运行时允许更细粒度的许可。例如，用户可以只授予对某个目录的权限，而不是允许访问主机系统的整个文件系统。网络和其他系统资源也有类似的控制。

这提供了一种运行程序的方式，在这种方式下，您可以精确地控制允许程序访问哪些资源。

### 轻巧

除了这种细粒度的沙盒，WebAssembly/WASI 运行时在开销方面是非常轻量级的。与 Docker 提供的细粒度沙盒不同，WebAssembly 运行在应用程序级别，而不是操作系统用户级别。这意味着 WebAssembly 程序可以更快地启动，并且在主机系统上和通过网络传输时消耗更少的资源。

## 这对 FaaS 意味着什么

一个完全沙箱化的轻量级环境可以允许在同一台机器上更紧密地打包无服务器应用程序——允许无服务器提供者降低成本。此外，启动时间应该更短(理论上大约 1-2 毫秒)。

此外，WebAssembly 是完全语言不可知的，因此在未来，您应该能够在 WebAssembly 环境中运行任何语言，这可能是任何语言。

这也使得本地测试更加容易，因为用户需要安装的只是一个 WebAssembly 运行时(一个单独的二进制文件)。

## 接下来呢？

希望这能让你对 WebAssembly 有一个大致的了解，至少在帮助推动 FaaS 前进的背景下。

如果你喜欢这篇文章，请在 Twitter 上联系我，告诉我你是否想让我写更多关于 WebAssembly 的内容。