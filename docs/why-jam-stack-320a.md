# 为什么是果酱栈？

> 原文：<https://dev.to/steelwolf180/why-jam-stack-320a>

[![Photo by Miguel Bruna on Unsplash](img/20cf785501ea915333ebd5f7dd294bed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qEGACf2F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/htouxdgk4ss93o4q6hbp.jpg)

## 简介

当我开始寻找替代方案来迁移我的博客时，它以前是在 Wix 中。

我在考虑使用 **WordPress** ，因为它的**支持**许多主机提供商和**负担得起的价格**允许我托管我的博客。

我偶然发现 youtube 视频或文章，谈论**静态站点生成器**。

它对使用它建立的网站概括为**安全性**、**负担能力**、**速度**和**维护时间**。

## 什么是果酱栈？

你可能会奇怪，为什么我谈论的是静态站点生成器而不是 JAM stack？对我来说，这其实是一回事。

我们来分解一下 JAM 是什么意思。 **JAM** 代表第一个 **J** 代表 **Javascript** ， **A** 代表 **API** ， **M** 是**标记**。

[![iso-scene-takeshape-red](img/1d5f0f5d714de4b1c40f9d13578b3bb8.png)](//images.ctfassets.net/ly2f59p4unnn/4iUFLqzAIGF6gPAmxWiE6c/d72a09938ed06d56e68a40034615d75e/iso-scene-takeshape-red.jpg)

### J 为 Javascript

对于 JAM 中的 javascript，它可能是前端 web 开发中的三大巨头。

对于大多数 web 开发人员来说， **Vue** 、 **React** 和 **Angular** 都有自己的**静态端生成器**到**呈现**它们的**数据**。

这并不意味着其他静态站点生成器不能用于 JAM stack 的 **J** 部分，这是**雨果**、**杰基尔**、**下一个**确实想到的。

### A 为 API

JAM stack 的 API 通常用于为网站提供特定的功能，如**内容交付**、**电子邮件**、**支付**以及其他相关服务。

因此，除了公司网站或博客之外，JAM stack 还可以用于各种网站，如电子开始。

### Markup

标记是用来为作为 API 端点的静态站点创建内容的面包和黄油。

因为它们通常通过使用静态站点生成器被转换成 HTML、CSS 或 Javascript。

加载一个网站的时间非常快。

## 卡纸堆叠的优点

[![global-competitive-advantage-success-pillars-ahero-t](img/613b6475dc0b3e4fa4968688d3f069e1.png)](//images.ctfassets.net/ly2f59p4unnn/DJLktwh2DEmAgvn1vRbgJ/25192395ec11331980bfa454982278c4/global-competitive-advantage-success-pillars-ahero-t.jpg)

### 易于开发和部署

这是因为生态系统和各种服务提供商提供的服务。

基于 JAM stack 的网站的**开发**和**部署**相对来说是不痛不痒的，并且可以轻松扩展。

以我的个人博客为例，我使用 **netlify** 作为静态网站的托管和部署服务。

每当我将代码更改推送到 Gitlab 库时，构建过程就开始了。

然后，它从 **Contentful** 端点和各种其他服务端点获取内容。

其中结合了我推送的代码修改，通过 **Gatsby** 转换成各种静态文件。

这些新生成的静态文件随后被部署在 **Netlify** 上，作为一个由 **Netlify** 为**免费提供 CDN、HTTPS 和表单提交的网站。**

### 内容创建

内容通常是一个 **markdown** 文件格式，但也可能是其他文件格式，这取决于你的静态站点生成器。

即使对于不是开发人员的人来说，管理和创建减价文件的内容也是相对容易的。

这些可以在 gitLab 或 **Contentful** 中，他们提供了一个 API 端点，你的静态站点生成器可以直接获取内容。

最后，绝不是内容应该只在降价。

事实上，内容可以从像 WordPress & Contentful 这样的 CMS 中提取，或者通过显示数据的外部 API 源提取

### 维护费用&管理网站

通常，维护一个网站的相关成本包括**托管**、**版本更新**和**内容更新**。

用一堆虚拟主机提供商来托管一个静态网站确实是**负担得起的**或**免费的**，这些虚拟主机提供商包括我自己使用的 **Netlify** 、AWS 、 **Github** 或 **GitLab** 。

不像 WordPress，由于安全原因需要**定期更新**。

一个静态网站不需要使用静态文件来更新你的网站，这对任何黑客来说都是困难的。

利用在您的静态文件中发现的**安全漏洞**的**优势**来入侵您的网站。

## 结论

[![conclusion@2x-1](img/084faeb124bfb88e940b89a2a46622f2.png)](//images.ctfassets.net/ly2f59p4unnn/5sJzQ2UpSyXJUkmU2w2ZtI/79387724b14850dd73123b99676717e5/conclusion_2x-1.png)

到目前为止，由于以上我列出的优点，越来越多的网站部署了静态站点。

其中一个使用静态网站的著名网站是 **FreeCodeCamp** 这是一个流行的在线平台，开发者可以在这里免费获得**的**编码训练营教育。

这实际上很有趣，因为当时我对此一无所知。

尽管如此，我还是在 **Hacktoberfest** 期间对 **markdowm** 中 **FreeCodeCamp** 的**指南**进行了修改。

当时我正在阅读昆西·拉尔森(Quincy Larson)在**开发者社区**上的第一篇文章，昆西·拉尔森是**自由代码营**的**创始人**。

这说明了为什么他们将网站迁移为静态网站。

我认为任何对 JAM stack 感兴趣的人都应该看看他的文章，进一步了解他们为什么把网站迁移到 JAM stack。

如果你喜欢我的文章，要么**注册**max[冒险家简讯](http://eepurl.com/dOUoUb)或者你可以**关注**获取我在 **Dev** 上文章的**最新**更新

这篇文章最初发表在 max 的博客[为什么是 JAM Stack？](https://www.maxongzb.com/why-jam-stack-read-time-4-mins/)和[米盖尔·布鲁纳在 Unsplash 上的照片](https://unsplash.com/photos/2f-8PGW45rI)

## 参考文献

*   [卡纸堆叠](https://jamstack.org/)
*   [把我的职业生涯押在 JAM Stack 上](https://dev.to/lvrbrtsn/betting-my-career-on-jamstack--4def)
*   [freeCodeCamp.org 如何使用 JAMstack +单个 API 服务器来帮助每月数百万人学习编码](https://dev.to/ossia/how-freecodecamporg-uses-the-jamstack--a-single-api-server-to-help-millions-of-people-learn-to-code-every-month-4d5g)
*   [静态站点 vs CMS](https://developer.okta.com/blog/2018/06/07/static-sites-vs-cms)
*   [盖茨比](https://www.gatsbyjs.org/)
*   [心满意足](https://www.contentful.com/)
*   [Netlify](https://www.netlify.com/)