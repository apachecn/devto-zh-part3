# 盖茨比真的有那么伟大吗？

> 原文：<https://dev.to/bnevilleoneill/is-gatsby-really-that-great-24l4>

[![](img/1e36841332971ccb052b1b47396608e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vCHxrVtS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A08bkqYD3DU8PAQntuqtozQ.jpeg)

### 什么是 GatsbyJS？

[用他们自己的话说](https://www.gatsbyjs.org/)，“Gatsby 是一个基于 React 的免费开源框架，帮助开发者快速构建**网站**和**应用。**

为什么我们需要一个框架之上的框架(或者一个库)？React 不就是一个“帮助开发者搭建网站和 app”的工具吗？这是一个合理的问题。它经常被用于 Next.js 等工具。

React 是一个库，旨在为开发人员提供一组核心功能。它旨在重量轻且应用广泛。

另一方面，盖茨比是“一个静态的 PWA(渐进式网络应用程序)生成器。您可以获得开箱即用的代码和数据分割。Gatsby 只加载关键的 HTML、CSS、数据和 JavaScript，所以你的站点加载得越快越好。一旦加载完毕，Gatsby 就会预取其他页面的资源，因此点击网站的速度快得令人难以置信。

Gatsby 是一个利用 React 的静态站点生成器。

### 什么是静态站点？

静态网站已经存在很长时间了。事实上，它们很可能是原始网站:只是 HTML、CSS 和 JavaScript。它们不会在运行时呈现；没有服务器端代码，没有数据库等。

静态站点生成器是一个生成静态站点的工具。关于 JS 框架和库，它们通常在运行时在客户端生成 HTML 内容。静态站点生成器在构建时生成这些内容。然后，一旦加载完毕，React 就会接管，您就有了一个单页应用程序！

这与服务器端渲染有什么不同？这是一个很棒的问题。主要区别是没有服务器端代码。因此，与 Next.js 等工具不同，当发出请求时，Gatsby 不会在服务器上呈现任何内容。这些都是在应用程序构建时完成的。

静态网站的一些好处是:速度、搜索引擎优化和安全性。用 Gatsby 制作的网站不仅速度极快，而且由于没有数据库或服务器，它们具有隐含的安全性，而且与标准的 React 应用程序不同，它们使 SEO 变得容易得多，因为爬虫可以找到内容。

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 入门

首先，您只需下载 Gatsby CLI: npm i -g gatsby。这个命令行工具将允许您生成、运行和构建一个 Gatsby 应用程序。

为了让自己快速启动并运行，我使用了 [Gatsby 默认启动库](https://github.com/gatsbyjs/gatsby-starter-default)。克隆这个之后，我运行 npm install，然后 gatsby develop。这给我留下了一个运行在 [http://localhost:3000](http://localhost:3000) 上的完全活跃的 Gatsby 应用程序。

为了确认页面已经被渲染，您可以访问任何页面并点击**查看源代码**。但是，请注意，您也可以单击链接，页面将会更新，而不需要完全刷新，就像您对 PWA 所期望的那样。

### 为什么是盖茨比？

Gatsby 有一些很棒的特性，使它区别于其他静态站点生成器。

首先也是最重要的，Gatsby 利用了 React，所以对于那些喜欢它的人来说，它是一个很好的选择。

Gatsby 还使用了 GraphQL，这是一种流行且非常强大的技术。它使用 GraphQL 来管理整个应用程序中的数据，尽管您可以在没有 GraphQL 的情况下使用 Gatsby，但它确实使应用程序的开发更具声明性和直观性。

Gatsby 与 Netlify 等平台配合得非常好，每次提交和推送 GitHub 时，您都可以轻松地配置您的站点进行构建和部署。

Gatsby 还附带了一个巨大的插件生态系统，可以满足各种需求。这些插件可以帮助你从像 WordPress 或 Contentful 这样的 CMSs 中获取数据，帮助你集成像 Algolia 这样的工具，并通过延迟加载和优化来管理你的图像。

Gatsby 还提供了丰富的 API，使得静态站点的开发变得更加容易——例如，createPage API 使得在构建时使用模板将 markdown 文件转换成静态页面变得非常容易。

最后，Gatsby 有一堆很棒的组件，它们简化了路由、链接和处理图像等事情，这些都不包含在核心的 React 库中。

### 设计你的网站

设计一个外观和感觉确实是体验中最困难的部分。在某种程度上，我被各种可能性弄得不知所措。一些事情帮助我前进。

首先，我查看了一页又一页的获奖作品集网站，收集灵感，感受一下 T2 的伟大设计。

其次，我读了 Adham Dannaway 的一篇很棒的文章，讲述了他设计和创建个人网站的过程。

我致力于设计一些我自己的自定义动画和 SVG。这很有趣，我不能推荐 Figma 足够的努力。如果熟悉素描，使用起来会极其简单。

[![](img/5358a4d571e66eb02d1fcb4762c3243a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y8y_ol3t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/288/0%2ARKXaS2At9dVuGmwt) 

<figcaption>我用 Figma 创作的图像之一。</figcaption>

### 开发你的网站

为了起步，我使用了 [Gatsby starter 库](https://github.com/gatsbyjs/gatsby-starter-default/)。我把这个从 GitHub 分叉出来，重新命名为 repo。然后我克隆了它，用 Gatsby CLI 来构建。在很短的时间内，我就在 localhost 上运行了一个实时热重装应用程序。

代码的结构非常简单。src 目录中有三个文件夹:组件、图像和页面。

图像目录包含了一些在网站中使用的 png 文件。

组件目录包含几个值得注意的组件。首先，layout.js 组件:它是一个包装器组件，旨在跨应用程序提供样式和功能。这种模式在 React 中非常流行。您会注意到在组件的中心有一个名为{children}的绑定。

[![](img/e677b691010d1166bd26b4f5b7f4c4e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Na7Nkf2e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A4h41n3KVymgDMyke)

这表示要在组件内部传递的内容。您可以在 pages 目录下的 index.js 中看到这个组件正在工作:

[![](img/5841dbf2332d87ab266fc81ecf2dfa8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S4PX76HT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Az6Jx_ni-uMJoFHpt)

另一个值得注意的组件是 image.js:

[![](img/901b6c2926ec39d16da4ec5ee9c09c8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EDVwa_1O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AwJUIOJtMMfbl1ca5)

这个组件显示了一些有趣的事情。首先，它让我们对 GraphQL 在 Gatsby 中的使用有了一点了解。组件底部的 StaticQuery 用于在构建时拉入图像，并枚举一些图像处理规范(最大宽度为 300 的流体)。然后将它传递给 Gatsby Image 组件，该组件优化图像，创建几个不同大小的版本，并在运行时延迟加载图像。

在 *pages* 下，有主 index.js、二级页面和 404 页面。在 index.js 中，您可以看到布局组件是如何工作的，以及如何使用 Gatsby 的 link 组件链接到其他页面，比如 page-2.js。这个文件夹里的任何东西都会被 Gatsby 在构建时转换成静态页面。

这提供了很多你需要的现成的东西！您可以立即开始构建页面和组件。然而，如果你想动态地创建网页，比如说 markdown 文件，你可以在 Gatsby 的网站上[遵循这个简单的教程](https://www.gatsbyjs.org/docs/adding-markdown-pages/)。

### 搜索你的站点

[![](img/c8ed1d02c320b88f40e8ccd2063eff14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vMUNX94l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AGIhefOVXqfuCx5w0)

没有服务器的一个缺点是搜索你的网站并不简单。这可以通过使用一些伟大的工具来规避，如 Algolia。Gatsby 有一些使用 Algolia 或其他工具(如 Lunr 和 ElasticSearch)实现这一功能的很好的指南。

### 部署您的站点

一旦你创建了一个网站，你需要考虑如何托管它。有两个很好的选择领先于其他选择:GitHub Pages 和 Netlify。

我选择了 Netlify，因为他们提供持续部署(Git 触发的构建)、全球 CDN、完整 DNS、自动化 HTTPS 等等。

他们的免费层包括“无限的个人和商业项目，HTTPS，从公共或私人回购的持续部署，等等。”我也可以很容易地通过 Netlify 购买我需要的域名。这基本上是一个一站式商店，提供我的网站上线所需的一切。

将你的站点添加到 Netlify 就像创建一个帐户，链接你的 GitHub，点击**从 git** 创建站点按钮一样简单。

### 回顾

我应该用另一台发电机吗？我应该用 React 或 Next.js 建立一个站点吗？盖茨比有优点也有缺点。然而，通过某些用例，许多缺点被最小化了。对于创建个人网站来说，盖茨比是我的最佳选择。

工作流程非常简单:当我做出更改并将它们推送到 GitHub 时，我的站点被重新部署。我不需要太关心基础设施等。Netlify 为我处理所有的事情。

它在允许我编码和减少样板代码之间保持了很好的平衡。

我喜欢 gatsby-image 插件，使用 GraphQL 的能力确实简化了向组件传递数据。

我唯一不喜欢的是 gatsby-node.js 之类的文件中需要的一些配置对我来说不那么直观。

### 结论

总之，我真的会向那些正在寻找候选人来开发像作品集和个人博客这样的小型网站的人推荐 Gatsby 它确实在这些用例中大放异彩。

它具有静态网站的所有优点，例如速度、安全性和 SEO，对于那些喜欢做一点编码而不想只使用 WordPress 或 SquareSpace 的人来说，它特别棒！

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

盖茨比真的有那么伟大吗？最早出现在 [LogRocket 博客](https://blog.logrocket.com)上。