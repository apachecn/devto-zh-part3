# 介绍内容

> 原文：<https://dev.to/sergiodxa/introducing-contentz-e7p>

几周前[我用西班牙语写了一篇文章](https://sergiodxa.com/articles/generador-sitios-estaticos-propio/)，描述了我对理想静态站点生成器的期望，今天我将展示 [**Contentz**](https://contentz.tech) 。我称之为纯静态站点生成器，因为它只加载了几行 JavaScript。

*最初发表于[https://sergiodxa.com/articles/introducing-contentz/](https://sergiodxa.com/articles/introducing-contentz/)T3】*

## 动机

我最初是使用带有静态导出特性的 [Next.js](https://nextjs.org) 来创建我的网站和博客的，但是在每次部署时都要花费几分钟的时间，并且每次都需要我生成每篇文章的 js 包。

我也试过盖茨比，基本模板花的时间甚至比我的 Next.js 网站还要多！因为我不想等那么久，所以我决定迁移，像 Jekyll 或 Hugo 这样的快速选项不支持我网站上已经有的一些功能，比如为离线第一缓存生成一个服务工作者，创建插件来扩展它们需要我使用 Ruby 或 go，因为我是前端，所以我不经常使用这些语言。

这就是为什么我创建了 Contentz，它支持 MDX，但只有服务器端，它导入和使用组件来呈现 HTML，它从不为个人页面生成 JS 包，这使得构建过程比其他现代 SSG 更快。

## 开始使用它

要开始使用 Contentz，您可以手动安装它，或者让它初始化您的网站并自动安装。

```
$ npx contentz init my-blog 
```

Enter fullscreen mode Exit fullscreen mode

安装后，您将拥有一个名为`config.yml`的小配置文件，其中包含以下内容。

```
---
title: my-blog
description: Just another Contentz site
language: en
incremental: false # Change to `true` if your server support keeping `.cache` and `public` folders 
```

Enter fullscreen mode Exit fullscreen mode

在这个文件中，你可以开始配置更多的东西，比如使用`domain: https://contentz.tech`(RSS 提要所需要的)来配置你的网站域名。你也会有一个`pages`和`articles`文件夹。

Contentz 同时支持定制页面和博客文章。要创建新的博客文章，您可以使用内置命令`write`。

```
$ contentz write my-first-article.mdx 
```

Enter fullscreen mode Exit fullscreen mode

这将在`articles`中创建一个文件`my-first-article.mdx`。如果你配置了环境变量`EDITOR`，它会尝试自动打开它。内容永远是:

```
---
title: Just Another Contentz Article
date: 2019-04-22T22:27:46.764Z
published: false # Change to `true` to list it
---

Here be dragons 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们把`published`改成`true`，建立网站。

```
$ contentz build 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个`public`目录，其中包含您生成的整个网站。你现在可以把它上传到任何 HTTP 服务器上，比如 GitHub Pages，Netlify，ZEIT Now 等等。

## 增量构建

Contentz 的核心特性之一是对**增量构建**的支持。默认情况下，如果您运行`contentz build`，它将只生成已更改的文件或与那些已更改的文件相关的文件。

这意味着，如果你只改变你博客的一篇文章，它不会重新构建整个博客，它只会为这样的文章和文章列表这样做。

这可以通过在`config.yml`内设置`incremental: false`来禁用。如果您不能控制在部署之间保留什么文件，并且不能保留`.cache`和`public`目录，这可能是有用的。

## 先下线

Contentz 生成一个服务工作器，该服务工作器被配置为自动缓存对同一站点的任何 HTTP 请求，并首先从网络，然后从缓存为其提供服务。这意味着在用户访问网站的一个页面后，它将被自动缓存，如果用户失去互联网连接并试图再次访问，它将从缓存中加载，但当用户访问互联网时，它将继续用最新的内容更新缓存。

## 打开图形生成器

为每一页或每一篇文章创建一个开放图表是一项枯燥的任务。甚至像 ZEIT 这样的公司也实现了自动化！Contentz 实际上有一个开放的图表或社会形象，因为它也作为 Twitter 卡、生成器工作。

要使用它，运行以下命令:

```
$ contentz social articles/my-first-article.mdx home 
```

Enter fullscreen mode Exit fullscreen mode

现在 Contentz 将为你的文章和主页生成社会形象。这些文件将存储在`/static/_social`文件夹中，您应该将它们添加到 Git 中，以避免在每次部署时都生成它们。

## 最后的话

你甚至可以在文档中读到更多功能[，如幻灯片支持、分析、i18n、Patreon 支持等等，如果你想要一些功能](https://contentz.tech/docs/)[问题](https://github.com/sergiodxa/contentz/issues)和/或[拉请求](https://github.com/sergiodxa/contentz/pulls)是受欢迎的。

如果你开始在自己的网站上使用它，请在我的推特上告诉我！