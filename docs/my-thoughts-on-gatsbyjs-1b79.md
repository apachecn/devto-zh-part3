# 为什么是盖茨比？

> 原文：<https://dev.to/frontendwizard/my-thoughts-on-gatsbyjs-1b79>

如果你一直在 twitter 上关注前端开发人员，你可能听说过 GatsbyJS。许多开发者已经从作为博客平台的 Wordpress 或 Medium 转型，开始用 Gatsby 启动他们自己的网站。有些人可能会说这是炒作，因为我们作为开发者已经为此内疚过很多次了。我们热爱我们闪亮的新工具，我们总是将代码重构为最新最棒的东西，只是为了在下一次浪潮中再做一次。

为了防范这种行为，我们需要更深入地挖掘，并试图理解其背后的原因。我们来探讨以下几个问题:**盖茨比是什么？它为开发者提供了什么？有哪些强有力的 it 用例？缺点是什么？**

# 盖茨比是什么？

不久以前，Gatsby 还被称为静态站点生成器。虽然这是完全正确的，但它不是一切。今天对盖茨比最好的定义是在写这篇文章的时候你在它的主页上看到的那个:

> Gatsby 是一个基于 React 的免费开源框架*T1，帮助开发者构建超快的网站和应用。*

这里的关键点是框架这个词。Gatsby 不仅仅是一个静态的站点生成器。这确实是一个 PWA 框架。它的插件提供的开箱即用的功能数量真的很惊人。

# 它为开发者提供了什么？

让我们面对现实吧，建立网站是很难的，我们总是低估 T2 所需要的努力，尤其是在最佳实践和可访问性方面。别让我从图像开始。有很多复选框，你需要检查之前，航运你的网站，没有做到这一点是不是和选项。作为开发者，我们需要为各地的每个人提供尽可能好的体验，否则我们可能无法在那一刻接触到每个需要该应用或信息的人。作为开发人员，我们有责任尽最大努力接触尽可能多的人，不管他们的设备、网速或其他什么。

这和盖茨比有什么关系？Gatsby 提供了许多现成的最佳实践和可靠的解决方案。Gatsby 团队的一个核心价值观是，网站应该是默认可执行的。渐进式图像加载？关键 CSS 的内联？无痛 PWA 配置？只要你说得出，盖茨比就会抓到你。真是令人印象深刻。不相信我？我鼓励你尝试一下。

图像解决方案是如此之好，以至于经常被认为是盖茨比的强项，尽管它实际上是一个插件。你所要做的就是在你的 Gatsby 配置文件中添加一些插件，然后你就可以开始工作了。您的图像将在构建时在 GraphQL API 上可用，图像组件将处理所有的大小调整，选择最佳的文件格式和其他一切。你甚至可以免费获得模糊效果来改善用户体验。

我们还没有谈到开发人员的体验。简直太棒了。看看这个:

*   你有 HMR，让你有即时反馈，而不需要重新加载页面和丢失状态。
*   您的应用程序将是静态的，因此您可以无服务器运行，这使得整个运营过程中您的应用程序始终可用并随时可用，这根本不是您的问题。无需担心维护服务器。梦是真实的。
*   你有最甜蜜的资产管道，在那里所有的事情都在构建时完成。
*   您还可以预览您的 PRs 部署，使其非常容易可视化的变化。

这是为你准备的最好的开发者体验之一。手放下。

# 盖茨比有哪些强用例？

最明显的用例显然是静态网站生成。但是不要让这愚弄了你，盖茨比可以做得更多。我要说的是，如果你正在用 React 构建一个 web 应用程序，它能够负担得起构建过程，并且不需要立即在它的路径上反映数据库的变化，那么 Gatsby 很适合你。老实说，我再也看不到自己为网站选择别的东西了，至少对我自己的副业来说是这样。

盖茨比展示区已经有了各种各样的网站和应用程序，我可以有把握地向你保证这将会增长。从文档，到商业，博客，甚至电子商务应用程序。

# 有哪些不好的方面？

我很难找到盖茨比的缺点。正如我上面提到的，对于那些你负担不起构建时间并且必须立即反映数据库变化的网页，你可能想要使用 [Next.js](https://nextjs.org/) 。

# 你的意见

你同意还是不同意我的观点？请在评论中联系我，让我知道，我们都会不断地从中学习。