# React 生命周期方法 React 16 入门

> 原文：<https://dev.to/stephencweiss/react-lifecycle-methods-a-primer-with-react-16-24og>

当我第一次开始使用 React 时，我没有意识到不同的生命周期方法有多大帮助。有一段时间，我做一些更简单的项目，只需要使用一个`constructor`、`render`，有时如果我变得*、*的话，还可以使用`componentDidMount`就可以了。

我知道还有更多，但是，我没有一个很好的框架去思考它。虽然 React 有很多关于生命周期方法的文档，但是我不能把它们放在上下文中。Scott Domes 的文章在这方面真的很有帮助——特别是他对 React 16 生命周期的更新。

要理解*为什么*生命周期方法不同于其他方法，比其他方法更特殊，只需看看 React 文档:

> 每个组件都有几个“生命周期方法”，您可以覆盖这些方法，以便在流程中的特定时间运行代码。

让我们来分解一下:“每个组件都有几个‘生命周期方法’”意味着 React 将在构建、更新和拆除 DOM 上的组件的不同阶段寻找这些方法。

“…您可以覆盖”意味着当您在组件中定义它们时，您是在告诉 React 当我们到达那个阶段时该做什么。想一个`render`方法。通过定义`render`，我们描述了*我们想要渲染什么*——当时，React 管理*。*

“……在流程中的特定时间”是指生命周期的不同阶段，其中有四个阶段:安装、更新、卸载和错误处理。

因此，通过在组件中定义不同的方法，我们可以告诉 React 我们在某些时候想要做什么。

有几件事要记住:

在渲染之前，我们不能访问 DOM，因此应该避免任何 DOM 操作。例如，使用 AJAX 请求检索显示在页面上的数据应该发生在挂载之后的*。泰勒·麦金尼斯解释道:*

> 您不能保证 AJAX 请求在组件挂载之前不会旋转。如果它真的[resolve before],那就意味着你将试图在一个未安装的组件上设置状态，这不仅不会工作，而且 React 会对你大喊大叫。在 componentDidMount 中使用 AJAX 将保证有一个组件可以更新。

*   一些生命周期方法是静态的，比如`getDerivedStateFromProps`，这意味着它们不能访问`this`的本地上下文。

我画出了不同的方法以及它们是如何相互作用的。当你看到这些都被可视化地展示出来时，不同的方法开始变得更有意义——就它们做什么而言，但更重要的是，就它们如何交互而言。

我没有展示我粗糙的手绘，而是找到了这张图片——它看起来更漂亮，而且，如果你点击链接，它是交互式的。

[![lifecyclemethods](img/5aa583c1edba6e07c770f07f5a9e5768.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--upPopDYo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://gatsby-starter-blog-demo.netlify.com/static/45d46c893195a885caf7e5d159a09d9e/fb8a0/lifecyclemethods.png) 来源: [React 生命周期方法图](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

# 补充阅读

*   [反应文件](https://reactjs.org/docs/react-component.html)
*   [反应 16 种生命周期方法以及如何使用它们](https://blog.bitsrc.io/react-16-lifecycle-methods-how-and-when-to-use-them-f4ad31fb2282)