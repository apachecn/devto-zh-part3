# Node.js v12 刚刚发布的出色特性

> 原文：<https://dev.to/bnb/the-awesome-features-that-just-landed-with-node-js-v12-178d>

本周，我们看到了 Node.js v12 的发布，这是将成为 LTS 的下一个 Node.js 发布系列。我想浏览发布的各种帖子和变更日志，并将信息浓缩成易于使用的 Node.js v12.x 中新增内容的摘要，与大家分享。💖

## 这个🔥变化

让我们深入了解一下 v12.0.0 中最重要、最显著的变化吧！

### 新增 ES 模块，卫 dis

随着 Node.js v12.0.0 的发布，我们看到 Node.js 中引入了 ES 模块的新实现。🎉

> **注意:** ES 模块功能仍在**实验中**，因此*不应*用于生产代码中，直到它们最终确定。

在发布时，这个新的实现已经取代了以前的实现，隐藏在`--experimental-modules`标志之后。这是为了帮助获得新的实现并进行测试，这样项目就可以得到反馈。如果一切顺利(🤞)，一旦 Node.js v12 在 10 月份进入 LTS，它就可以不带标签发货了！

首先，我想说这将是一个 TL；博士，如果你有兴趣深入了解 Node.js 中 ESM 的新热点，请查看模块团队在 Medium 上发表的博文。

#### 先前实现

以前实现的许多功能都保留了下来。这包括 ES2015 `import`语句，各种`export`，Node.js `export`对所有核心模块的支持，CommonJS 的 WIP 导入， **very** WIP loader API，以及如果`.mjs`文件扩展名存在的话，明确的 ESM 解析。

#### 新增实现功能

这些特性 100%是新的，模块团队一直在进行增强，并且在 Node.js v12.0.0 中的`--experimental-modules`标志后面可用。

*   在`.js`文件中导入和导出语法
    *   有很多反馈认为 Node.js 需要提供一种在`.js`文件中使用导入/导出的方法。
    *   为此实施了两种不同的解决方案(继续阅读！)
*   支持`package.json`中的`"type": "module"`
    *   如果检测到这种情况，Node.js 会将项目中的所有 `.js`文件*视为 es 模块。*
    *   如果仍然有 CommonJS 文件，可以用文件扩展名`.cjs`重命名它们，这将告诉 Node.js 显式地将它们解析为 CommonJS
    *   一个`--input-type`标志，用于类似`--eval`和 STDIN 的情况

#### 当前在制品特征

这些特性目前正由模块团队开发，要么已经实现，但可能会改变*或*正在开发，但尚未在 Node.js v12.0.0 中发布。

*   JSON 导入
    *   目前不工作，但正在积极工作。
*   导入并要求互操作
    *   ️️⚠️模块团队要求你在问题解决之前不要发布可以在 Node.js 中使用的 es 模块。我认为在这个问题解决之前发布的模块可能会崩溃。
*   模块加载器
    *   非常 WIP
    *   第一个实现的`--loader` API 已经发布，但它将被改进，因此，*改变*。
*   一种更简单的方法来`require`ES 模块中的代码。
    *   当前的实现有点笨拙。模块团队正在努力降低障碍。
*   包路径映射
    *   这将允许在某些情况下进行不太冗长的导入
*   自动入口点模块类型检测
    *   实际上，静态分析允许 Node.js 判断一个模块是 CommonJS 模块还是 ES 模块。

#### 快速 ESM 示例

如果你有兴趣看看 Node.js 中的 ESM 是什么样子，可以看看我昨天推送的两个回购:

