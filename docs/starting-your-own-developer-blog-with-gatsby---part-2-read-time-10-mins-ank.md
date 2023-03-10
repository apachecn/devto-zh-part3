# 用 Gatsby 开始你自己的开发者博客-第 2 部分(阅读时间:10 分钟)

> 原文：<https://dev.to/steelwolf180/starting-your-own-developer-blog-with-gatsby---part-2-read-time-10-mins-ank>

[![Photo by Debby Hudson on Unsplash](img/3e0660a36fd3d18ed6ea8b2e1a9d3572.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ifDwxUAG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6xxalndla7rc54z8qswz.jpg)

## 简介

在上一篇文章中，我谈到了开始写博客的各个步骤，从为什么你应该开始留出时间来写博客。

如果你偶然发现了这篇文章，去[第一部分](https://www.maxongzb.com/starting-your-own-developer-blog-with-gatsby-part-1-reading-time-4-mins/)开始创建一个博客。

在这个系列的第二部分，我将讲述从我如何在 Netlify 上发表文章到主持你的博客的工作流程。

## 开发者博客工作流程

[![Developer Blog Workflow](img/a07de9f7455e3712645bb4c3b50d3994.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C3F5nxq8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tlevhr36dibrri2adcqj.png)

这是我目前发布每周博客的工作流程。当帮助你开始时，你可以修改它来适应你的需要。

### 初稿，校对符合语法&心满意足

首先，我将在**上写内容，这是免费的，因此它将提供第一次校对。当我专注于写博客时，检查我的语法和拼写错误。**

一旦我对我的博文感到满意，在我的初稿编辑阶段。

我将把我的内容复制到 **Contentful** 中，根据之前保存的博客帖子模板添加降价和照片。

### 一张图片在你博客中的重要性

基于来自**自由代码营**的**昆西·拉森**的提示，该自由代码营在**媒体**上拥有最大的开发者焦点出版物之一。

吸引潜在读者阅读你的博客文章的关键之一是在你的博客文章中有一个高质量的封面图片和每 300 字的 T2 图片。

有了高质量的封面图片，一个人会停下来点击你的博客文章，因为它看起来很有趣。

使用漂亮的封面图片和标题，特别是如果你在和一堆让人分心的东西竞争，比如 T2 的推特、T4 的 Linkedin、Instagram、T7 或脸书的 T9，让你的博客脱颖而出。

我在 **Unsplash** 上为我的博客文章提供封面图片和其他相关图片，因为它提供了大量免费的库存照片供你在博客文章中使用。

### 通过网络发布你的博客文章

一旦你在 **Contentful** 上发布了你的博客文章， **Netlify** 将**自动**启动一个 webhook，从 **GitLab** 中获取你的博客源代码。

使用**盖茨比** & **GraphQL** 开始生成 HTML、Javascript 和 CSS 静态文件的过程。

GraphQL 端点是通过将您的内容从 **Contentful** for Gatsby 拉到 consumes 来生成静态文件而创建的。

## 选择您的博客模板

我会从选择一个现有的 **Gatsby Starter** 开始，而不是从头开始构建。

原因是开始为你的开发者博客创造内容比花时间从零开始建立你的开发者博客要重要得多，前者比后者重要得多。

这就是为什么 **Wix** 、 **WordPress** 或 **Squarespace** 在博客新手中很受欢迎。

### 新手博客的要求

有很多 Gatsby Starter 网站，所以我将列出一个开发者博客必须具备的最基本的功能。

*   搜索引擎优化——通过搜索引擎为你的博客增加流量
*   **标签** -用标签来组织你的博客文章，以方便阅读
*   **每篇博客文章的封面图片** -增加你的博客文章被阅读的可能性
*   **语法突出显示**——这样你就可以在博客文章中添加易于语法突出显示的代码块。
*   谷歌分析(Google Analytics)——这对于衡量你博客的访客量非常重要。
*   **RSS Feed** -供你的任何访问者订阅你的博客
*   像 WordPress 一样管理你的博客的无头 CMS

在浏览了这些入门博客的网站演示后，我发现 ryanwiemer 拥有我开始学习所需的所有东西。

除此之外，ryanwiemer 提供了文档让我开始使用 contentful，因此本教程将使用他的博客。

## 账户创建服务

基于上面的工作流程，你需要创建以下帐户来托管你的博客，它们如下。

*   [GitLabs](https://gitlab.com/users/sign_in) -将你博客的源代码存放在一个存储库中
*   [Netlify](https://app.netlify.com/signup) -启用 SSL 和 DNS 来托管您的博客
*   [Contentful](https://www.contentful.com/sign-up/) -管理您的博客并在其中存储您的内容

## 本地机器中的项目设置

开始使用 [ryanwiemer](https://github.com/ryanwiemer/gatsby-starter-gcn) 项目。输入以下命令创建一个新的 Gatsby 项目。

### NPM 安装

```
git clone https://github.com/ryanwiemer/gatsby-starter-gcn.git
npm i 
```

Enter fullscreen mode Exit fullscreen mode

### 盖茨比 CLI 安装

```
gatsby new gatsby-starter-gcn https://github.com/ryanwiemer/gatsby-starter-gcn.git 
```

Enter fullscreen mode Exit fullscreen mode

## 满足设置

### 获取空间 ID 和访问键

使用**内容**中的**一个空格**，点击**设置**下的 **API 键**。

[![Menu to access API Keys](img/3aba36bf0224a3c977fab90f5c380a41.png)](//images.ctfassets.net/ly2f59p4unnn/1mqYgsNLIQsYMISSM4Ek2e/954661f185a4935a271f4cf96dd8be3b/menu_to_access_API_Keys.png)

在 **API 键**中，点击一个名为**的 API 键，例键 1** 。在 API 密钥细节中，复制**空间 ID** 和**内容交付 API -访问令牌**，如下图所示。

[![API Key Details](img/f8b7e2f40c04597fb67dc9dfe4acd962.png)](//images.ctfassets.net/ly2f59p4unnn/9wjEp4eq3uCI0CoQcYKew/45d0f014b848729a8bdf1feefde3f93b/API_Key_Details.png)

### 对你的盖茨比项目满意吗

现在有了**空间 ID** 和**内容交付 API -访问令牌**。

转到根目录，输入下面的命令，将 Contentful API 连接设置为 GraphQL 端点。

```
npm run setup
gatsby develop 
```

Enter fullscreen mode Exit fullscreen mode

## 从 Git 进行网络部署

通过 Netlify，它将使用存储库的* * master * *分支从 Gitlab 提供项目的自动重新部署。

每当你的项目有变更，无论是你的**内容**还是 **Gitlab** 项目变更。

### 将 GitLab 资源库导入 Netlify

要让它在 Netlify 上工作，您需要连接 **GitLab** 并按照下面的步骤导入包含您想要托管的 Gatsby 项目的存储库。

导航到**部署**并选择**触发部署**从资源库的**主**分支在 Netlify 上手动部署和发布您的网站。

### 在 Contentful 上添加 Webhook

要启用 webhook，请访问内容丰富的网站。然后进入**设置**并点击**网页挂钩**。

一旦重定向到 webhooks，点击**添加 Webhook** 选择**选择特定触发事件** Netlify 重建整个项目。

[![Netlify Webhook](img/7d90ea56cfb1125569f05e47e4d36405.png)](//images.ctfassets.net/ly2f59p4unnn/4qZ6dourLqISi6wiwOSqAa/7d67dc8ea144d0cd8cfae15ea420947d/Netlify_Webhook.png)

## 向开发者博客添加自定义域

默认情况下，Netlify 将为您提供一个随机生成的域，您可以使用它来满足基本需求。

为了利用 Netlify 的免费 DNS 和 HTTP 服务，你需要提供一个你之前带来的 T2 自定义域名。

要在 Netlify 上添加自定义域，请在托管您的 Gatsby 项目的网站中选择**设置**，然后导航到名为**域管理**的选项卡，单击**添加自定义域**按钮。

Netlify 提供了一步一步的指南来添加您带来的自定义域。域名**激活**大约需要 **24 小时**才能激活。

## 结论

我希望您已经用这个 2 部分系列发布了您的开发者博客。如果你在开通博客时遇到任何问题，请务必在**[【steelwolf180@gmail.com】](mailto:steelwolf180@gmail.com)**给我发电子邮件。

## 参考文献&链接

*   [在博客文章中加入图片的 11 个最佳实践](https://neilpatel.com/blog/blog-post-image-guide/)
*   如何撰写技术博客文章——昆西·拉森的演讲
*   [去飞溅](https://unsplash.com/)
*   ryanwiemer 的《盖茨比-首发-gcn》
*   [语法](https://www.grammarly.com/)
*   [软件开发人员博客](https://simpleprogrammer.com/blogging-software-developers/)

本文的原始资料来源于[用 Gatsby 开始你自己的开发者博客-第 2 部分(阅读时间:10 分钟)](https://www.maxongzb.com/starting-your-own-developer-blog-with-gatsby-part-2-read-time-10-mins/)