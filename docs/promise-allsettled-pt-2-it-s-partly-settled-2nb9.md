# Promise.allSettled() Pt.2 -已经部分解决了！

> 原文：<https://dev.to/irreverentmike/promise-allsettled-pt-2-it-s-partly-settled-2nb9>

这是我关于即将到来的`Promise.allSettled()`功能的[第一篇文章](https://mike.biful.co/solve-all-your-problems-with-promise-allsettled)的后续，即将来到你附近的一个节点应用。

今天早些时候，我收到了来自 GitHub 用户`@j-f1` :
的[拉请求](https://github.com/mbifulco/blog/pull/14)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新使用文档 #14](https://github.com/mbifulco/blog/pull/14) 

[![j-f1 avatar](img/b3511b457fedd5d7006959c6646c625d.png)](https://github.com/j-f1) **[j-f1](https://github.com/j-f1)** posted on [<time datetime="2019-04-26T17:20:41Z">Apr 26, 2019</time>](https://github.com/mbifulco/blog/pull/14)[View on GitHub](https://github.com/mbifulco/blog/pull/14)

事实证明，*是一种将`Promise.allSettled()`添加到你的应用程序*的便捷方式*！*🎉。它使用起来也极其简单。

## 核心-js npm 包

没错- [core-js](https://github.com/zloirock/core-js) 。从他们的`README.md`来看，这正是它听起来的样子:

> *   Is polyfill of JavaScript standard library, which supports:
>     *   The latest ECMAScript standard.
>     *   ECMAScript standard library proposal.
>     *   Part of WHATWG/W3C standard (cross-platform or closely related ECMAScript).
> *   It is the maximum modularity: you can easily choose to load only the functions you will use.
> *   Can be used without polluting the global namespace.
> *   [is tightly integrated with `babel`](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md#Babel) : this allows many optimizations of `core-js` import.

进一步查看自述文件中的[，polyfill 中有一个受支持功能的列表:](//er/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md#ecmascript-proposals)

> *   [`Promise.allSettled`](https://github.com/tc39/proposal-promise-allSettled) Second-stage proposal

该死的。那就行了！

## 如何使用 core-js

正如`@j-f1`所指出的，在任何使用`babel`作为传输器的项目中，你所需要做的就是将 core-js 添加到你的项目中，并将其包含在你的应用程序的入口点:

首先，将依赖项添加到您的项目中

```
> yarn add core-js 
```

Enter fullscreen mode Exit fullscreen mode

然后，在你的应用程序的入口点(通常是类似于`index.js`，或者项目根目录下的`app.js`):

```
import 'core-js'; 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您想只包括*和`Promise.allSettled()`聚合填充的*，而不包括其他内容，请使用:

```
import 'core-js/proposals/promise-all-settled'; 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！🍻