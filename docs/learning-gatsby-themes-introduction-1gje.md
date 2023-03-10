# “学习盖茨比主题”系列介绍

> 原文：<https://dev.to/ekafyi/learning-gatsby-themes-introduction-1gje>

在这个系列中，我记录了我试图理解、使用和制作盖茨比主题的旅程。在接下来的帖子中，我将发布代码、想法和其他我学到的材料。请注意，这些可能不是完善的、成熟的内容，而更像是“公共学习”的性质。

## 什么和为什么

> Gatsby 主题的工作方式类似于插件，但是在一个新的抽象层次上。他们可以将配置添加到项目中，实现页面，提供组件，并设置要在 GraphQL 中查询的数据源。— [盖茨比主题介绍](https://www.gatsbyjs.org/docs/themes/introduction/#how-do-they-work)

主题是去年年底推出的一个令人兴奋的新功能，尽管仍处于试验阶段。主题是盖茨比站点的*可重用块，可以*共享、扩展和定制* ( [来源](https://youtu.be/SV2zu9FvUTw?t=406))。你可以在这篇文章中找到[用例及基本原理的总结。](https://www.gatsbyjs.org/blog/2019-01-31-why-themes/#who-are-themes-for)*

我第一次接触主题(的描述)是在[主题团队 Github 页面](https://github.com/orgs/gatsbyjs/teams/themes/discussions/)上。该团队正在 Twitter 上邀请新的潜在贡献者，我表示了兴趣。不幸的是，我没有在问题和路线图中找到任何适合我目前技能水平的东西，但这听起来很有趣，我立刻就被吸引住了！

我觉得主题很有趣，因为:

*   主题化很好地利用了模块化的、基于组件的 JAMstack 架构。每个主题都解决了一个特定的问题/需求，而不会将您局限于任何特定的结构、数据源或与之无关的样式方法。博客主题会将博客添加到您的站点中；[组件隐藏](https://www.gatsbyjs.org/blog/2019-01-29-themes-update-child-theming-and-component-shadowing/)给你自由带来你自己的 UI (CSS-in-JS？萨斯？导入香草 CSS？你的电话！).考虑到 Twitter 上的开发者似乎从来没有在大多数事情上达成一致，我们可以开始构建而不必等待就所有事情达成一致，这很好！😬
*   这种方法鼓励创造性的定制和实验。除了使建立多个相关网站变得更容易(通过提取公共部分并将特定功能分成主题)*和*为网站添加功能，它还催生了像[数字花园](https://github.com/johno/digital-garden)和[街区](https://github.com/blocks/blocks)这样的项目。

然而，我对主题也有点不确定，因为:

*   我还没有研究主题的实际实现。我不确定最佳实践和抽象的“正确”方式。*对于【用例 X】，我应该把站点元数据放在站点中还是主题中？在主站点中应该设置多少默认配置(或者不设置)？*
*   我不确定利用主题化需要多少技术力量。它会超出我的技能水平吗？业余时间学有多容易/难？

嗯，开始学习的唯一方法是通过[制造东西](https://dev.to/ekafyi/using-gatsby-themes-in-a-non-theme-starter-54c7)。

## 资源

*   [官方主题文档](https://www.gatsbyjs.org/docs/themes/)
*   [官方博客文章标注“主题”](https://www.gatsbyjs.org/blog/tags/themes/)
*   [Github 问题和 PRs](https://github.com/gatsbyjs/gatsby/issues?utf8=%E2%9C%93&q=label%3A%22topic%3A+themes%22+)

*   视频(演讲和现场编码)

    *   [介绍盖茨比主题(克里斯·比卡尔迪)](https://www.youtube.com/watch?v=wX84vXBpMR8)
    *   与约翰·奥坦德(杰森·伦斯托夫)一起打造盖茨比主题
    *   [盖茨比和主题《盖茨比的未来》(达斯汀·绍)](https://www.youtube.com/watch?v=SV2zu9FvUTw)
*   示例储存库

    *   [jlengstorf/livestream-Gatsby-themes](https://github.com/jlengstorf/livestream-gatsby-themes)
    *   [Christopher biscardi/Gatsby-starter-theme](https://github.com/ChristopherBiscardi/gatsby-starter-theme)
    *   [Christopher biscardi/Gatsby-theme-examples](https://github.com/ChristopherBiscardi/gatsby-theme-examples)
    *   [碳-设计-系统/盖茨比-主题-碳](https://github.com/carbon-design-system/gatsby-theme-carbon/)
*   文章

    *   Alex Luong 创作你的第一个盖茨比主题

你还有其他与主题相关的资源想要分享吗？请在评论中分享！