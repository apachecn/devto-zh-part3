# 不要仅仅靠反应来建立你的投资组合

> 原文：<https://dev.to/flexdinesh/dont-build-your-portfolio-with-just-react-11a9>

**TL；DR** -与[盖茨比](https://www.gatsbyjs.org/)或 [Next](https://nextjs.org/) 一起使用 React 来构建静态公共站点。

React 现在风靡一时，我觉得用 React 开发网站容易多了。当然，你也可以使用和支持其他框架，但是我将这篇文章的内容限制在 React affictionados。

# 为什么要用 React

*   基于组件
*   带有[创建-反应-应用](https://facebook.github.io/create-react-app/)的预配置(大部分)启动器
*   更快的发展
*   React 太棒了🎉

# 为什么不该用刚才的反应

*   DOM 是在浏览器中构建的(客户端)
*   SEO 含义
*   更高的[互动时间(TTI)](https://developers.google.com/web/tools/lighthouse/audits/time-to-interactive)

让我们更深入地了解一下所有这些事情发生的原因。

在这一行被调用后，React 将站点加载到 DOM 中。

```
ReactDOM.render(<App />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

这意味着什么，只有在加载并执行了`bundle.js`之后，站点的整个 DOM、样式和行为才会被初始化。

**如果你在浏览器设置中关闭 JavaScript 并打开一个 React 站点，什么都不会加载，你只会看到一个空页面**。

不幸的是，在**索引**期间，**搜索引擎**就是这样看待你的网站的。所以如果你用 React 创建你的站点，只有你的页面标题会被索引，而 React 代码中的其他内容都不会被索引。

编辑:谷歌在索引过程中有限地执行 JavaScript，这意味着如果你在第一次渲染时没有内容，或者如果渲染时间超过 N 秒，它会跳过内容。此外，DuckDuckGo 最近已经成为很多人事实上的搜索引擎，一年多来它一直是我唯一的搜索引擎。而且 DuckDuckGo 在索引过程中不执行 JavaScript。因此**依靠搜索引擎执行 JavaScript 不是一个安全的赌注**。

除了 SEO 的影响之外，在第一次加载(画图)时不加载 DOM 会增加你的 TTI 度量。如果您可以在加载脚本之前的第一次加载中加载 DOM，那么您将极大地提高 UX。

# SSR 和 Static Site 代用于救援

> **SSR 网站**和**静态网站**是两回事。SSR 页面在每次请求时从服务器呈现，而静态页面是在构建时生成的。

如果你使用像 [Gatsby](https://www.gatsbyjs.org/) 或者 [Next](https://nextjs.org/) 这样的框架，你可以生成静态站点或者创建 SSR 页面。

换句话说，如果您关闭 JavaScript 并加载这些站点，整个 DOM 将被加载，因为页面已经呈现。

理想情况下，在第一次渲染后，只应加载和附加事件处理程序和补充数据。

# 盖茨比还是 Next？

*   如果你正在建立一个静态的公共网站，盖茨比将是合适的工具。
*   如果你正在建立一个有登录和大量服务器端逻辑的网站，Next 将是合适的工具。

当然，有很多其他的选择和理由来选择你合适的工具，但就个人而言，这两个对于大多数初步需求来说已经足够了。

其他争论点

*   你可以用 React 创建 SSR 页面，但是你需要手动设置你的服务器和所有的东西。Next.js 让整个过程变得更加简单。
*   谷歌声称它最近在索引时执行 JavaScript，但我试过了，对我不起作用。我们不确定其中的细微差别。

## 盖茨比是❤️

另一条新闻是，我完全被盖茨比·❤️迷住了，并且不可思议地爱上了他

我把我的旧作品集从 React 迁移到 Gatsby 正是因为这篇文章中讨论的原因。这是我在《盖茨比》——[中的新作品集。](https://dineshpandiyan.com)

展望未来，我计划今年在盖茨比建立我所有的副业。我创建了一个 Gatsby starter 样板，在默认的 Gatsby starter - [Gatsby 样板](https://github.com/flexdinesh/gatsby-boilerplate)上添加了配置。

目前为止我的盖茨比网站

*   [迪内什·潘迪扬](https://dineshpandiyan.com)
*   [tweetfancy.io](https://tweetfancy.io)
*   另一个宠物项目在 WIP 阶段。我可能会在一个月后推出它。

你太棒了！✨