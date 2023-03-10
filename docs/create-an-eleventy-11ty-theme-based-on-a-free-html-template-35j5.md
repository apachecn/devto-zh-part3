# 基于免费的 HTML 模板创建一个十一(11ty)主题

> 原文：<https://dev.to/brob/create-an-eleventy-11ty-theme-based-on-a-free-html-template-35j5>

在这个视频系列中，我们将看看如何从谷歌上找到的免费 HTML 模板开始，并将其转换为静态站点生成器(SSG)使用。我们将使用我个人最喜欢的 SSG[11ty](https://11ty.io)。

对于许多在代理机构工作或从事自由职业的人来说，很难从 WordPress 过渡到静态的东西。使用 WordPress 时，主题非常方便。使用静态站点生成器，您仍然可以享受模板和主题的便利。

许多 SSG 都有免费或低价的主题，但是将任何 HTML 主题转换成 SSG 主题并不困难。

## 简介

[https://www.youtube.com/embed/z-o1W9ijUhI](https://www.youtube.com/embed/z-o1W9ijUhI)

## 教程 1:项目结构和初始模板

在本视频中，我们将着手挑选主题和基本的 11ty 设置和配置。在我们将资产和静态 HTML 移动到我们的项目中之后，我们通过`npm install @11ty/eleventy`安装 Eleventy 并创建一个`.eleventy.js`配置文件，做一些简单的修改。

[https://www.youtube.com/embed/h6ZxRudaYIQ](https://www.youtube.com/embed/h6ZxRudaYIQ)

## 教程 2:创建基础模板

在本视频中，我们将着手创建基础模板，所有其他模板都将继承该模板。

这个模板将包含我们的页眉和页脚，以及我们的网页横幅。

我们设置模板，然后使用 front matter 来使用每个 HTML 页面上的布局。
[https://www.youtube.com/embed/iWivBpYmOaQ](https://www.youtube.com/embed/iWivBpYmOaQ)

## 教程 3:创建可重用的简单内容模板

在本视频中，我们将探索如何扩展我们的基本模板，以允许我们将用于“关于”页面的可重用的简单内容模板。

[https://www.youtube.com/embed/iHHxd5L_gIo](https://www.youtube.com/embed/iHHxd5L_gIo)

## 教程 4:服务和证明的 11 个集合(有趣的事情开始了)

这个视频是有趣的开始！我们开始通过创建 11ty 的集合，将静态 HTML 中的数据转换为更加动态的数据。

这些集合允许我们为每个服务和证明创建一个文件，然后在我们的模板中遍历这些文件，从我们的 HTML 创建设计。
[https://www.youtube.com/embed/xzH2XZubgEk](https://www.youtube.com/embed/xzH2XZubgEk)

## 教程 5:用前台素材和收藏创建自定义主页

在本教程中，我们将着手把我们的主页转换成一个自定义模板。对于主页上的一次性数据，我们将使用 front-matter。对于服务和推荐，我们将直接从上一个视频中创建的集合中提取数据。对于一个新的品牌部分，我们将创建另一个集合，并将它们也纳入进来。
[https://www.youtube.com/embed/5MpfJNdPnNs](https://www.youtube.com/embed/5MpfJNdPnNs)

## 联系表单和网络部署

在本教程中，我们将充实联系页面，包括为我们的地图创建一个变量，并设置向 Netlify 提交表单。我们将连接到 GitHub，并让 Netlify 在每次推送 master 时自动部署我们的 11ty 站点。

[https://www.youtube.com/embed/Rw75idHWPi4](https://www.youtube.com/embed/Rw75idHWPi4)

## 即将推出:NetlifyCMS 教程