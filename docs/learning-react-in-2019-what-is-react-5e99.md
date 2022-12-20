# 2019 年学习 React:React 是什么？

> 原文：<https://dev.to/wuz/learning-react-in-2019-what-is-react-5e99>

在 Javascript 生态系统中工作一段时间，你可能会听到很多关于 React 的事情。关于图书馆是什么/做什么/应该是什么的观点和从事图书馆工作的人一样多。让我们探讨一下 React 是什么，以及框架中最重要的部分是什么。

## 正式

如果你访问 React 主页，你会发现 React 是什么的一个很好的描述。在顶部，你会看到“一个用于构建用户界面的 JavaScript 库”的字样。让我们把它分解一下。

### 【Javascript】

这里需要注意的一点是，React 现在是而且可能永远是一个 Javascript 框架。即使在处理 React Native 或其他库时，也是在处理 Javascript 代码。

### “构建用户界面”

这里是“大思路”。react——通过虚拟 DOM、diffing 算法和纤程等概念——是一种快速、反应式的构建用户界面的方法。有了像 React Native 这样的工具，这扩展到了大量的设备——React 是一个纯 web 库。

* * *

## 喜欢这个帖子？

这是我正在进行的“2019 年学习反应”系列中的第一篇。如果你喜欢，可以订阅我下面的时事通讯——我每周都会发送关于我的帖子的更新，我所学内容的分类，以及我在网上找到的一些很棒的链接。

### T2】🎉[立即报名！](https://mailchi.mp/c86513b586f3/dev)🎉

* * *

## 起反应的重要部位

很可能，你已经听说过很多关于 React 的不同特性——虚拟 DOM、组件、状态、钩子等等都构成了你工具箱中的不同工具。当你刚开始工作时，哪些是重要的？你应该把重点放在哪里？

### Javascript

帮助提高 React 技能的最佳方法是努力提高 Javascript 技能。React 是用 Javascript 构建的，对类和函数的良好理解会让你走得更远！

### 虚拟 DOM

虚拟 DOM 是快速反应的方式之一。React 使用 DOM 的虚拟表示，而不是进行复杂的操作，如交换`div`和重新呈现 [DOM](https://developer.mozilla.org/en-US/docs/Glossary/DOM) 上的列表。如果你把 DOM 看作一个对象，它可以有这样的节点:

```
{
    node: 'html',
    children: [
        { node: 'head', ...headElements }
        { node: 'body', ...bodyElements }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

该对象是虚拟 DOM 的一个例子。换出数组中的元素比换出 DOM 中呈现的`<li>`要快得多。如果你正在做大量的 DOM 更新，那么 React 可以批处理它们，而且上帝会更快地。

在学习了一点 JS 技能之后，我强烈推荐看看关于构建你自己的基本虚拟 DOM 的教程(就像[这个](https://medium.com/@deathmood/how-to-write-your-own-virtual-dom-ee74acc13060))。你会对你正在做的事情有一个更好的了解，这对学习很有帮助。

### 状态管理

最后，React 的另一个主要部分是状态管理。考虑 React 的一个很好的方法是将其作为一个框架，将状态片段作为一个视图来呈现。在这种情况下，组件只是特定状态的呈现视图。想想这个:如果你有一个数组中的条目列表存储在 state 中，让我们称之为`numbers`，`li`的呈现列表是那个状态在那个特定时间的一部分。如果你向数组中添加一些东西，`li`会更新，你会得到一个新的状态片段。

学习在 React 应用程序中管理状态是构建高性能 web 应用程序的好方法。我们将在以后的文章中详细介绍这一点！

* * *

在 React 中快速升级并不难。对 React 应用的工作方式和 React 是什么的基本理解可以帮助你构建优秀的、高性能的前端。下一次，我们将更多地介绍 JSX 及其运作方式。

感谢阅读！