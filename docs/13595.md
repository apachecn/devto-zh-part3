# [Intro]使用 Vuepress、Tailwind 和 Netlify 创建博客

> 原文：<https://dev.to/hyper_yolo/intro-build-a-blog-with-vuepress-tailwind-and-netlify-3jlp>

最近，我推出了一个新版本的个人网站，它在幕后使用了 [Vuepress](https://vuepress.vuejs.org/) 。作为一个新的 Vue.js 采纳者，我认为 vue press——一个建立在 vue.js 基础上的静态站点生成器——是网站的自然选择:它直观、高性能且灵活，支持降价。

这个项目还很年轻(在撰写本文时是 v.1x-alpha 版)，所以除了默认设置之外，还没有太多的资源。我希望这个 3 部分的演练可以帮助那些正在考虑将 Vuepress 用于个人网站或博客的人。

## 我们要做什么

我们将创建一个自定义 Vuepress 主题的博客。您还将学习如何应用一些基本的 Vue.js-fu，合并像 tailwind.css 这样的功能性 css 库，并最终将其部署到 netlify。

*   [第 1 部分:用 Tailwind.css 建立一个有效的 Vuepress 博客](https://dev.to/hyper_yolo/part-1-setup-a-working-vuepress-blog-with-tailwindcss-1594)
*   第 2 部分:编写并应用自定义的 Vuepress 主题。
*   第 3 部分:最终总结并部署到网络生活。

## v 表示静态站点发生器

Vuepress 旨在生成文档，但也适用于个人网站和博客。它带有一个默认的“隐藏”响应主题，并推荐一个特定的文件结构。不过，你当然可以自己卷。

### v0 v.s v1

vuepress v.0 和 v.1-alpha 有很大的区别。我喜欢的几个主要更新是:

1.  降价销售机制。
2.  [访问全局计算属性。](https://vuepress.vuejs.org/guide/global-computed.html#global-computed)
3.  [插件的使用。](https://vuepress.vuejs.org/plugin/)

在整个系列中，我们将使用最新的 v.1x-alpha 。如果你有兴趣知道 v.1 增加了什么，在这里阅读更多关于[的变更日志](https://github.com/vuejs/vuepress/blob/master/CHANGELOG.md)。

## 当你准备好了，让我们从[开始第一部分:用 Tailwind.css 建立一个有效的 Vuepress 博客](https://dev.to/hyper_yolo/part-1-setup-a-working-vuepress-blog-with-tailwindcss-1594) →

这是一个来自我的网站的交叉帖子。查看原文和更多内容！