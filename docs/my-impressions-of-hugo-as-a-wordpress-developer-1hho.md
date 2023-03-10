# 我对 Hugo 作为 WordPress 开发者的印象

> 原文：<https://dev.to/tylerlwsmith/my-impressions-of-hugo-as-a-wordpress-developer-1hho>

两年前，我成为了一名自由网络开发者，在获得一些客户后，我为我的自由 WordPress 开发公司建立了一个网站。我的大部分业务来自于推荐，所以我只花了几个小时构建了一个单页的 HTML 文件，并在`<head>`标签中内嵌了 CSS。

自从我建立我的商业网站以来，我已经完成了几十个项目。我想扩展我的网站来展示它们，但是我永远找不到把它移植到 WordPress 的动机。我害怕所有的小步骤:

1.  设置本地 WordPress 环境。
2.  安装我所有的 WordPress 插件并添加我的许可证。
3.  为自定义内容类型设置插件样板和 ACF 字段。
4.  为所有的 WordPress 模板建立一个匹配我的品牌的自定义主题。
5.  审核所有的 WordPress 设置。
6.  将 Yoast SEO 配置为`noindex`所有自动生成的低价值 WordPress 页面(类别和标签等)。
7.  把所有东西都 FTP 到服务器上(我用的是便宜的共享主机)。
8.  将数据库反弹到服务器。
9.  更新`wp-config.php`文件信息以指向新数据库。
10.  将数据库中所有表的本地 URL 替换为生产 URL。
11.  重新输入我的插件许可证。
12.  手动检查现场，以确保没有任何破坏。

WordPress 是一个不可思议的平台，在过去的两年里，它支付了我的大部分账单。然而，它的配置是单调乏味的，我并不认为我应该为一个可能在一天中获得 5 次点击的网站去经历所有的麻烦。

我希望我的网站有以下内容:

*   主页
*   关于页面
*   投资组合列表页面和单个投资组合项目页面
*   联系页面

## 雨果和静网站发电机

我已经听说**静态站点发生器**一年多了，所以我决定试一试。

静态站点生成器使用模板和内容文件为整个站点生成静态 HTML 页面。用这些生成器构建的网站加载速度非常快，因为当你访问服务器时没有后端数据处理，而且它们非常安全，因为这些网站没有后端。

