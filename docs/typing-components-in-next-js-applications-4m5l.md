# 接下来键入组件。JS 应用程序

> 原文：<https://dev.to/kosslebedev/typing-components-in-next-js-applications-4m5l>

> **注**:本帖涵盖接下来打字。使用 v9 之前版本的 JS 应用程序。从版本 9 开始，接下来。默认情况下，JS 有自己的类型，它们的名称可能与 DefinitelyTyped package 中使用的不同。如果你用 Next。JS 版本 9 及以上，请参考[官方文档](https://nextjs.org/docs#typescript)。对于早期版本，请继续阅读:)

在这篇文章中，我们接下来将讨论打字。JS 组件。接下来我们将使用[。JS](https://github.com/koss-lebedev/nextjs-typescript) 应用程序，连接到 Reddit API 并显示给定子编辑中热门帖子的列表。现在，主要组件`Posts.tsx`没有任何类型安全，所以我们将修复它。

该项目已经包含配置的 TypeScript，所以如果你不知道如何做，请阅读官方指南[这里](https://github.com/zeit/next-plugins/tree/master/packages/next-typescript)，它就像安装一些依赖项和放入一些配置文件一样简单。

现在让我们从 GitHub 克隆[项目](https://github.com/koss-lebedev/nextjs-typescript)并开始吧。

## 函数组件

首先，让我们看看一般情况下我们是如何输入 React 组件的。

如果我们把组件写成函数，我们可以使用 React 库中的`FunctionComponent`类型。此类型采用描述道具形状的泛型类型参数。我们的`Posts`组件接受一个子编辑名和一个帖子列表，所以`props`对象看起来像这样: