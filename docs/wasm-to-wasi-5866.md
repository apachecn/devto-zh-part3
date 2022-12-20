# WASM 到 WASI

> 原文：<https://dev.to/jeikabu/wasm-to-wasi-5866>

你必须努力避免听到网络大会/WASM。几乎没有一天不出现另一篇文章。

几年前，我们使用 [emscripten](https://emscripten.org/) 将一个 C/C++游戏引擎转换成 JavaScript/WebGL，以便在网络浏览器中运行。有趣的是，最终的混乱是巨大的，缓慢的，调试起来相当痛苦。[团结把这个拨到 11](https://blogs.unity3d.com/2015/05/06/an-introduction-to-ilcpp-internals) : `C# -> (via Mono) IL -> (via IL2CPP) C++ -> (via emscripten) JavaScript`。他们值得为软件工程体操的令人印象深刻的一点小费，但是我很高兴我从来没有使用它。

我一直对 WASM 背后的技术很好奇，但不是一个网络前端的家伙只是偶然/被动地感兴趣。WASI 通过在浏览器之外拥抱 WASM 改变了这一点；另一个[“编写一次，随处运行”](https://en.wikipedia.org/wiki/Write_once,_run_anywhere)灵丹妙药的竞争者。

*铁锈连接*。也就是说，为什么会生锈？Rust 团队让 [WASM 成为 2018](https://blog.rust-lang.org/2018/03/12/roadmap.html#four-target-domains) 的焦点，因为[的原因](https://mgattozzi.com/rust-wasm/)。争论的关键是:

1.  没有运行时的语言是 WASM 的最佳选择(例如 C/C++/Rust)
2.  Rust 比 C/C++更好/更安全/更容易(特别是对于来自 JavaScript 的人来说)

也许是主观的，但是戴上正确的有色眼镜就可以接受(除非你是一个顽固的 C/C++爱好者)。拉斯特和 WASM 能解决你所有的问题吗？可能不会，但不管怎样，我们可以对技术感兴趣。

与其写一篇冗长的关于 WASM 基础知识的论文(有很多)，我想我会收集各种资源和笔记/浏览器标签。

WASM 简介:

*   [WASM:通用应用运行时](https://cybernetist.com/2019/04/25/wasm-universal-application-runtime/)
*   [https://webassembly.org/](https://webassembly.org/)

浏览器外的 WASI- WASM:

*   [标准化 WASI:在 web 之外运行 WebAssembly 的系统接口](https://hacks.mozilla.org/2019/03/standardizing-wasi-a-webassembly-system-interface/)
*   https://wasi . dev/

使用案例:

*   无服务器:
    *   [带 AWS Lambda 和 WebAssembly 的无服务器 Rust】](https://blog.scottlogic.com/2018/10/18/serverless-rust.html)
    *   [cloud flare Workers 的无服务器 Rust](https://blog.cloudflare.com/cloudflare-workers-as-a-serverless-rust-platform/)以及现在的[牧马人 CLI:cloud flare Workers 上部署 Rust with WASM](https://blog.cloudflare.com/introducing-wrangler-cli/)
    *   [网络组装的承诺](https://dev.to/ryan_levick/the-promise-of-webassembly-2obi)
*   嵌入:
    *   [在 Rust 应用程序中嵌入 web assembly](https://medium.com/wasmer/executing-webassembly-in-your-rust-application-d5cd32e8ce46)
*   [其他](https://webassembly.org/docs/use-cases/)

深入研究:

*   [铁锈 WASM 工作组](https://rustwasm.github.io/)和[文件](https://rustwasm.github.io/docs.html)。
*   运行时间: [wasmtime](https://github.com/CraneStation/wasmtime/) 、 [Lucet](https://github.com/fastly/lucet) 、 [wasmer](https://github.com/wasmerio/wasmer) (均内置 [cranelift](https://github.com/CraneStation/cranelift) )。Linux 用户模式。
    *   [宣布 Lucet: Fastly 的原生 WebAssembly 编译器和运行时](https://www.fastly.com/blog/announcing-lucet-fastly-native-webassembly-compiler-runtime)
*   js-sys : Rust 绑定到 JavaScript ECMAScript 标准。
*   [web-sys](https://docs.rs/crate/web-sys/) : Rust 绑定到浏览器 API。由 [WebIDL](https://heycam.github.io/webidl/) 自动生成。
*   性能:
    *   [WebAssembly 很快:WebAssembly 与 ES6 的真实世界基准测试](https://medium.com/@torch2424/webassembly-is-fast-a-real-world-benchmark-of-webassembly-vs-es6-d85a23f8e193)
*   内部构件:
    *   [多线程 Rust 和 Wasm](https://rustwasm.github.io/2018/10/24/multithreading-rust-and-wasm.html)
    *   [web assembly 中的内存(以及为什么比你想象的更安全)](https://hacks.mozilla.org/2017/07/memory-in-webassembly-and-why-its-safer-than-you-think/)。看看[的 Mozilla 博客](https://hacks.mozilla.org/)就知道了，那里还有其他有趣的东西，没必要一一链接。

杂项:

*   [铁锈- >网页浏览器中的 WASM](https://rust.godbolt.org/z/PZqG56)