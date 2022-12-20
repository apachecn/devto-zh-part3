# GatsbyJS 和 WordPress 让网站重获新生

> 原文：<https://dev.to/robmarshall/gatsbyjs-and-wordpress-gives-website-a-lease-of-life-205l>

在拥有相同的 WordPress 主题三年多之后，是时候改变了！

这有几个原因。主题没什么大问题，但字体在手机上不是最好的。用手机看文章，不用眯着眼，真好。

我也想要一个快速的博客。没有什么比想要阅读一篇文章更糟糕的了，由于所有的膨胀，它需要花费很长时间来加载。这个博客是非营利的(但是如果你使用 [Brave 浏览器](https://brave.com/tho935) …，你可以给我小费),所以我不需要加载谷歌广告。我认为加载最慢的是谷歌字体(这是我需要调查的)。

当需要一个快速的网站时，似乎只有一个选择——拥有一个静态网站。很长一段时间以来，原则上我一直是静态网站的大力倡导者。好处显而易见。减轻了服务器的压力。在页面加载速度上有更好的用户体验，消耗更少的用户数据。由于是无头的，我可以使用任何我想要的后端(我想坚持使用 WordPress)。

在我的文章“[内容网的兴起](https://thoughtsandstuff.com/the-rise-of-the-content-mesh/)”中，我谈到了静态网站。事实上，现在比以往任何时候都更有可能用许多不同的微服务来制作一个网站。这些服务旨在做一件事，并把它做好。就像建立一个静态网站是为了做好一件事一样——创建超快的网站。

所有艰苦的工作都由盖茨比完成，在 Netlify 上运行。这两者配合得非常好，部署一个站点是如此的简单。只要将 Netlify 连接到 git 存储库，它就会在每次推出新版本时构建一个最新的站点。他们有一个免费的计划，这是一个很好的开始。真的推荐！一旦我超越了他们，我可能会转向 AWS 计划或类似的计划，但现在它是完美的！

由于想坚持使用 WordPress，我最大的担忧是如何轻松地获得我写的文章。如果有人已经接受了挑战，我不想重新发明轮子。不这么做是明智的。 [Gatsby Source WordPress](http://gatsbyjs.org/packages/gatsby-source-wordpress) 是令人难以置信的 Gatsby 插件，它使得 WordPress REST API 可访问。这个插件意味着几乎所有东西都可以使用 GraphQL 查询来访问。这些是选择性查询，只传递您当时需要的内容。大幅降低管理费用。我可以访问所有的页面，帖子，设置，分类等。如果我需要延长，我可以！几个简单的查询就能提取出整个站点组件的数据，而且作为组件，它们是完全可以互换的！

该网站是一个正在进行的项目。我正在慢慢增加新的功能，如与谷歌分析容易集成，与 WordPress Yoast 搜索引擎优化完全兼容。目的是创建一个功能齐全的模板，以启动未来的项目。如果你感兴趣，github 库是:[https://github.com/justlikethisdesign/gatsby-tns](https://github.com/justlikethisdesign/gatsby-tns)

因为我坚持使用 WordPress。我仍然使用一个标准的管理面板，这个主题在顶部处理前端:[https://github . com/justalikethisdesign/WordPress-REST-API-Theme](https://github.com/justlikethisdesign/WordPress-REST-API-Theme)

这是一个微小的主题。事实上，它所做的只是将用户重定向到另一个网站(当更新时，我的网站——或者你的网站，如果你想使用它的话)。这个主题将会是我的无头 WordPress 网站的支柱，因为我将会建立更多这样的网站。

我不得不使用一些插件来确保所有的 REST APIs 都可供我使用:

*   [WP-API 的 JWT 认证](https://wordpress.org/plugins/jwt-authentication-for-wp-rest-api/)
*   [WP REST API 菜单](https://wordpress.org/plugins/wp-api-menus/)
*   [WP API Yoast](https://github.com/jmfurlott/wp-api-yoast)

这些插件为盖茨比提供了更多的功能。这使得整个开发过程更加顺畅。

这仅仅是过程的开始，但是我已经看到了性能的巨大提高，而且构建过程更加令人愉快！

我删除了评论。因为谁需要垃圾邮件😉