*   [simple-esm](https://github.com/bnb/simple-esm)–node . js 中的 ESM 在当前 ESM 实施中的表现示例
*   [simple-esm-usage](https://github.com/bnb/simple-esm-usage)–这是一个示例，说明如果当前的实现保持不变(它将会发生变化，因此这更多是理论而不是实践)，如何在 Node.js 中使用来自 npm 的 ESM 模块

我计划保持这些回购协议(以及发布给 npm 的 simple-esm 版本)都是最新的，因为 esm 实施根据我自己的理解发生了变化，并且作为一种社区资源，在 Node.js 中有一个最小可行的 ESM 示例。

### V8 7.4

这个版本包括一个重要的 **V8 升级**，在发布时向前跳跃了几个版本到 V8 的最新版本。这次升级包括了许多非常棒的增强功能。我个人最感兴趣的是[零成本异步堆栈跟踪](https://v8.dev/blog/v8-release-72#async-stack-traces)，但是还有很多其他的增强功能，V8 团队的 Mathias Bynens 对此做了更好的概述:

> ![Mathias Bynens profile image](img/dedf260f16ec3783dd7e70f43cf5ca29.png)【马蒂亚斯·拜仁】[@马蒂亚斯](https://dev.to/mathias)中的新 JavaScript 功能{ListFormat，locale，relative format }
> object . fromentries
> 稳定数组# sort
> 【string # match all】
> 【symbol # description】
> 【well formed JSON . stringify】下午 14:45-

### TLS 1.3

接下来，我们有**官方 TLS 1.3 支持**。这是对以前的 TLS 版本的一个令人难以置信的改进，我非常兴奋，它现在在即将在 LTS 发行的发行版中得到支持！幸运的是，由于 OpenSSL 1.1.1 中的底层实现，这是一个向后兼容的变化。另外，在 PR 中提到[它应该被反向移植到其他 LTS 发布线上。](https://github.com/nodejs/node/pull/26209)

如果你对 TLS 1.3 的精彩部分感到好奇，我推荐这篇来自 IETF 的[博客文章](https://www.ietf.org/blog/tls13/)。

### 工作者线程

这是第一个 LTS 发布线，将包括目前正在实验的工作线程。这个版本不再需要运行带标志的工作线程，有望降低在 Node.js 中更广泛使用并行化工作工具的障碍。

如果您现在对尝试工作者线程感兴趣，可以使用一些资源来开始:

*   [在 Node.js 中使用 worker _ threads](https://medium.com/@Trott/using-worker-threads-in-node-js-80494136dbb6)
*   [node . js 工作线程中的简单双向消息传递](https://hackernoon.com/simple-bidirectional-messaging-in-node-js-worker-threads-7fe41de22e3c)
*   Node.js 多线程:什么是工作线程，为什么它们很重要？
*   [官方 Node.js Worker Threads Docs](https://nodejs.org/api/worker_threads.html)

### 内置堆快照

在这个版本中，我们还看到了从 npm 上的 [heapdump 模块](https://www.npmjs.com/package/heapdump)改编而来的内置堆快照。这通过`v8.getHeapSnapshot()`公开，并返回一个可读的流。

## 其他值得注意的变化和改进

*   核心依赖关系:
    *   升级到 OpenSSL[1 . 1 . 1 b](https://www.openssl.org/news/cl111.txt)([nodejs/node # 26327](https://github.com/nodejs/node/pull/26327))
    *   升级到 ICU 63 ( [nodejs/node#25852](https://github.com/nodejs/node/pull/25852) )
    *   目前还有一个[开放 PR](https://github.com/nodejs/node/pull/27361) 进一步更新到 ICU 64.2
*   Node.js 已经开始使用 [llhttp](https://github.com/nodejs/llhttp) 作为它的默认解析器( [nodejs/node#24730](https://github.com/nodejs/node/issues/24730)
*   `package.json`中无效的`main`条目现在将抛出一个错误( [nodejs/node#26823](https://github.com/nodejs/node/pull/26823)
*   `node --debug`现已停产——请改用`node --inspect`([nodejs/node # 25828](https://github.com/nodejs/node/pull/25828))
*   TLS 1.0 和 1.1 现在默认禁用( [nodejs/node#23814](https://github.com/nodejs/node/pull/23814) )

## 鳍

希望这个新版本的概述对你有帮助！如果您对已经发布的新功能有任何疑问，何时您可以开始期望在 Node.js 中使用 ESM，或者关于 Node.js v12 的任何其他内容，我很高兴成为您的资源，希望您能找到您正在寻找的答案！