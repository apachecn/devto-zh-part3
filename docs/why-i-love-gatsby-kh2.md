# 我为什么喜欢盖茨比

> 原文：<https://dev.to/caiorcferreira/why-i-love-gatsby-kh2>

*照片由[帕特里克·福尔](https://unsplash.com/photos/0gkw_9fy0eQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/blog?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5】*

最近我开通了自己的博客，这是一次奇妙的经历。在大约一个星期天的时间里，我已经完成了这个网站 90%的内容，并且享受了每一刻。这都要感谢盖茨比，在这里我会告诉你为什么！

# 简介

当我决定分享更多我在职业生涯中的所作所为和所学时，我立刻想到了博客。作为一个热情的读者和开发者粉丝，我的第一步是记下我的想法，将它们组织起来并在开发者社区发布。这很棒，反馈也很令人兴奋。

但对我来说，技术写作不仅仅是分享知识和建立形象。它就像一本日记，一面审视我自己和我职业生涯的镜子。我希望它能反映出我和我的每一个步骤。因此，作为这样一个特殊的主题，我觉得我需要一些更个性化的东西，因此，我自己的网页。

# **去❤️的理由**

建立个人博客有很多选择，比如 Wordpress、Blogspot、Jekyll 等等。在这本杂集里，[果酱堆](https://jamstack.org/)很久以前就引起了我的注意。它是一种基于 Javascript + API 的+标记构建快速、简单且可扩展的网站的 web 架构。它本质上是在构建时从 Markdown 和其他 API 源构建内容，并可以通过客户端 Javascript 添加动态行为。大多数 JAM 框架也被称为静态站点生成器，就像上面提到的 Jekyll 和 Hugo。由于 Jekyll 是在 Ruby 中构建的，而 Hugo 是在 go 中构建的，所以他们需要使用模板引擎来设计它的页面，这就引出了第一个原因。

## 做出反应

在 React 中主要从事前端开发工作，对我来说，它可以被用作模板引擎似乎很自然，因此，当我第一次见到 [Gatsby](https://www.gatsbyjs.org/) 时，它非常适合。有了 React，我们有了一个众所周知的模板 API，它是简单的 JSX/HTML，易于组件化和组合。

## 建筑

Gatsby 是一个开源框架，它允许我们使用 React 和多种数据源来构建高性能的网站，如 Markdown、CMS(尤其是[headless](https://headlesscms.org/))、web APIs 和多种文件(JSON、YAML、CSV 等)。所有数据源都被抽象在 GraphQL API 后面，创建了一种统一的方式来访问和管理数据。这提供了一个可扩展的解耦架构，为数据端和 React 生态系统中可用的所有工具带来了一个大型插件库。

## 开源

就社区、文档和内容而言，Gatsby 是我见过的最好的开源项目之一。如果你打算使用它，我强烈建议你阅读[官方教程](https://www.gatsbyjs.org/tutorial/)，它非常棒，会让你对它的架构有一个很好的了解。除此之外，社区提供了许多初学者知识库来帮助你完成你的项目。在我的例子中， [gatsby-starter-blog](https://github.com/gatsbyjs/gatsby-starter-blog) 提供了任何博客都想要的功能:SEO 工具、RSS feed、Google Analytics、网站清单、离线支持、主题化和排版配置的 CSS。

## 定制

上面列出的所有功能都是通过插件提供的，例如，SEO 工具提供了[头盔插件](https://www.gatsbyjs.org/packages/gatsby-plugin-react-helmet)(它允许我们用标题和关键字配置 HTML 头)。这些允许我们轻松地定制我们的项目。在我的博客中，我还添加了支持 SVG 的[插件](https://www.gatsbyjs.org/packages/gatsby-plugin-react-svg)(它允许我像 javascript 模块一样导入 SVG)，一堆更好的图像渲染插件(包括页面加载期间的低分辨率样本)，你可以在这里找到，使用 [PrismJS 插件](https://www.gatsbyjs.org/packages/gatsby-remark-prismjs)的代码样式和语法高亮。

但是我**最** **喜欢**的定制是 Styled Components(通过[这个插件](https://www.gatsbyjs.org/packages/gatsby-plugin-styled-components))与 typhony . js(也有一个插件，但它是随 starter 一起提供的)的组合，因为 Styled Components 使我能够使用常见的 CSS 工具，如媒体查询和伪元素以及 Javascript 函数，这允许我使用`rhythm`typhony . js 函数以一种[和谐的方式](https://betterwebtype.com/rhythm-in-web-typography)设置边距和填充。此外，由于 starter 附带了 CSS 模块支持，我可以创建一个包含 CSS 变量的全局样式表，使主题保持一致变得容易。

## 开发者体验

你只需要 3 个命令就可以使用盖茨比:

```
 $ npm install -g gatsby-cli
    $ gatsby develop # inside the starter repository
    $ gatsby build # when you are finished implementing your site 
```

Enter fullscreen mode Exit fullscreen mode

要添加插件，你只需要安装一个 npm 包，并把它的名字放在`gatsby-config.js`文件中。这是容易的，简单的 e 乐趣使用！😄

## 简单快捷的送货

从开发到最终网站你只差一个命令:`gatsby build`。生成的代码是简单的静态 HTML 和 CSS，可以部署到任何静态托管服务，如 Github 页面。因此，您可以免费轻松地构建和部署您的站点。

然而，对于我的博客，我决定在[网上主持。他们的服务非常简单，甚至不需要文档，尽管存在于您需要的地方，因为您可以点击部署您的站点，他们会带您指向 Github 存储库，选择一个命令来构建您的站点(因此，我们可以添加 gatsby-cli 作为`devDependency`并创建一个 npm 脚本来使用`node_modules`二进制文件构建站点)和“boom”，您将在一个随机的域中拥有它。](https://www.netlify.com/)

除此之外，如果你也想要一个自定义域名，你可以直接通过他们购买，你的网站也将使用 Netlify 提供和配置的加密 TLS 证书在 HTTPS 运行。

它也是一个考虑到开发人员经验的平台，如果你愿意，它有很多特性可以将你的网站扩展成一个 web 应用程序。

# 亲提示

如果你打算用盖茨比来建立你的博客，我有一些额外的建议！

## 寻求灵感

今天，我最喜欢的 dev 博客是 Dan Abramov 的[反应过度的](https://overreacted.io/),因为他也使用 Gatsby 来建立他的博客，所以我从阅读他在 Github 中打开的源代码中学到了很多。如果你像我一样，没有太多的 UI 设计技能，在博客中有一个你喜欢的东西是非常有帮助的。

## 运行灯塔

Chrome 附带了一个不可思议的工具，用于审计网站的四大支柱:性能、可访问性、最佳实践和 SEO。Gatsby killing 的一个特点是，它的默认值让你在这次评估中获得高分，但 run each 确实有助于捕捉你可能忽略的任何错误。像我一样，忘记在社交媒体链接图标上添加`aria-label`。

## 主题

为你的博客设定一个主题将有助于为你自己打造一个品牌，并改善读者在使用你的网站时的体验。因为我在选择颜色方面是个灾难，所以[coolers](https://coolors.co/)拯救了我，提供了一个很好的颜色方案。

# 总结

最后，盖茨比不仅有用，而且玩起来真的很有趣。我享受使用它的每一刻，在一个星期天，我设法实现了网站的 99%,只留下一些小的调整。在不到一周的繁忙时间里，我把我的博客放到了广播上，并欣赏了这里的风景！

*如果你喜欢这个帖子，你可以在 [caioferreira.dev](http://caioferreira.dev/) 上看到更多，如果有任何问题或意见，请留下评论，让我们一起讨论！*