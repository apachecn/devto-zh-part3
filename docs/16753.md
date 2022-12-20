# 盖茨比和 WordPress:摘要

> 原文：<https://dev.to/booyaa/gatsby-and-wordpress-summary-1kh9>

*马文·迈耶在[Unsplash](https://unsplash.com/photos/VvKdB1FYNZs)T3 拍摄的照片*

我们的博客系列已经结束了，那么我们学到了什么呢？

*   Gatsby 有一个非常好的高性能入门模板(`gatsby new`)。虽然它是基于 React 的，但资源的组织(组件、图像、页面、模板)使得编辑或创建新项目变得相当直观。
*   构建系统允许您通过实时重新加载获得关于您的更改的即时反馈。(`gatsby develop`)
*   Gatsby 插件生态系统是健康的，它为我们提供了许多将内容引入网站的方法。
*   内置的 graph QL explorer(graph QL)允许我们查询我们获取的 WordPress 数据。
*   盖茨比 API 很强大，但我们没有被它们淹没，因为我们只需要了解 [`createPages`](https://www.gatsbyjs.org/docs/node-apis/#createPages) 。
*   Netlify 在不断部署我们网站的新版本方面做了大量艰苦的工作。
*   使用 webhooks 我们可以连接 WordPress 和我们在 Netlify 上的站点。

如果你还在为这篇博文的标题感到困惑:保持低价并保持联系。

*   我们通过使用 WordPress.com 的免费博客和 T2 的免费主机来保持低成本。
*   我们通过使用 WordPress 自带的 webhook on event 功能保持联系。

我们还有很多内容没有涉及:

*   基于标签、类别、媒体、用户、分类或自定义文章类型创建页面。
*   为短代码、用户评论和搜索提供替换。
*   图像处理(这反过来需要使用 ACF 实体(高级定制字段)WordPress 插件)。
*   认证你的网站注册会员的内容。

谢谢你完成这个系列！请让我知道你是否想让我介绍关于 Gatsby WordPress 插件的其他话题。

*要转到系列的另一部分，点击当前标记(黑点)旁边的灰点。*