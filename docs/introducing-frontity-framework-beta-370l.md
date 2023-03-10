# Frontity Framework Beta 简介

> 原文：<https://dev.to/frontity/introducing-frontity-framework-beta-370l>

我们很高兴地宣布，今天我们发布了第一个 Frontity 框架测试版！🎉

Frontity 是一个基于 React 开发 WordPress 网站的免费开源框架。这是第一个专门关注 WordPress 的 React 框架——它的每一部分都经过了简化和优化，以便与它一起使用(作为一个无头或解耦的 CMS)。

## Frontity 的测试版有哪些内容

Frontity Framework 对初学者很友好:它旨在让所有开发者更容易使用 WordPress 和 React **建立网站**和**更容易使用**，即使对于那些不太熟悉 React 的人也是如此。

以下是第一个测试版中包含的内容列表。

#### 零设置开发

一切都已经准备好了，你可以专注于构建你的网站:React、webpack、Babel、SSR、Routing、CSS-in-JS、WP REST API、TypeScript、林挺、测试等等。不需要额外的配置。

这意味着您可以将大部分时间花在项目的开发需求上(例如主题)，而将较少的时间花在建立项目或担心工具和配置上。

#### 迅雷不及掩耳

Frontity 发送一个准备好开始浏览站点的 HTML，所以最初的加载感觉几乎是即时的。不需要额外的资产或往返。

这个 HTML 功能齐全，无需 JavaScript 即可导航。一旦 React 加载，它就会控制应用程序，用户不会注意到任何变化，它是 100%透明的。

#### 即时应用内导航

React 加载后，Frontity 的路由器会自动预取其他路由和数据。当用户在应用程序中导航时，他们永远不必等待。

#### 代码拆分

Frontity 使用 webpack 来拆分代码，并发送应用程序工作所需的最少代码。它还允许开发者在可加载组件的帮助下动态加载组件。点击了解更多信息[。](https://docs.frontity.org/learning-frontity/code-splitting)

#### 服务器端渲染

Frontity 用 React 生成的完全填充的 HTML 文件进行响应。这减少了第一次绘制所需的时间，对 SEO 也有好处。

使用 WordPress REST API 检索内容。一旦 React 被加载到浏览器中，它就会控制页面并施展它的魔法。

#### 扩展性

Frontity 最令人惊奇的一点是它灵活的可扩展性模式，更类似于 WordPress 本身，而不是其他 JavaScript 框架。

开发人员可以创建他们自己的定制包(扩展)或通过安装任何现有的 Frontity 和 npm 包向他们的网站添加新功能，而不必从头开始构建它们。此外，Frontity [主题](https://api.frontity.org/frontity-themes)和[包](https://api.frontity.org/frontity-packages#official-frontity-packages)可以在不修改代码的情况下激活和停用，并且可以跨项目重用。

除了这些扩展，还有其他专门为 Frontity 创建的工具和功能，如[无限滚动挂钩](https://api.frontity.org/frontity-packages/collections-packages/hooks/infinite-scroll-hooks)和[自动预取](https://api.frontity.org/frontity-packages/collections-packages/components#auto-prefetch)，它们也是 Frontity 可扩展性的一部分。

#### 一流的打字稿支持

Frontity 有惊人的类型脚本支持。事实上，我们非常喜欢它，以至于 Frontity 本身就是使用 TypeScript 构建的。但是不要担心，它绝对是可选的:如果你不知道或者不想学习它，你可以使用普通的 JavaScript。

#### 可能的最小反应束

Frontity 帮助建立网站，快速提供更好的用户体验。这就是我们努力让内核变小的原因。但是嘿！它最终减少了 60%，重量仅为 60kb (gzipped)。

#### 无服务器和水平缩放

Frontity 服务器非常小，非常适合无服务器要求。这意味着前端可以无限扩展。Frontity 还准备在任何节点服务器中进行水平扩展。

#### 现代浏览器对 ES6 的支持

Frontity 生成了两个 JavaScript 包:

*   一个在 ES6 中，没有传输或聚合填充，因此它尽可能小和快。
*   另一个在 ES5 中，用于不支持 ES6 的老浏览器。

支持 ES6 模块的现代浏览器将请求 ES6 捆绑包，这意味着在浏览器中减少了捆绑包的大小并缩短了评估时间。这保证了现代浏览器的性能不会受到损害，同时确保了与旧浏览器的向后兼容性。

#### 支持 WordPress.com【WordPress.org】支持 T2

Frontity 可以使用不同的“源”扩展。这个第一次发布包括一个`wp-source`，它可以与任何[wordpress.com](https://developer.wordpress.com/docs/api/)或[wordpress.org](https://developer.wordpress.org/rest-api/)网站的 REST API 一起工作。这样 Frontity 可以不需要任何额外的 WordPress 插件。

这个框架已经被设计成可以在将来支持其他资源，比如用于 WordPress 的 GraphQL API。

#### 支持一次安装多个站点

这类似于 WordPress multisite: Frontity 允许你只安装一个软件就可以服务任意数量的站点。这对于管理不同客户端的用户或想要创建网络的用户来说非常有用。

#### 反应并发和悬念

React 团队正在努力发布一个异步的“无 CPU 阻塞”版本，Frontity 将与它兼容。预计将于 2019 年在 Q2 举行。一旦发布，我们期望看到 React 动画库的使用增加，这将使用户体验更上一层楼。

#### 久经考验的框架

在过去的两年里，我们正在开源我们用来支持大型 WordPress 新闻网站的内部框架。Frontity 被数百万读者所使用，它已经被证明是构建引人入胜的前端体验的理想工具。点击阅读完整故事[。](https://frontity.org/about-us/)

## 帮助改善 Frontity 框架

Frontity 的入门和测试真的很容易，我们邀请您按照[文档](https://docs.frontity.org/)和本 **[快速入门指南](https://docs.frontity.org/getting-started/quick-start-guide)** 进行操作。

一旦你试用了它，请加入[社区论坛](https://community.frontity.org/)，让我们知道它的进展。您的**反馈**对今天的测试版和最终版本的形成非常有价值。您可以加入现有的对话或打开新的主题(线索)。

错误报告和其他贡献也将受到高度赞赏。有关如何参与的更多信息，请查看[本指南](https://docs.frontity.org/contributing/how-to-contribute)。

我们迫不及待地想听听你对 Frontity 的看法！

## 从不错过一次更新

如果你想了解 Frontity 的最新消息，别忘了订阅 Frontity 简讯。您将收到最新的框架更新、学习资源和社区新闻。

如果你在寻找关于 Frontity 的新闻、视频和主要更新，Twitter 、[、Youtube](https://www.youtube.com/c/Frontity/) 和[博客](https://frontity.org/blog/)也是不错的地方。

如果您对该框架有任何其他问题，请在[社区论坛](https://community.frontity.org/)分享。我们希望更多地了解您以及 Frontity 如何帮助您的项目。

*最初发表于[frontity.org/blog](https://frontity.org/blog/introducing-frontity-framework-beta/?utm_source=dev.to&utm_medium=introducing-frontity-beta-post&utm_campaign=beta-launch)。*