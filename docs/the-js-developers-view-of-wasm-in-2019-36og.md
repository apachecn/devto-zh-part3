# JS 开发者对 2019 年 WASM 的看法

> 原文：<https://dev.to/neutrino2211/the-js-developers-view-of-wasm-in-2019-36og>

Webassembly 是一项伟大的创新，但大多数模块都是用 C++、Rust 或 GO 制作的，由于 javascript 开发人员不得不为一个项目维护两个代码库是很困难的，幸运的是这正是 [AssemblyScript](https://github.com/AssemblyScript/assemblyscript) 的用武之地，AssemblyScript 将 typescript 编译成 wasm，这向所有 javascript 开发人员开放了 webassembly。

在 assemblyscript 出现之前，我们必须学习 C++才能使用 webassembly，但现在我们有了进入 webassembly 生态系统的方法。

为了更好地理解这种环境，我们需要问自己几个问题。

### 1:我们能从 webassembly 中获得什么？

我们从 webassembly 获得的一个巨大优势是性能提升，webassembly 比 javascript 快 32 倍，这使 javascript 应用程序能够以接近本机的性能运行。

用 javascript 制作的原生应用经常被批评为速度慢、响应慢，尤其是在低功耗设备中，尽管开发者一直在提高性能，所以使用 webassembly 将使这些应用运行得更快。

### 2:我们可以用 webassembly 做什么？

使用 webassembly 可以做很多事情，其中包括

*   数据科学。

*   平面设计。

*   游戏开发。

*   虚拟现实。

*   平台仿真。*有一个用 webassembly 编写的 Windows 2000 虚拟机*

这个列表可以持续一段时间，但这会让这篇文章太长，所以这里有一个[链接](https://webassembly.org/docs/use-cases/)到它的用例。

### 3:对 webassembly 的支持如何？

Webassembly 背后有一个非常活跃的社区，有许多开发人员在从事像。

*   ASM-DOM :一个用于构建 c++ spa 的 webassembly 虚拟 DOM

*   Blazor :一个使用 C#/Razor 的实验性 web UI 框架

*   一个制作网络应用的 rust 框架

*   go-vdom-wasm :一个为戈朗和 wasm 设计的实验性虚拟 dom

*   Asmble : Webassembly 到 JVM 字节码编译器

*   [Wasmer](https://github.com/wasmerio/wasmer) :一个独立的 JIT WebAssembly 运行时*一个类似的项目是[wasmjit](https://github.com/rianhunter/wasmjit)T5】*

*   pywasm :用 python 编写的 webassembly 解释器

*   [wac](https://github.com/kanaka/wac) :用 c 语言写的 webassembly 解释器*支持 SDL！*

这意味着我们的模块也可以适应一个更大的画面，并且实际上可以在任何地方运行

### 4:我们如何入门？

开始的唯一方法是使用 assemblyscript，它非常容易设置。

##### 第一步:安装

我们通过运行
安装 assemblyscript

```
$ npm i AssemblyScript/assemblyscript -g 
```

Enter fullscreen mode Exit fullscreen mode

这将安装一些命令。

*   asc:这是 assemblyscript 编译器，它将类型脚本文件编译成 wasm 二进制文件。

```
$ asc -h
SYNTAX
  asc [entryFile ...] [options]

EXAMPLES
  asc hello.ts
  asc hello.ts -b hello.wasm -t hello.wat
  asc hello1.ts hello2.ts -b -O > hello.wasm 
```

Enter fullscreen mode Exit fullscreen mode

*   asinit:设置新的 assemblyscript 项目或更新现有项目

```
$ asinit -h
Sets up a new AssemblyScript project or updates an existing one.
For example, to create a new project in the current directory:

  asinit . 
```

Enter fullscreen mode Exit fullscreen mode

##### 第二步:初始化项目

为了初始化一个项目，我们运行

```
$ asinit my-project 
```

Enter fullscreen mode Exit fullscreen mode

这为我们创建了一个新项目，但是我们必须手动安装依赖项

```
$ cd my-project && npm install 
```

Enter fullscreen mode Exit fullscreen mode

##### 第三步:构建

`asinit`命令负责向我们的项目添加构建脚本，所以我们所要做的就是运行

```
$ npm run asbuild 
```

Enter fullscreen mode Exit fullscreen mode

## 例子

### 1:指针

Web assembly 使用基于堆栈的虚拟机，这意味着它使用类似数组的结构，称为*堆栈*来存储变量，这样的问题是我们最终会遇到指针，javascript 不能很好地处理指针。

场景:我们有一个从 assemblyscript 编译的 wasm 库，它返回一个非常重要的字符串。

```
export function return_string(): string{
    return "Important stuff";
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在 javascript 文件中使用它，就像这样

```
const wasmModule = await WebAssembly.instantiateStreaming(fetch('simple.wasm'), {})
const importantResult = wasmModule.instance.exports.return_string();

console.log(importantResult); //=> 8 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码运行起来没有问题，但是控制台中记录的不是字符串，而是一个指向字符串的指针，如果我们查看内存，我们会发现它看起来像这样。

```
"\0f\00\00\00I\00m\00p\00o\00r\00t\00a\00n\00t\00 \00s\00t\00u\00f\00f"; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用 assemblyscript 本身加载 wasm 二进制文件，而不是直接通过`WebAssembly.instantiateStreaming`加载，这将帮助我们处理那些讨厌的指针。

```
import { instantiateStreaming } from "assemblyscript/lib/loader";
const wasmModule = await instantiateStreaming(fetch('simple.wasm'), {})
const importantResult = wasmModule.getString(wasmModule.return_string());

console.log(importantResult); //=> Important stuff 
```

Enter fullscreen mode Exit fullscreen mode

### 例 2: DOM

作为一名 javascript 开发人员，使用 DOM 是必不可少的，但是 webassembly 没有直接的方法来做到这一点，所以不同的语言有不同的方法来解决这个问题。

*   C++ (emscripten)

```
auto root = emscripten::val::global("document").call<emscripten::val>(
      "getElementById",
      std::string("root")
    ); 
```

Enter fullscreen mode Exit fullscreen mode

*   GO (syscall/js)

```
var root = js.Global().Get("document").Call("getElementById", "root") 
```

Enter fullscreen mode Exit fullscreen mode

*   Rust (wasm_bindgen)

```
let document = web_sys::window().unwrap().document().unwrap();
let root = document.get_element_by_id("root").unwrap(); 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以做到这一点，但需要更多的努力。我们需要做的第一件事是为模块提供一个可以用来操作 DOM 的方法。

```
import { instantiateStreaming } from "assemblyscript/lib/loader";
const wasmModule = await instantiateStreaming(fetch('simple.wasm'), {
    index: {
        changeDiv: (id, value)=>{
            var div = document.getElementById(wasmModule.getString(id));
            div.innerHTML = wasmModule.getString(value);
        }
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

然后在我们的模块
中使用它

```
declare function changeDiv(id: string, value: string): void;
export function set_hello_message(): void {
    changeDiv("helloDiv","Hello from WASM");
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

WASM 是快速、安全和可靠的，因此能够在不离开我们的舒适区的情况下使用它是非常令人鼓舞的。

# 感谢阅读！！！！

老实说，这篇文章的最初版本对这种情况的看法非常糟糕，我很高兴 [@jtenner](https://dev.to/jtenner) 指出了这一点。我希望这显示了一个更好的观点