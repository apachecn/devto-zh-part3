# 我如何将我的博客转移到林业 CMS

> 原文：<https://dev.to/lauragift21/how-i-moved-my-blog-to-forestry-cms-1bb>

这里有一个简短的免责声明。我使用最适合我的方式，还有其他几种方式来为你的博客写内容。对我来说，重要的是如何更快地发布内容，我发现走这条路可以帮助我加快速度。这是对我有用的，你可以试一试，看看它是否也适合你的需要。

在寻找更好的方式为我的博客创作内容后，我意识到了这一点。我做了一点研究，想找到更好的方法来实现这个目标。在成功迁移到使用 [Forestry](https://forestry.io) 之后，我想为什么不写一篇关于它的博客，这正是我正在做的。

**我为博客创作内容的常规程序通常是-**

*   我有一个关于[概念](https://www.notion.so)的委员会，负责管理博客帖子的想法，我选择了一个我想开始做的。
*   开始用我常用的减价格式写文章(这通常是一件痛苦的事，因为我必须确保我遵循了我的博客所要求的风格指南)
*   然后我将整篇文章复制并粘贴到[的](https://grammarly.com)上，检查可能的拼写错误。
*   然后运行最终副本的预览，以确保一切看起来都很棒，很准确。

现在我已经做了很多，所以我已经习惯了，直到我找到一个更好的方法，我将告诉你。我一直想迁移到使用 CMS，但没有时间或跳过我的时间表，我终于得到了它，并发现它并不像我想象的那么难。

## 迁徙到林业

[*forestry . io*](https://forestry.io)_ 是一个 Git 支持的 CMS(内容管理系统)，用于使用静态站点生成器构建的网站和网络产品。Forestry 帮助开发人员将基于内容的系统无缝地管理到他们的网站中，同时还有与团队合作的好处。

[![forestry](img/abef6fdb895a6e10017e2d7634744e52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IjZUAHw_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547728143/gift-egwuenu/Screen%2520Shot%25202019-01-17%2520at%25201.18.47%2520PM.png)

为了让我们能够互动，我会检查一下设置，我在我的博客中使用了一些步骤来达到最终的效果。

#### 1。创建一个帐户并设置您的网站

你必须[创建一个林业账户](https://app.forestry.io/signup)才能开始。你可以用你的邮箱注册，也可以使用我推荐的第三方 GitHub。

[![img](img/e23b13002a86ae568e18eccf287c3481.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pmLEvgA1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547825330/gift-egwuenu/Screen%2520Shot%25202019-01-18%2520at%25204.28.21%2520PM.png)

报名后。我们将被重定向到仪表板页面，在这里我们将设置网站。点击新网站按钮，并选择雨果。Hugo 是一个静态站点生成器，我的博客就是用它来构建的。

[![img](img/a7c3304d8f471d31aa46c51552916d5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AGFJ3Sq5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547728339/gift-egwuenu/Screen%2520Shot%25202019-01-17%2520at%25201.30.54%2520PM.png)

现在下一步是连接到我的博客的 GitHub 库。记得选择正确的回购和分支机构，您想要的内容提交。

[![img](img/0ba9a7c40a3bdcddccff2a3a61f10bc1.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--6HR1cpSv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547728345/gift-egwuenu/Screen%2520Shot%25202019-01-17%2520at%25201.31.04%2520PM.png) [ ![img](img/6bae39ffd044d317c1937df1a3911562.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---UVaEG1U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547825760/gift-egwuenu/Screen%2520Shot%25202019-01-18%2520at%25204.34.47%2520PM.png)

现在，您已经成功创建了一个站点，现在您可以导航到显示所有已创建站点的仪表板。

[![img](img/4ee5f1d5b47c376cec0408e822a5678b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MEoa1nm---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547728334/gift-egwuenu/Screen%2520Shot%25202019-01-17%2520at%25201.30.47%2520PM.png)

#### 2。设置您的站点 CMS

有趣的部分来了。有几个选项可以无缝地创建林业内容，您必须自己设置。我是这样做的。

*   **前沿问题:**这是所有博客文章的基础，它需要你设置好，这样你创建的每个新文章都会自动生成。我以前有我的前面的事在下面用`yaml`格式展示。

```
 ---
  author: "Gift Egwuenu"
  date: 2019-01-16
  linktitle: eleventy-101
  title: Getting Started with Eleventy
  description: The era of static sites generators is ever growing due to the popularity of JamStack on the web. We'll learn how to get started with 11ty, a static site generator and explore its use cases.
  images: ['https://res.cloudinary.com/lauragift/image/upload/v1547576269/gift-egwuenu/Screen%20Shot%202019-01-15%20at%207.13.40%20PM.png']
  keywords:
 - staticsites
  weight: 10
 --- 
```

Enter fullscreen mode Exit fullscreen mode

但是对于林业来说，你只需要按照你想要的方式建立起来，其他的步骤就会自动完成。

[![img](img/2e2fbb35e9f68f01028a6a0cd6ebdd8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lHoD-Ln7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547826602/gift-egwuenu/Screen%2520Shot%25202019-01-18%2520at%25204.45.53%2520PM.png)

*   **媒体:**为帖子创建内容时，图片非常重要，林业部门已经为您解决了这一问题，您只需上传图片并在内容中使用即可。我还喜欢这样一个事实，你可以通过简单的设置集成 [Cloudinary](http://cloudinary.com) 来处理你所有的图像。唯一需要的步骤是将您的 cloudinary 帐户连接到您网站的设置。

[![img](img/0f63a897fc7ab7d92a2d10f04e85afb7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iM9WpFWq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547827170/gift-egwuenu/Screen%2520Shot%25202019-01-18%2520at%25204.46.46%2520PM.png)

#### 3。部署

我已经为我的帖子的部署设置了 Netlify，我用我的 GitHub Repo 连接了它。我只需要在 forestry settings 中指定 build 命令，这样当我发布一篇文章时，它就会运行 build 命令，这会触发文章从草稿变为已发布。有了这个，我只需要等几分钟就可以在我的博客上发布帖子了。

[![img](img/0d06ceb71e3d0ece6dc7c92f7429a63a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yQdpIyVp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547826554/gift-egwuenu/Screen%2520Shot%25202019-01-18%2520at%25204.46.31%2520PM.png)

[![img](img/b409af31c44d6b2b02c1126958a65fd5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5V85C6Cr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547827582/gift-egwuenu/Screen%2520Shot%25202019-01-18%2520at%25204.36.39%2520PM.png)

## 结论

自从我迁移到使用这个工作流程，我发现它改善了我的写作方式，因为一切都是如何构建的，我只需要拉几根绳子就可以发布一篇文章，这完全值得一走。

让我知道这篇文章是否有帮助，如果你目前正在使用 CMS，也请告诉我你的经历。

[最初发表在我的博客上](https://www.giftegwuenu.com/how-i-moved-my-blog-to-forestry-cms/)