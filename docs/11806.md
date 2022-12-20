# JAMstack 和 CD 管道:用 Nuxt、Netlify CMS 和 Netlify 创建一个博客

> 原文：<https://dev.to/israelmuca/jamstack-and-cd-pipelines-create-a-blog-with-nuxt-netlify-cms-and-netlify-2mml>

*本文最初发表于[Israel muca . dev](https://israelmuca.dev/blog/jamstack-and-cd-pipelines-create-a-blog-with-nuxt-netlify-cms-and-netlify/)T3】*

## 采用“旧”的方式

假设你想创建一个博客，你可以很容易地运行一个 Wordpress 站点，用一个 Linux 服务器分发你的内容。在每次页面加载时，服务器会生成网页，并将它们提供给用户。每个请求都会让服务器工作。
这是一个完全可行的选择，尽管与更现代的选择相比速度较慢，更不安全，因为您必须在您的服务器上进行更新(或者为托管服务器支付更多费用)，并且它需要使用 PHP 并运行数据库。

## JAMstack 来救援了！

那么什么是 JAMstack 呢？代表 **J** avaScript、 **A** PIs、 **M** arkup。一种编写网页的现代方式，在部署前编译成静态资产。JavaScript 处理页面中的动态变化，也就是服务器端的进程，如果需要的话，现在被抽象成 API，并且你的标记在部署期间被编译。请继续在官方网页上阅读所有相关内容。

这种方法的好处是，你的网页最终会非常快，所有内容都是预编译的，每次提供页面时都不必获取任何内容，并且你可以在更多的地方托管它(而且它更便宜(甚至免费，使用 [Netlify](https://netlify.com) ),因为它只是 HTML 和 JS 文件，同时提高了页面的 SEO)。
你的页面也更安全了！因为你没有公开的 API，你甚至可以把 Wordpress 作为 CMS 使用，但是没有运行 Wordpress 本身的安全风险或性能问题。
性能绝对值得一提，因为让你的网站超快是相当容易的。

不过有一点需要注意。每次你改变什么，你必须重建你的资产，并部署它们...啊...但是等等！，因为这就是 CD 管道发挥作用的地方！

## CD 管道...？

您可能听说过 CI/CD 这两个术语，CD 有时指的是持续交付或持续部署。当争论这些词的确切含义时，争论变得相当长，正如你在[这个堆栈溢出](https://stackoverflow.com/questions/28608015/continuous-integration-vs-continuous-delivery-vs-continuous-deployment)问题中看到的。

在我的情况下，我谈论的是持续部署，正如 Netlify [在这里](https://www.netlify.com/docs/continuous-deployment/)所解释的。

> 连续部署的工作原理是将 Git 存储库连接到 Netlify 站点，并保持两者同步。

很简单。我们希望我们的回购与我们的网站同步。

### 但如何？

Netlify 是一个非常棒的 PaaS，它有很多特性，这里仅举几个例子:

*   通过 webhooks 直接从您的 Git 存储库自动构建和部署。
*   回滚，因为以前的部署不会被删除。
*   持续集成测试。
*   来自拉取请求的部署预览。
*   免费 SSL 证书。
*   一个 CDN。
*   还有 S O，M U C H，M O R E。

请查看它们的[功能](https://netlify.com/features/)或直接进入[文档](https://www.netlify.com/docs/)查看。因为我提到的一切都包含在他们非常慷慨的免费等级中。
所以对于我们的用例，我们将使用他们的自动化构建和部署(就像我一样)。

## Nuxt.js + Netlify，天作之合

在我的例子中，我一直在使用 [Vue.js](https://vuejs.org) 并且我想用 Vue 来创建我的博客，所以为此，我使用了 [Nuxt.js](https://nuxtjs.org) ，这是一个建立在 Vue 之上的框架，它提供了服务器端渲染(你需要一个运行的 Nuxt 实例，因此也是一个服务器，这对于我们的项目来说是不可行的),并且还提供了静态站点生成器，其中**将你所有花哨的 Vue 代码编译成普通的 HTML 和 js。这正是我们网络生活所需要的！哦耶！。**

每次你把你的代码推送到 repo，Netlify 都会拉最新的版本，在我的例子中，运行`nuxt generate`，然后把`/dist`文件夹上传到他们的 CDN。就是这样！您的最新版本将在一两分钟后上线。无需手动上传任何东西或检查上传的版本...现在一切都是自动化的了...持续部署 FTW！

## Netlify CMS

最后但同样重要的是。你怎么能这样管理你的内容呢？
记住, [JAMstack 最佳实践](https://jamstack.org/best-practices/)提到，理想情况下，所有东西都存在于你的 git 上，所以任何人只需点击几下鼠标，就可以开始为你的项目做贡献。

这就是 Netlify 的 CMS(T2)的用武之地。这是一款开源软件，在某种意义上，它可以帮助你将 Github 用作“数据库”。允许您将博客文章直接创建到`.md`文件中，您可以将其推入 repo 以触发构建，就像这样创建新的博客文章。
他们有一个易于使用的界面，甚至有一个编辑工作流程，将文章设置为在 Github 上部署的 Pull 请求，允许您或您的团队在合并它们之前查看它们(使用 Netlify)。

总而言之，这些都是惊人的工具，让我们在现代环境中创建速度极快的网站，并且对于这样一个惊人的最终产品只需要很少的配置。

## 入门

为此，我们只需要:

*   Github 帐户
*   净值账户
*   Vue/Nuxt 理解

## 叉回购

使用你的 Github 账户，点击[这里](https://github.com/israelmuca/israelmuca.dev/fork)进行回购或者[克隆它](https://github.com/israelmuca/israelmuca.dev)。

## 构建设置

安装所有依赖项

```
npm i 
```

现在，您可以使用:
运行开发中的代码

```
npm run dev 
```

这将为您在`localhost:3000`中的页面提供热重新加载。

一旦您准备好为生产生成静态资产:

```
npm run generate 
```

这将创建一个包含资产的`/dist`文件夹。此文件夹未提交。
这是 Netlify 将在他们的服务器上构建的，然后将结果上传到他们的 CDN。*我们再也不需要手动这么做了。我们这样做只是为了观察网络生活中发生的事情。*

## 配置您的*新*回购

### Netlify CMS

您需要为您的内容定义字段和文件。我使用的默认值与您在这里看到的非常相似， [dev.to](https://dev.to) 。主要包括用于 SEO 目的的标题、描述和关键字，也有一点帖子的摘要以及在博文页面的 ArticleCard.vue 和 Header.vue 中使用的主图片。

您可以在`static/admin/config.yml`中配置这些字段，并随时查看 [Netlify CMS 文档](https://www.netlifycms.org/docs/configuration-options/)进行额外的定制。

当您的本地服务器正在运行时，您可以访问 [localhost:3000/admin/](https://dev.tolocalhost:3000/admin/) 来修改您的博客内容。您也可以在它部署后进行，只要您有提供给您的网络地址。

### 路线

在这里，Nuxt 和 Nuxtdown 一起工作来创建网站中的路线。

在`pages`目录中，您会发现一个`index.vue`，顾名思义，它是我们站点的索引。
T3 这是由 Nuxt 处理的。

该目录将作为我们的网站以及(israelmuca.dev/blog/.的目录。在我的例子中)，它将保存所有使用`_blogpost.vue`模板创建的博客文章。对于`content/blog/`目录中的每个`.md`，它将被编译一次。您可以看到内容是如何从那个`.vue`文件中获取的。
**这是由 Nuxtdown** 处理的。

您可以在`nuxtdown.config.js`中配置 Nuxtdown，请继续查看 [Nuxtdown 文档](https://github.com/joostdecock/nuxtdown-module/blob/master/README.md)以进一步了解正在发生的事情或更改功能。

### 其余地点

该网站的其余部分是一个普通的 Vue + Nuxt 应用程序，我试图在我认为最需要的地方注释代码，你可以在:`nuxt.config.js`中配置 Nuxt，另外，像往常一样，如果你有任何问题，请查看 [Vue.js](https://vuejs.org/v2/guide/) 和 [Nuxt.js](https://nuxtjs.org/guide/) 文档以进一步理解这段代码。

## 祝贺你

至此，您应该有了一个功能齐全的 Nuxt 生成的、Netlify 托管的博客站点。

## 问题

如果你在实现上有任何问题，请不要犹豫，创建一个 Github 问题或者给我发一个 [tweet](https://twitter.com/IsraelMuCa) 。

## 感谢

有一句话我很喜欢，并一直努力记住:

> 如果说我看得更远，那是因为我站在巨人的肩膀上
> 艾萨克·牛顿

忠于这句话，我不得不感谢许多人以这样或那样的方式促成了这次特别的回购:

*   Andrea Gomez 和她的设计
*   vista . js
*   [Nuxt.js](https://nuxtjs.org/)
*   [Netlify CMS](https://www.netlifycms.org/)
*   [Nuxtdown](https://www.npmjs.com/package/nuxtdown)
*   [布尔玛](https://www.bulma.io)
*   [棱镜](https://prismjs.com/)
*   [renestalder 的模板](https://github.com/renestalder/nuxt-netlify-cms-starter-template)
*   [Netlify](https://www.netlify.com/)

再次感谢，并尽快阅读！