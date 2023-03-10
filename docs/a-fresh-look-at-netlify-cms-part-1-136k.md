# 重新审视网络生命周期管理系统(第一部分)

> 原文：<https://dev.to/remotesynth/a-fresh-look-at-netlify-cms-part-1-136k>

当我为已经出版两年的 [O'Reilly static sites book](http://shop.oreilly.com/product/0636920051879.do) 写“添加 CMS”这一章时，我有机会检查了一个名为 [Netlify CMS](https://www.netlifycms.org/) 的全新项目。它太新了，以至于当我回顾它来写这一章时，它甚至还没有公开。

所以，当我最近有机会检查这个项目时，我并不感到惊讶。然而，我对变化如此之大感到惊讶。例如，我花了这本书的大约五页来浏览您现在只需点击一下按钮就可以完成的配置。因此，在这篇文章中，我们来看看什么是 Netlify CMS，以及如何开始使用模板。本系列的后续文章将着眼于在现有站点上设置这一功能，以及高级设置的各种配置选项。

## 什么是 Netlify CMS

Netlify CMS 是为静态网站设计的内容管理工具。它是由[网络公司](https://www.netlify.com/)创建的，但是不要求你以任何方式使用他们的服务。Netlify CMS 强大的一个原因是它可以和你选择的任何静态站点生成器一起工作——不管是 Jekyll、Hugo、Hexo 还是其他什么。

一旦添加到您的站点并正确配置，Netlify CMS 提供了一个简单的用户界面来添加和编辑内容，对于习惯使用类似 Wordpress 的 WYSIWYG 界面的用户来说，这个界面会更加熟悉。

例如，下面显示了一个非常简单的“关于我们”页面的编辑和预览，该页面的“主要内容”元数据非常有限。

[![netlify cms admin wysiwyg](img/f3a0567c82c4e821be7a659351944293.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bmq5x1kK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pok62o27mvforphig2ml.png)

## 在新站点上设置 Netlify CMS

设置全新 Netlify CMS 安装的最简单方法是使用在[netlifycms.org](https://www.netlifycms.org/docs/start-with-a-template/)上预定义的模板之一，并使用在 Netlify 上部署按钮。你可以选择 Hugo，Gatsby 或者中间人作为你选择的静态站点引擎。

下面的实时记录显示了这将如何快速地建立一个网站的基础。

[![setting up a new netlify cms site](img/05874c0d2e3d0e2109ec977a42dda78a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TyFaIzrJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mb6cu9658d3uyjsdr9km.gif)

在部署过程中，模板将自动向 Netlify 帐户的所有者发送邀请。

[![invite](img/58d2d6cd24bc7633906219d042eb2cc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ELqquK5e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0padq9cziusd8noi1tew.png)

单击“接受邀请”,您将被发送到新网站，并被提示创建密码。

[![creating a password](img/bccaf92b65cbbc270f4072f12bf843da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YqE5u7Mu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xj5xno6uqfh6ejmrb8qp.png)

一旦你这样做了，你就会进入管理员界面。

[![netlify cms admin main page](img/9cba0c8e8c19e9835cbe870f777fdcd5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1ieYXFPh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2zojouhbhrwvaye1at3x.png)

所有这些工作都是因为模板被配置为使用 [Netlify 身份服务](https://www.netlify.com/docs/identity/)进行身份验证。如果你想添加更多的用户，你可以进入你的 Netlify 帐户管理，点击顶部的“身份”标签，然后“邀请用户”(免费帐户允许你最多邀请五个用户)。

[![invite new users](img/4f44dddea4c0a2429a61319037b17f4d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E9eWw3BL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rpj54kgnt9zq6ma5d8hh.png)

Netlify 中的身份服务有许多配置选项。例如，您甚至可以将管理员配置为允许通过外部提供商进行身份验证，如 Google 身份验证。只需在您的网络管理员中进入“设置>身份”来配置此服务(有些选项需要付费帐户)。

## 下一步

既然我们已经建立了站点，接下来的步骤应该是这样的:

1.  从 GitHub 存储库中取出一个本地副本。
2.  自定义网站的外观和感觉。
3.  根据需要修改 Netlify CMS 的配置

最后一步需要编辑 Netlify CMS 的`config.yml`配置文件。这总是在 CMS 的`admin`文件夹中，但是的确切位置取决于您使用的引擎。就我而言，我用了雨果，所以它在`/site/static/admin/`。

[![netlify cms configuration file](img/dfd131059a41c382b1ff6b612ac1e88c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vz8DzBLJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fuvc3k0obb96ap2i5hbr.png)

在本系列的后续文章中，我们将更详细地介绍配置以及如何将 Netlify CMS 添加到现有站点中。