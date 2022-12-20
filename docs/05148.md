# 伸出援手，WASM。嵌入 Lua。

> 原文：<https://dev.to/adam_cyclones/reaching-out-wasm-help-embedding-lua-3fbj>

# 意图

[https://github.com/acronamy/wasm-lua-node-tidal](https://github.com/acronamy/wasm-lua-node-tidal)

我做这个实验已经有一段时间了，这让人抓狂，我相当确定我已经完成了 99%,但是我遇到了障碍。

我希望从 node.js 调用 Lua 作为 WASM 模块，有一些现有的项目，但它们专注于浏览器中的 Lua，我看到了 Lua 的不同潜力——尽管今年列出了最差的编程语言之一，(根据使用情况，就像说我的螺丝刀不擅长拧螺丝，因为我不太用它)—我今年爱上了 Lua。

就像流行的 Lovit 框架的创建者一样，我希望有一个更快的 V8 替代方案，但也有一个漂亮而熟悉的应用程序，但这个项目不同，它没有取代 node，而是按照预期使用 Lua，一种与 node.js 一起透明运行的嵌入式脚本语言，而不是取代它，只是补充它。

## 你想从我这里得到什么？

所以，让我们言归正传，这是我第一次也可能是不成功的对社区的呼吁，你不需要知道太多关于 Lua 的知识，但是如果你对 emscripten 和导师我，或者可以让我的应用程序编译和运行。或者放入一个 PR，我会非常感激，我现在可以调用我的 _run_lua 函数，但是我得到一个内存越界错误。不知道...

## 我要带着这个去哪里？

我想让梦幻般的 Love2D 游戏引擎在 html 画布上以接近本地的速度工作。我也想得到开放的 resty 集成，我有很多想法。

感谢阅读！