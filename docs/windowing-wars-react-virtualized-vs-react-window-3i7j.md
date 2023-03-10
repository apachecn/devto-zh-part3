# 窗口战争:反应虚拟化与反应窗口

> 原文：<https://dev.to/bnevilleoneill/windowing-wars-react-virtualized-vs-react-window-3i7j>

[![](img/0655b781fde303debb8a09abea957c40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QyqIvOia--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u2pco96e3ce581ttn5qy.jpg)

> `react-window`是对`react-virtualized`的完全改写。我没有尝试解决同样多的问题或支持同样多的用例。相反，我专注于使包装更小更快。我还花了很多心思让 API(和文档)尽可能对初学者友好。

以上直接引自 Brian Vaughn 的 [`react-window` GitHub](https://github.com/bvaughn/react-window#how-is-react-window-different-from-react-virtualized) ，又名`bvaughn`—`react-window`和`react-virtualized`的作者(也是 React 核心团队的成员)。

**TL；博士** : `react-window`更新、更快、更轻，但它不能做`react-virtualized`能做的一切。如果可以的话，使用`react-window`，但是`react-virtualized`有很多可能对你非常有用的功能。

在本文中，我们将讨论:

1.  这些图书馆是做什么的？
2.  `react-window`是做什么的？
3.  `react-virtualized`做了什么`react-window`没有做的事？
4.  哪个最适合你？![🚀](img/3391331076099586282a97d71115908e.png)

### [![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)T4】](https://logrocket.com/signup/)

### 问题 1:需要开窗吗？

react-window 和`react-virtualized`都是窗口库。

**开窗**(又名虚拟化)是一种通过只将列表的可见部分写入 [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) 来提高长列表性能的技术。

如果没有窗口，React 必须在一个列表项可见之前将整个列表写入 DOM。

因此，如果我有大约 10，000 个列表项，我必须等待 React 向 DOM 写入至少 10，000 个`<div />`才能看到列表中的第一项。

哎哟。
[https://codesandbox.io/embed/9zqowllmo](https://codesandbox.io/embed/9zqowllmo)
提醒一下，React 内部使用了一个“[虚拟 DOM](https://reactjs.org/docs/faq-internals.html#what-is-the-virtual-dom) ”来保存你的 UI 状态，因为“真实”的 DOM 速度慢且昂贵。通过窗口，你可以通过尽可能避免“真正的”DOM 来加速你的初始渲染。

### 问题二:真的需要开窗吗？

尽管窗口可以提高性能，但它并不是灵丹妙药。

窗口可以提高性能，因为它延迟了将整个列表写入 DOM 的时间，但事实是，如果您希望用户看到这些项目，它们最终必须被写入 DOM。如果你没有预先支付渲染时间，那么你将不得不在以后支付。

有时窗口实际上会降低性能，因为用户必须等待每个单独的列表项在滚动时加载，而不是等待整个列表在装载时的一次急切加载。
[https://codesandbox.io/embed/ryy8pk8wqp](https://codesandbox.io/embed/ryy8pk8wqp)
在上面的演示中，注意窗口版本的列表显示得更快，但是当你滚动它时，非窗口版本感觉更快。

窗口版本看起来更快，因为它延迟了整个列表的呈现，但当快速滚动时，它感觉更慢/看起来很笨拙，因为它将列表项加载和卸载到 DOM。

是否开窗很大程度上取决于你的情况和对你来说什么是重要的:

|  | **无窗口** | **开窗** |
| **初始加载时间** | ![⚠️](img/3381411a2ef9168b5a99abd3ecdd0ca1.png)取决于列表大小 | ![✅](img/52c40a6a527544754ebb9f9796a1e914.png)【近】瞬间 |
| **列表项加载时间** | ![✅](img/52c40a6a527544754ebb9f9796a1e914.png)【近】瞬间 | ![⚠️](img/3381411a2ef9168b5a99abd3ecdd0ca1.png)取决于物品的复杂程度 |
| **DOM 操作发生** | ![⚠️](img/3381411a2ef9168b5a99abd3ecdd0ca1.png)初始渲染上 | ![⚠️](img/3381411a2ef9168b5a99abd3ecdd0ca1.png)卷轴上的 |

一般来说，如果没有必要，我不会推荐使用窗口。我犯过在不必要的时候开窗的错误，最终结果是一个更慢的列表，需要更长的时间来制作，而且明显更复杂。[![😓](img/33ac3bf163b0dd50663c61aa6c5d1aa8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YzRmAcel--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f613.png)

`react-window`和`react-virtualized`都是很棒的库，它们使窗口变得尽可能简单，但是它们也给你的 UI 带来了一些限制。

在你开窗之前，试着正常地列出你的清单，看看你的环境是否能承受。如果你有性能问题，那么继续。

### 问题 3:`react-window`对你来说够好吗？

正如`react-window`和`react-virtualized`的作者所说的[:](https://github.com/bvaughn/react-window#how-is-react-window-different-from-react-virtualized)

> `react-window`解决不了多少问题，也不支持多少用例。

这可能会让你认为`react-window`不能解决你的问题，但事实未必如此。`react-window`是一款更轻的内核，理念更简单。

`react-window`仍然可以支持许多与`react-virtualized`相同的用例，但是作为开发人员，您有责任将`react-window`作为构建模块，而不是将`react-virtualized`用于每个用例。

`react-window`只是一个虚拟化列表和网格的库。这就是为什么它要小 15 倍以上。[再次引用`bvaughn`](https://github.com/bvaughn/react-window#how-is-react-window-different-from-react-virtualized):

> 向[create-react-app]项目添加一个`react-virtualized`列表会将(gzipped)构建大小增加大约 33.5 KB。向 CRA 项目添加一个`react-window`列表会将(gzipped)构建大小增加< 2 KB。

开箱后，`react-window`只有四个组件:

*   [T2`FixedSizeList`](https://react-window.now.sh/#/api/FixedSizeList)
*   [T2`VariableSizeList`](https://react-window.now.sh/#/api/VariableSizeList)
*   [T2`FixedSizeGrid`](https://react-window.now.sh/#/api/FixedSizeGrid)
*   [T2`VariableSizeGrid`](https://react-window.now.sh/#/api/VariableSizeGrid)

这与`react-virtualized`的 13 个组件有很大不同。

虚拟化集合类型:

*   [T2`Collection`](https://bvaughn.github.io/react-virtualized/#/components/Collection)
*   [T2`Grid`](https://bvaughn.github.io/react-virtualized/#/components/Grid)
*   [T2`List`](https://bvaughn.github.io/react-virtualized/#/components/List)
*   [T2`Masonry`](https://bvaughn.github.io/react-virtualized/#/components/Masonry)
*   [T2`Table`](https://bvaughn.github.io/react-virtualized/#/components/Table)

以上集合类型的助手/装饰者:

*   [T2`ArrowKeyStepper`](https://bvaughn.github.io/react-virtualized/#/components/ArrowKeyStepper)
*   [T2`AutoSizer`](https://bvaughn.github.io/react-virtualized/#/components/AutoSizer)
*   [T2`CellMeasurer`](https://bvaughn.github.io/react-virtualized/#/components/CellMeasurer)
*   [T2`ColumnSizer`](https://bvaughn.github.io/react-virtualized/#/components/ColumnSizer)
*   [T2`InfiniteLoader`](https://bvaughn.github.io/react-virtualized/#/components/InfiniteLoader)
*   [T2`MultiGrid`](https://bvaughn.github.io/react-virtualized/#/components/MultiGrid)
*   [T2`ScrollSync`](https://bvaughn.github.io/react-virtualized/#/components/ScrollSync)
*   [T2`WindowScroller`](https://bvaughn.github.io/react-virtualized/#/components/WindowScroller)

根据经验，对于表格、列表和网格，您应该能够使用`react-window`来代替`react-virtualized`。然而，你不能将`react-window`用于任何其他东西，包括砖石布局和任何其他不适合网格的二维布局。

下面是一些使用`react-window`实现与`react-virtualized`相同结果的演示:

#### 动态调整容器大小(`AutoSizer`)

[https://codesandbox.io/embed/2zpo8nl56j](https://codesandbox.io/embed/2zpo8nl56j)

#### 动态调整项目大小(`CellMeasurer`)

[https://codesandbox.io/embed/yw4ok6l69z](https://codesandbox.io/embed/yw4ok6l69z)

**注意:**上面演示中的方法有一些注意事项([因为在`react-virtualized`](https://github.com/bvaughn/react-virtualized/blob/master/docs/CellMeasurer.md#limitations-and-performance-considerations) 中使用实际的`CellMeasurer`有一些注意事项)。

这个单元格测量器必须两次呈现该项的内容:一次是调整它的大小，另一次是在列表中。这种方法还要求节点与 react-dom 同步呈现，因此复杂的列表项在滚动时可能会显得较慢。

#### 无限加载(`InfiniteLoader`)

直接取自 [`react-window-infinite-loader`](https://github.com/bvaughn/react-window-infinite-loader) 包:
[https://codesandbox.io/embed/5wqo7z2np4](https://codesandbox.io/embed/5wqo7z2np4)

#### 方向键导航(`ArrowStepper`)

[https://codesandbox.io/embed/x9739j5v8q](https://codesandbox.io/embed/x9739j5v8q)

#### 滚动同步多重网格(`MultiGrid` + `ScrollSync`)

[https://codesandbox.io/embed/h2g26](https://codesandbox.io/embed/h2g26)

### 问题 4:无论如何你都应该使用`react-virtualized`吗？

再次引用 [`react-window` GitHub](https://github.com/bvaughn/react-window#how-is-react-window-different-from-react-virtualized) 中的话:

如果`react-window`提供了你的项目需要的功能，我会强烈推荐使用它而不是`react-virtualized`。然而，如果你需要只有`react-virtualized`提供的功能，你有两个选择:

1.  使用`react-virtualized`。(它仍然被大量成功的项目广泛使用！)
2.  创建一个组件，修饰其中一个`react-window`原语，并添加您需要的功能。您甚至可能希望将这个组件发布给 npm(作为它自己的独立包)！![🙂](img/c1766ba8b46615e3a821a99042b0d85a.png)

原来如此！

这仍然是一个伟大的项目，但它可能会超出你的需要。然而，我会推荐使用`react-virtualized`而不是`react-window`，如果:

1.  **您已经在您的项目/团队中使用了`react-virtualized`。**如果它没坏，就不要修复它——更重要的是，不要引入不必要的代码更改。
2.  **你需要虚拟化一个不是网格的二维集合。**这是`react-virtualized`处理的唯一一个`react-window`不支持的用例。
3.  **您想要一个预先构建的解决方案。** `react-virtualized`有所有用例的代码演示，而`react-window`只提供虚拟化列表原语，所以你可以构建它们。如果你想要有更多用例的文档和预制的例子，那么更重的`react-virtualized`适合你。

### 底线

**`react-window`** :更新更快的虚拟化列表原语。使用`react-window`作为您的虚拟化列表构建块，以满足您的特定用例，而不会带来大量不必要的代码。

**`react-virtualized`** :一款更重的一体机，可解决许多使用案例，并为其提供文档/示例，包括虚拟化非网格集合(例如砖石布局)。`react-virtualized`仍然是一个很棒的库，但是可能比你需要的要多。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](http://logrocket.com/signup/)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[Windowing wars:React-virtualized vs . React-window](https://blog.logrocket.com/windowing-wars-react-virtualized-vs-react-window/)首先出现在[日志博客](https://blog.logrocket.com)上。