在比较了几个静态站点生成器之后，我选择了 [Hugo](https://gohugo.io/) ，到第二天，我已经为 [Netlify](https://www.netlify.com/) (一个专注于静态站点的托管服务)部署了一个不起眼的 3 页 Hugo 站点。

即使有 Hugo 和 Netlify 的学习曲线，这个网站花费的时间和用 WordPress 创建的时间差不多。重要的是，我必须跳过上面列出的 12 个步骤。

以下是我作为 WordPress 开发者对 Hugo 的看法。

## 雨果的《顾虑分离》感觉不错

这听起来可能很奇怪，但是很多时候在 WordPress 中工作时，关注点的分离感觉非常的分离。

*   **升华文本**用于编辑代码。
*   **端子**用于编译 Scss。
*   **MAMP PRO** 用于运行服务器。
*   编辑内容、管理媒体库、编辑导航菜单、定义 ACF 中的字段以及编辑站点配置。
*   **Sequel Pro** 用于管理数据库。

管理同一个项目需要大量独立的工具。

有了 Hugo，您可以使用更小的工具集:

*   **升华文本**用于编辑代码、编辑内容、管理媒体、编辑导航菜单、定义字段和编辑站点配置。
*   **终端**，用于运行服务器并使用`hugo server`命令编译 Scss。终端也用于创建新页面。

我喜欢看的东西少一些。这有助于我集中注意力。

## Hugo + Markdown 让编辑内容变得愉悦

先说这个:HTML 是一种写内容的冗长语言。WordPress 使用 WYSIWYG 编辑器来减少 HTML 的冗长。WordPress 编辑器有时会做一些不可靠的事情，当你从另一个来源复制粘贴时，经常会出现不需要的 HTML。

降价解决了这个问题。不需要太多的细节，下面是 markdown 的样子:

```
# Heading 1
## Heading 2

This is a paragraph.

* This is an unordered list.
* Isn't it cool?

1\. This is an ordered list.
2\. This is the second item in an unordered list.

[this is a link](https://www.example.com)
![this is an image, and inside these brackets is the alt text](https://www.example.com/img.gif) 
```

Enter fullscreen mode Exit fullscreen mode

因为它是纯文本，所以您可以放心地在 Google Docs 和您的文本编辑器之间复制和粘贴 markdown:markdown 将正确呈现。

如果你想了解更多关于减价的信息，请查看这张[减价备忘单](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)。

## Hugo 让 URL 结构和媒体管理变得轻松

使用 Hugo，你在`/content/`文件夹中创建你的页面结构，所以如果你有`/content/team.md`，它会在你的网站上显示为`www.example.com/team/`。这比纠结于 [WordPress 重写 API](https://codex.wordpress.org/Rewrite_API) 要容易得多。

您也可以在`/content/team/index.md`存储相同的页面。这在`/content/team/`文件夹中创建了一个叫做**的页面包**，它允许你将页面的所有资源保存在同一个目录中。你可以创建`/content/team/person1.png`，然后通过输入`![Person 1's Face](person1.png)`调用 markdown 中的图像。

与 WordPress 媒体库相比:WordPress 本质上对待你的每一个文件就像它在一个大的文件夹中一样，使得在大的网站上很难找到资产。

## Hugo 的模板系统很容易理解

作为一名 WordPress 开发者，我在我的电脑上将 [WordPress 模板层级图](https://wphierarchy.com/)设置为背景，因为它很复杂，很难记忆。

Hugo 的模板系统要容易理解得多，它只有几种常用的模板。以下是据我所知 Hugo 模板的概要:

*   列表模板(list.html)
*   单一模板(single.html)
*   主页(index.html)
*   404(404.html)

列表模板是列出其他页面的页面(想想`www.example.com/blog/`)。单个模板显示单个资源(`www.example.com/blog/my-blog-post/`)。主页和 404 模板不言自明。

当你为 Hugo 创建一个主题时(顺便说一句:Hugo 也有主题，很像 WordPress)，你的主题中有一个`/layouts/defaults/`文件夹作为模板，*的所有内容*都会默认使用这个文件夹。例如，`/layouts/defaults/single.html`将用于显示单一资源的所有页面(个人博客页面、个人团队成员页面、个人投资组合项目页面等)。

然而，假设您有一个“服务”内容类型(`/content/services/`)，并且您希望单个页面使用不同的模板。您可以创建`/layouts/services/single.html`，它将覆盖默认模板。我发现 Hugo 模板的层次结构比 WordPress 更容易理解。

Hugo 使用了一个名为`baseof.html`的包装文件，你可以在其中为页眉和页脚添加标记，这样你就不必像在 WordPress 中那样手动将它们包含在每个模板中。通过使用适当的文件夹结构(`/layouts/services/baseof.html`)，您还可以为不同的内容类型设置不同的`baseof.html`。和其他模板语言一样，Hugo 也允许你包含片段。

Hugo 不像 Laravel Blade 或 Twig 那样允许多级继承，但 Hugo 仍然非常强大。

## Hugo 让定义自定义字段变得简单

作为一名 WordPress 开发者，高级自定义字段插件是我最好的朋友，但它迫使我在浏览器中花费大量时间点击它的选项。

在 Hugo 中创建的每个 markdown 文件的顶部都有一个名为 **front matter** 的块，它用于指定帖子的元数据。

默认情况下，Hugo 中的 front matter 是这样的:

```
---
title: "Blog writing is fun!"
date: 2019-02-13T21:01:46-08:00
--- 
```

Enter fullscreen mode Exit fullscreen mode

假设我想在这篇文章中添加一个自定义字段。我可以把它加到下一行。

```
---
title: "Blog writing is fun!"
date: 2019-02-13T21:01:46-08:00
newField: "HELLO WORLD!"
--- 
```

Enter fullscreen mode Exit fullscreen mode

实际上就这么简单。

现在，您可能希望确保每个内容类型都有相同的字段集。你可以使用**原型**来做到这一点。一个**原型**是一个预定义的前台事件集，当您在终端中运行`hugo new content/path/to/file.md`来创建一个新页面时，就会填充它。

如果我想在我的`blog`内容类型中(在我的`/content/blog/`文件夹中)为一个作者添加定制的前台内容，我会创建一个名为`/archetypes/blog.md`的新文件，它包含以下内容:

```
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
author: "Tyler Smith"
draft: true
--- 
```

Enter fullscreen mode Exit fullscreen mode

有了这个，每次我从终端创建一个新的博客文章时，它都会填充新的字段。

这种基于 YAML 的前沿方法的缺点是，你得不到 ACF 给你的数据验证。

## 雨果有很多很酷的特点

除了我已经说明的内容，我还想提几个其他功能:

*   Hugo 的服务器会在您保存项目时自动刷新，这意味着浏览器中的内容会准确反映项目的当前状态。
*   Hugo 可以运行您的 Sass 任务，并会在保存时自动刷新页面。
*   Hugo 可以动态地自动生成缩放图像，并且只为您的站点使用的尺寸生成它们。
*   Hugo 内置了对分类法的支持😲
*   Hugo 有数百个免费的第三方主题。
*   你可以覆盖第三方主题的主题文件，就像 WordPress 中的子主题一样。
*   Hugo 会在构建时自动生成一个 sitemap.xml 文件。
*   如果您使用 [Netlify](https://www.netlify.com/) 进行托管，那么当您推送到主 Git 分支的远程时，您可以自动重建和部署您的站点。这意味着您可以始终保持本地和生产环境同步。

用 Hugo 建立我的网站是一次很棒的经历，但也不是一帆风顺的。以下是我遇到的挑战:

## 去模板化对我来说很陌生

Hugo 使用来自 [go 编程语言](https://golang.org/)的 Go 模板。围棋是由谷歌 T3 的超级书呆子发明的[，他们试图创造一种更快的编程语言。当您使用 Hugo 时，您可以看到他们的工作得到了回报:即使是大型网站也几乎可以立即编译。](https://golang.org/doc/faq)

也就是说，Go 模板对我来说真的很奇怪。

在我用过的几乎每一种语言中，对表达式求值看起来都像`if ($a == $b)`。在 Hugo 中，同样的东西会是`if eq $a $b`。

尤其是在测试多个表达式时，这可能看起来很奇怪。下面是我的站点头文件中的一个表达式:

```
{{ if or (and (not .Draft) (not (.Param "hidden"))) (.Site.IsServer) }}
    Do stuff...
{{ end }} 
```

Enter fullscreen mode Exit fullscreen mode

双花括号只是表示 Go 模板代码正在运行。

同样，每个逻辑块都以`end`结束。

作业看起来也怪怪的！看这个:`$a := 22`。

我确信这种奇怪的语法有很好的理由，而且不一定不好:这些只是我作为 WordPress 开发者对 Go templating 的印象。

## 你卡壳的时候 Hugo 的资源比 WordPress 少

Hugo 有很棒的文档，但是它没有涵盖所有内容。文档中使用了大量的 Go 模板，但并没有完全解释清楚。这是合理的:教你一门语言的核心编程概念不是框架的工作。但是如果你像我一样完全不熟悉围棋，你需要做一些额外的工作来跟上速度。

由于它不像 WordPress 那样被广泛使用，当你陷入困境时，谷歌的搜索结果不会很有帮助。当你学习雨果的时候，计划花一些时间去尝试。

## 我不明白 Go 中的范围或上下文

Hugo 中有一个点(看起来像这样:`.`)是所有数据的保管者，它决定了你可以访问哪些数据。

我不太明白它是如何工作的。这绝对是 Hugo 的一个核心概念，但我仍然只是对它有一点感觉。

我找到一篇文章[帮助我理解 Hugo 中的点和作用域是如何工作的](https://regisphilibert.com/blog/2018/02/hugo-the-scope-the-context-and-the-dot/)。如果你在学雨果，而圆点让你困惑，我会从这里开始你的阅读。

## 最后的想法

我爱 Hugo，我可能会在 Hugo 上保留我的自由职业者网站很多年。能够在我的文本编辑器中做这么多是理想的，因为我使用 Netlify 的自动部署，所以我的本地副本上的内容总是与生产服务器上的内容相同。生产服务器也非常快，因为没有服务器端处理来生成页面。

也就是说，我不认为自己会在客户的网站上使用 Hugo:客户使用 Hugo 所需的学习曲线太高了。Netlify 有一个与 Hugo 集成的 CMS，但对于大多数项目来说，我觉得成本/收益没有意义。

如果你是一个 WordPress 开发者，考虑将你的个人网站或博客转移到 Hugo 可能是值得的，因为这将带来我在这篇文章中概述的便利和好处。也就是说，我不认为你会跳出平台，成为一名全职 Hugo 开发者。

也有基于 PHP 的静态站点生成器可用，如[Jigsaw](https://jigsaw.tighten.co/)by[tight](https://tighten.co/)。如果你是一个 WordPress/PHP 开发人员，也许值得看看 Jigsaw，跳过 Hugo 学习曲线，利用 Blade 模板。

如果你想学雨果，推荐看[长颈鹿学院的雨果 YouTube 课程](https://www.youtube.com/watch?v=qtIqKaDlqXo&list=PLLAZ4kZ9dFpOnyRlyS-liKL5ReHDcj4G3)。