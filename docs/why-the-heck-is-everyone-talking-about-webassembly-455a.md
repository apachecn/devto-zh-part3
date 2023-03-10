# 为什么每个人都在谈论 WebAssembly？

> 原文：<https://dev.to/captainsafia/why-the-heck-is-everyone-talking-about-webassembly-455a>

所以，我要对你们说实话。我做了一份很糟糕的工作，那就是了解最新的技术。我看到事情出现在 Twitter 上，我看到人们谈论它，我把它添加到我要学习的事情的列表中，然后我从来没有抽时间去做它。这是一个恶性循环。但是，这是新的一年，所以这是打破恶性循环的最佳时机。

今天早些时候，我看到了一条关于 WebAssembly 的推文。在过去的几年里，我听到了很多关于 WebAssembly 的讨论。我浏览过 WebAssembly 网站,甚至参加过一次关于 WebAssembly 的会议。也就是说，我从来没有真正花时间去钻研技术，真正理解所有的热情是什么。直到今天。那么，为什么每个人都在谈论 WebAssembly 呢？

### 什么？

回答“什么？”通常是尝试新事物的好起点。WebAssembly 主页提供了以下描述:

> WebAssembly(缩写为 Wasm)是基于堆栈的虚拟机的二进制指令格式。Wasm 被设计为 C/C++/Rust 等高级语言编译的可移植目标，支持客户端和服务器应用程序在 web 上的部署。

