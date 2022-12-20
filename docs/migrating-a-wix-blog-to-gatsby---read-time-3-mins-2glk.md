# 将 Wix 博客迁移到 Gatsby -阅读时间 3 分钟

> 原文：<https://dev.to/steelwolf180/migrating-a-wix-blog-to-gatsby---read-time-3-mins-2glk>

[![Photo by frank mckenna on Unsplash](img/dd2a1c1a0aaec74e3f8579383206f211.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sgTJcHLT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/410odziaisrwoek5mnio.jpg)

## 简介

上周，我把我的博客文章从 Wix 转移到了 Gatsby。

在那之前，我花了将近一个月的时间浏览了《盖茨比》和《T2》的教程，这样我就可以把我的博客迁移到《T4》【盖茨比】,因为这些年来我在使用 **Wix** 时越来越沮丧。

## 为什么从 Wix 转到 Gatsby？

首先，Wix 对于不是开发人员的博主来说很有用。但是到了一个开发者手里，就非常失败了。

简单的功能，比如把我的代码片段嵌入到一篇博客文章中，这需要我通过一堆障碍去做，做起来相当痛苦。

在博客文章中添加图片也几乎是同样的体验。

除此之外，还有很多我并不经常使用的功能，比如**邮件活动**。网站加载需要很长时间，这是我的一个主要障碍，让我开始寻找替代方案。

## 心满意足地写博客

**contentiful**is**headless CMS**是我在搜索开发者将博客迁移到 Gatsby 的教程时偶然发现的。

我喜欢它的一个关键特性是标记编辑器，它可以让你轻松地将代码片段或图片嵌入到你的博客文章中。

我喜欢它的第二个特性是，它不是将您的**标记文件**一起存储在您的 git 存储库中。

每当你发布一篇文章，Contentful 就会将你的标记文件转换成一个 **GraphQL 端点**。以便 Gatsby 可以使用它来创建 HTML 和 CSS 文件。

## 管道发布博文

从下周开始，我将创建一个 **2 部分博客系列**，在那里我将从头到尾向你展示如何在 **Gatsby** 建立一个开发者博客。

我将详细介绍从域名注册到部署，最后在 Gatsby 中创建你的第一篇博文。

我将使用的工具是 **Contentful** 、 **GitLabs** 、 **Netilfy** 和 **Gatsby** ，所以请务必查看我的[博客](https://maxongzb.com)或 **dev.to** 网站。

## 结论

在花时间将我自己的 Wix 博客迁移到盖茨比之后。我学会了理解为什么一个开发者在项目中使用标记语言来做文档。

之前我不明白为什么我们要这么做，但是现在在把我的博客和**昆西·拉森**写的 **JAM Stack** 文章从**自由代码营**转移到**开发者到**后，我意识到了真正的原因。

这样做的真正原因是，任何人都可以对文档做出贡献，即使他们只是使用它的初学者。

因为拥有优秀的项目文档有助于减少完成工作所需的时间。

最终，我花了很长时间才把我的博文从 Wix 上迁移过来。因为《盖茨比》中的这个错误。

通过仔细阅读文档，我花了几个小时才最终意识到并理解我忘记了为页面和帖子添加元数据。

## 参考文献

### 为什么我移动我的网站来回应盖茨比的满足和虚荣

[https://dev . to/iron cove/why-I-moved-my-website-to-react-Gatsby-contentful-and-net lify-4op 9](https://dev.to/ironcove/why-i-moved-my-website-to-react-gatsby-contentful-and-netlify-4op9)

### 用盖茨比建博客。JS &反应如何

[https://dev.to/saigowthamr/build-a-blog-using-gatsbyjs-react-2lo
T2】](https://dev.to/saigowthamr/build-a-blog-using-gatsbyjs--react--2lo)

### freeCodeCamp.org 如何使用 JAM stack +单个 API 服务器帮助每月数百万人学习编码

[https://dev.to/ossia/how-freecodecamporg-uses-the-jamstack-一个单 api 服务器每月帮助数百万人学习编码-4d5g](https://dev.to/ossia/how-freecodecamporg-uses-the-jamstack--a-single-api-server-to-help-millions-of-people-learn-to-code-every-month-4d5g)