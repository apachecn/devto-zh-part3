# 关于 React DevTools 你不知道的 5 件事

> 原文：<https://dev.to/bnevilleoneill/5-things-you-didn-t-know-about-react-devtools-8jd>

[![](img/87aaa8e767bd479520a248924cd5b045.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--muls2kFe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/550/1%2A2y3LJ80oZRSFzUR0NLULqQ.jpeg)

如果您对 React 开发感兴趣，那么您可能已经尝试过官方的 React DevTools。这个浏览器扩展可以让你调试你的组件，可用于 [Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en) 、 [Firefox](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/) ，甚至可以作为一个[独立应用](https://github.com/facebook/react-devtools/blob/master/packages/react-devtools/README.md)用于 Safari 和 React 原生调试。

React DevTools 正在[积极开发中](https://twitter.com/brian_d_vaughn/status/1113968922993221633)，新的特性一直在增加。本文将向您展示五个您可能还没有尝试过，或者您根本不知道存在的特性！最后，我们将看看 React DevTools 的未来会带来什么。

### 用 profiler 调试性能

2018 年 9 月，React 团队[推出了一个令人难以置信的强大功能](https://reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html)，用于调试性能相关问题:分析器。当与 React 16.5 或更高版本一起使用时，React DevTools 为您提供了一个分析功能，它向您显示了应用程序如何重新渲染的摘要。

就像您选择的开发工具中的常规 JavaScript 分析器一样，您必须“记录”一组交互。完成后，您将看到应用程序经历的每次重新渲染的可视化效果，其中包含精确的计时信息和彩色编码的火焰图。现在，它甚至[捕捉每一次 DOM 更新的截图](https://twitter.com/brian_d_vaughn/status/1113200027835310080)！

[![](img/8cee4b499c10d60c89d9cc8ff762986f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hyLxjXpK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AwjikSFSJzAowZaMkzSCSBQ.png) 

<figcaption>下面是剖析器的火焰图视图</figcaption>

已经有一些很棒的文章可以教你关于 React Profiler 的所有知识。最初的博客文章很好地介绍了大多数功能，而[埃克乌诺·奥比纳的文章](https://scotch.io/tutorials/use-the-react-profiler-for-performance)提供了真正的深度探讨。我还可以建议看一下[的 Brian Vaughn 录制的直播流](https://www.youtube.com/watch?v=nySib7ipZdk)，在那里他使用 React Profiler 来定位和消除一些讨厌的性能错误。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### React DevTools 是 themable！

你是那些花时间让你的开发环境看起来恰到好处的人之一吗？好吧，我有消息告诉你！React DevTools 是 themable！

[![](img/dad557927a8e14481d959b3ff18abe1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NjaUfAuV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/530/0%2ATA-gu64zH5OIaWVU) 

<figcaption>主题选择器隐藏在 cog 图标后面。</figcaption>

默认情况下，它遵循你的 Chrome 主题，所以如果适用的话，它会尊重你的黑暗模式偏好。但是为什么就此打住呢？React DevTools 预装了 **18 个不同的主题**，包括一些你最喜欢的编辑器最流行的主题。如果这还不够，甚至还有一个 UI 来实现你自己的主题——就在扩展中！

要开始玩主题，请单击主面板右侧的齿轮图标。你会看到一个设置面板，让你选择预装的主题。

通过点击下拉菜单右边的铅笔图标，您也可以访问自定义主题设计器。

[![](img/4e5cde125dc242f0f12958617a5abc26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zplrO_El--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AWnddoM0m-FQsRU59) 

<figcaption>点击主题下拉菜单旁边的铅笔图标，你会看到这个功能丰富的主题设计器！</figcaption>

### [控制台中的](#interact-in-the-console)交互

虽然您可以直接在 React DevTools 中找到并修复大多数问题，但有一些调试只需使用一个好的 ol' console.log 就可以更好地工作。DevTools 的一个我最喜欢的特性是，它实际上为您提供了通过控制台本身与组件实例进行交互的选项！

在 React DevTools 中选择一个组件，并弹出打开控制台(按 escape 键可以让您同时看到这两个组件)。输入$r，您就可以访问该组件的实例了！

[![](img/f98b6ce2b507050364b25df9c84e3b61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1-W5ppsi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ae7dmaExkRf3Q_X5J)

这给了您很大的调试能力。您可以触发回调方法，检查状态，甚至让您直接从控制台增加功能。相当整洁！

如果您对某个特定的函数感兴趣(比如点击处理程序或回调函数)，React DevTools 还为您提供了一个非常方便的小特性。如果您在 props 或 state overview 中右键单击一个函数，您可以选择将它作为一个全局变量！这样，你可以随时调用它，使用不同的参数和上下文。

### 观源如临

现代浏览器中的调试工具比大多数人意识到的要强大得多。您可以设置断点，随着应用程序的变化检查状态和属性，并逐行执行代码。然而，用 Babel 编译的代码往往很难读懂，也很难理解。如果有一种方法可以让我们以我们编写的方式来查看源代码，这不是很好吗？

Babel 提供了一个插件，为所有 JSX 元素添加了 __source prop，让 React DevTools 直接链接到你正在检查的组件的源代码。它会使您的应用程序变慢一点，但是在开发模式下效果很好。

要使用此功能，请安装[babel-plugin-transform-react-jsx-source](https://github.com/babel/babel/tree/master/packages/babel-plugin-transform-react-jsx-source)，将其应用到您的。babelrc 文件，并重新启动您的开发服务器。现在，当您右键单击组件树中的一个组件时，您会看到一个新选项——“显示源代码”。

[![](img/c85427b65b7f12e5b08c31eb1ede595e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lu6_-n3w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/650/0%2A4w4gQJ7Lk2Z0Wecj) 

<figcaption>显示输入表单源代码在应用巴别塔插件时显示为一个新选项</figcaption>

这样，您就可以跳转到浏览器的源代码视图，在那里您可以检查、设置断点和更改整个代码。

如果你使用流行的引导工具 [Create React App](https://facebook.github.io/create-react-app/) ，你会发现它开箱即用。没有配置，没有插件，什么都没有。直接跳到源代码！

### 使用秘密的 React APIs

你知道吗，React 的一些顶级导出是专门为与 React DevTools 交互而设计的？你可能不经常需要它们，但是当你需要的时候知道它们是非常好的。

#### useDebugValue

当你创建与其他开发者共享的[定制钩子](https://reactjs.org/docs/hooks-custom.html)时，使用 useDebugValue 钩子可能是个好主意。这个钩子向 React DevTools 中显示的钩子添加了一个信息标签。

#### 交互追踪

在某些情况下，理解为什么特定的提交或重新渲染花费更长时间可能有点复杂。这就是为什么 React DevTools 引入了对所谓的交互跟踪的支持。这是一个编程式 API，允许你在应用程序中的不同事件上放置标签，并跟踪它们的性能。

首先，安装调度器包，并导入 unstable_trace 方法。这个方法接受一个标签(例如，单击了登录按钮)、一个时间戳和一个回调。回调中发生的事情将会比以前更详细地被跟踪。

要了解更多关于这个即将推出的功能，请参考[布莱恩·沃恩关于这个主题的要点](https://gist.github.com/bvaughn/8de925562903afd2e7a12554adcdda16)。请注意**这些功能仍处于非常早期的阶段**，仍在积极开发中。它们玩起来很有趣，但是 API 可能会在任何时候崩溃。**慎用！**

### 型材在生产！

如果您遇到了只在应用程序的生产版本中出现的性能退化，您可能想尝试对您的应用程序进行分析。不幸的是，React 默认不包含对 React 生产版本的概要分析支持。

幸运的是，您仍然可以选择在您的生产应用程序中提供概要分析。您可能不想将它发送给您的客户，但是在本地或在 QA 环境中测试它会对您有很大帮助。要了解这是如何做到的，请看布莱恩·沃恩的这篇文章。

### React DevTools 的未来

如前所述，React DevTools 目前正在大量开发中。React 核心团队成员 Dan Abramov 花了几周时间来帮助发布特性，React DevTools 的一个新的主要重写已经开始成形。以下是我们最期待的一些功能的预览:

#### 遗留 React 支持

React DevTools 当前版本仅支持 React 16 及以上版本。然而，在新的主要版本中，支持 React 0.13、14 和 15。这对任何维护或修复遗留 React 代码库的人来说都是一个好消息！
[![](img/e3d547b5ce0a9dd59a92ac2745c2160a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZucmgweV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qtdzunz62r2rkq79oj8v.png)

#### 折叠或展开树形视图

当您打开 React DevTools 时，您现在可以决定默认情况下是希望组件的树视图折叠还是展开。您可以从新的和改进的设置面板中更改您的首选项，该面板位于 cog 图标后面。

> ![unknown tweet media content](img/79529da9d679fa6d79391ee51c3bb851.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/D4TZZWFU0AUyLG_.mp4" type="video/mp4"></video>![Brian Vaughn profile image](img/ac4d742cbd5a6e12603fdb889fc757ee.png)布莱恩·沃恩@布莱恩 _ d _ 沃恩![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)⚛️🛠有些人更喜欢默认折叠的组件树。其他人更喜欢膨胀的。
> 
> (V)(，，，，)(V)两者何不兼得？20:59PM-2019 年 4 月 16 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1118257647109623809)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1118257647109623809)10[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1118257647109623809)185

#### 简化树形视图

React DevTools 的新版本将获得稍微简化的树形视图。不会显示任何内联属性(除了键，如果适用的话)，纯 DOM 节点(比如

) will be shown.

这是一个非常有争议的改变，一旦发布，可能会受到社区的一些反对。也就是说，有利的一面包括更好的性能，以及更易于管理的树视图 UI。
[![](img/e6583e9aab14bc1b7c6f2b48522472f0.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--iVxr_C1P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ly9ba38407dnha2zhe78.png)

#### 改进了工具提示

你知道 React DevTools 的大多数控件都有很好的解释工具提示吗？大多数用户也不知道。原生工具提示有很长的延迟，这导致许多用户认为根本没有任何工具提示。这就是为什么你会在即将到来的版本中发现新的和改进的工具提示，这是 React Training-boss[Ryan Florence](https://twitter.com/ryanflorence)和他的 [Reach UI 库](https://ui.reach.tech/)提供的！

> ![unknown tweet media content](img/bf5c5fedf99f2bb51dcbd96ece7fc5a7.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/D4n_5ABUEAAR6s8.mp4" type="video/mp4"></video>![Brian Vaughn profile image](img/ac4d742cbd5a6e12603fdb889fc757ee.png)布莱恩·沃恩@布莱恩 _ d _ 沃恩![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)[@ ryanflorence](https://twitter.com/ryanflorence)看看这些东西有多油滑😍2019 年 4 月 20 日 21:00 点[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1119707346182131712)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1119707346182131712)13[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1119707346182131712)198

#### 多了，多了

上面列出的特性只是 React DevTools 新版本中预计会出现的许多特性中的一部分。如果你感兴趣，你可以在布莱恩·沃恩的文章中了解更多关于这个新版本的信息。

### 旁白:关于布莱恩·沃恩

你可能已经注意到，当我们谈论 React DevTools 时，Brian Vaughn 的名字经常出现。Brian 为脸书工作，是 React 核心团队成员。他是 React DevTools 的主要开发人员，在为普通 React 开发人员创建有用的工具方面做了大量工作。如果你想成为一名 DevTools 专家，我推荐你看看他的几篇关于这个主题的**。布莱恩还做过* [*几个播客*](https://reactpodcast.com/37) [*访谈*](https://egghead.io/podcasts/brain-vaughn-react-core-team) *值得一查。**

 *### 一个极其重要的工具

React DevTools 使 React 应用程序的构建、发布和调试变得比以往更加容易。多亏了新的分析器 API，我们甚至可以立刻解决大多数性能问题！对于新手和专业人士来说，这是一个非常重要的学习如何使用和掌握的工具。

React DevTools 中你最喜欢的特性是什么？

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

关于 React DevTools 你不知道的 5 件事的帖子[首先出现在](https://blog.logrocket.com/5-things-you-didnt-know-about-react-devtools-2c6e0ef22529/)[日志博客](https://blog.logrocket.com)上。*