这个描述绝对不是为那些急于了解 WebAssembly 的人写的。我四处打探了一下，看是否能找到一篇更接近的解释者文章。我记得 [Lin Clark](https://twitter.com/linclark) 在 WebAssmebly 上制作了一些相当不错的内容，所以我决定看看[的介绍文章](https://hacks.mozilla.org/2017/02/a-cartoon-intro-to-webassembly/)是否会有所帮助。

我发现她的介绍非常有用。它证实了我的假设是正确的。WebAssembly 是一种获取任何编程语言中的代码并在 web 浏览器中运行它的方法。好的。这是一个更加平易近人的定义，我可以立即看到这样一项技术的价值。现在，它是如何工作的？

### 如何？

而“什么？”是一个有趣的问题来回答，“如何？”就更有趣了。任何了解我的人(或者读过我以前的博客文章的人)都知道，在探索事物如何工作的时候，我喜欢非常细致入微。我认为，为此，我应该从编写自己的程序开始，将其编译成 WebAssembly，然后在浏览器中运行它。

我发现 WebAssembly 的[简介文档](https://webassembly.org/getting-started/developers-guide/)比他们登陆页面上的一些文本更容易理解。我按照“下载工具链”一节中的说明进行了操作。他们指示我克隆 Emscripten SDK 并将其安装在我的本地机器上。

```
$ git clone https://github.com/juj/emsdk.git
$ cd emsdk
$ ./emsdk install latest
$ ./emsdk activate latest
$ source ./emsdk_env.sh 
```

Enter fullscreen mode Exit fullscreen mode

令我惊讶的是，当我跑这些台阶时，绝对没有什么可怕的事情发生。没错。为新的事物做准备时没有出错！真是一个惊喜。

下一步是写一个“你好，世界！”用 C 编程，编译成 WebAssembly。同样，在[开发者指南](https://webassembly.org/getting-started/developers-guide/)的“编译并运行一个简单程序”一节中的步骤。

发生了什么重要的事情？我用 C 语言编写了一个程序，并用 Emscripten 工具链编译了这个程序。这个 Emscripten 编译器生成了一个 Wasm 源文件、一个 JavaScript 文件和一个 HTML 文件。HTML 页面加载 JavaScript 文件，JavaScript 文件又将 Wasm 源文件加载到页面上。由于我使用的是最新版本的 Chrome 浏览器，Wasm 源文件中编译好的 Webassembly 由浏览器运行。

WebAssembly 程序被表示为一个普通的 JavaScript 对象，在代码库中恰当地称为`Module`。这个`Module`对象是在由 Emscripten 编译器生成的 javaScript 文件中生成的，它存储有关 WebAssembly 程序中使用的内存、与程序相关的 Wasm 二进制文件、管理程序运行时状态的代码等信息。本质上，这个模块包含了一个 WebAssembly 程序的数据和代码，可以被发送到你需要 WebAssembly 程序运行的任何地方(比如我的浏览器)。你可以在[这个设计文档](https://github.com/WebAssembly/design/blob/master/Modules.md)中找到更多关于 WebAssembly 模块的信息。

### 为什么？

现在，为什么是 WebAssembly？在研究这个问题时，最常见的原因之一是 WebAssembly 的速度。

虽然性能对于任何技术来说都是一个重要因素，但我不相信它是最重要的因素。我见过太多的开发人员在不必要的时候牺牲了其他重要的因素(比如开发人员的经验和可维护性)。

澄清一下:我并没有否认 WebAssembly 在某些情况下的速度优势。我只是在寻找更多的专业人士。

WebAssembly 还提供什么？好吧，另一个明显的好处是，它扩展了可以通过网络交付应用程序的程序类型(进而扩展到程序员)。我不能低估它的价值。这是一个有趣的前提。我很想知道这将如何改变人们在网络上的开发方式，以及对 WebAssembly 有什么影响

总的来说，上述原因对于 WebAssembly 作为一项技术来说是有说服力的，但是我很想知道我是否能够在我工作的环境中直接使用它。这些天我大部分时间都在做全栈的 web 开发，React 在前端，Node 在后端。对于像我这样的人来说，我发现了一个相当引人注目和有趣的用例。

从“如何？”一节中，您会想起 WebAssembly 在浏览器中运行编译后的代码。因为这段代码是编译的，所以经过了一定程度的模糊处理。

尽管如此，仍然有办法绕过这种混淆。当我在研究这个的时候，我看到了这篇论文，这篇论文讨论了编译后的 WebAssembly 模块被破译的方法。有志者事竟成。

### 有何不可？

没有技术是完美的。在探究了 WebAssembly 的一些理由*之后，我决定看看是否有任何理由来避免 WebAssembly。WebAssembly 有哪些缺点？*

这是一个很难回答的问题。我怀疑，因为 WebAssembly 是一项相当新的技术，它还没有达到发现技术中任何问题或缺陷所必需的临界质量。又或许 WebAssembly 完美无瑕，绝对没有瑕疵？

我会说，在主流浏览器默认采用 WebAssembly 技术之前，找到不使用 web assembly 的理由会容易一些。火狐、Chrome、Edge、Safari 都支持 WebAssembly 的事实。尽管如此，这里有一些我在研究 Web 组装时注意到的事情。

从上面的“如何”一节中，您可能还记得我写的 C 源代码最终被转换成 Wasm 二进制文件，并被加载到网页上，由我的浏览器执行。有一个全新的资产(Wasm 二进制文件)需要通过网络发送到我的浏览器。与通过互联网发送到用户浏览器的任何东西一样，需要注意尽量减小二进制文件的大小，以免降低页面加载速度。这本身并不是 WebAssembly 的缺点，只是需要注意的一点。网络上的非 Wasm 资产并不存在这个问题。根据我的研究，似乎有不少人已经写了关于如何最小化这些二进制文件大小的文章，所以*耸耸肩*，这实际上可能不是什么大事。

我在研究 WebAssembly 时遇到的另一个麻烦问题是 WebAssembly 和 JavaScript 之间的互通性。例如，如何从 WebAssembly 程序中调用用 JavaScript 编写的函数(无论用什么语言编写)？我找到了一篇[中型文章](https://medium.com/@KevinHoffman/javascript-interop-with-webassembly-2c69a3db19e9)讨论了一个开发者使用的工作，[找到了一个允许 WebAssembly 模块的提议](https://github.com/WebAssembly/proposals/issues/12)，我在“如何做？”中描述了这个提议以上部分将像 ES6 模块一样导入。

所以，是的，我想，对于 WebAssembly 来说，最大的“为什么不”是它是一项相当新的技术，并且不是所有的细节都已经弄清楚了。但据我所知，社区的拥护者似乎非常积极主动地回应问题，并为问题创造设计方案，所以就这样了。

### 结论

好吧！那是一次相当有趣的探索。我很高兴我对什么是 WebAssmebly 更有信心了。我不认为自己会很快使用它，但我认为这是因为我没有在一个可以从使用 WebAssmebly 中受益的环境中工作(构建 3D 游戏等)。

您目前正在使用 WebAssembly 吗？您是否有具体的计划在即将发布的功能/产品中使用它？我在这篇博文中对 WebAssembly 有什么误解吗？