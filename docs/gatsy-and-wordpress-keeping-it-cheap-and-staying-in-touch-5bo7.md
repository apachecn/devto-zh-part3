# 盖茨比和 WordPress:保持低价并保持联系

> 原文：<https://dev.to/booyaa/gatsy-and-wordpress-keeping-it-cheap-and-staying-in-touch-5bo7>

*Billy Huynh 在[Unsplash](https://unsplash.com/photos/saS98jKhVjA)T3 拍摄的照片*

这是一篇关于创建 Gatsby 网站的博文系列的开始，内容来自 WordPress 网站。

Gatsby 是一个基于 JAMStack (JavaScript、API 和 Markup)的静态站点生成器，使用 React 和 GraphQL 创建速度惊人的站点。它很快，因为它创建的网站是静态的，并且使用了像 service worker 和 webpack 这样的现代网络技术。你可以使用插件从各种来源获取内容，一旦被拉入 Gatsby，它们就可以变成静态资产。

在本系列结束时，您将会学到:

*   如何创建一个新的盖茨比网站
*   如何配置 WordPress 插件来连接 WordPress 博客
*   如何调整起始站点以使用新创建的 WordPress 文章和页面节点
*   通过 GitHub 自动发布到 Netlify
*   最后，建立 WordPress.com 博客，在发表新文章时通知 Netlify，以触发 Gatsby 站点的新构建

您需要遵循的事项:

*   GitHub 的一个账户
*   净收益账户
*   一个[WordPress.com](https://wordpress.com)的账户
*   最喜欢的小吃和饮料

我们还假设您了解 Git 基础知识、节点(和朋友),并且熟悉命令行。

如果你遇到困难，你可以在 GitHub 上查看我的参考网站。在一节的结尾，我将坚持使用与我们所做的更改非常匹配的提交散列。

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*