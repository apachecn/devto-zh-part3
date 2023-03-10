# 杰基尔 HPSTR 到雨果 HPSTR 主题迁移

> 原文：<https://dev.to/infominer33/jekyll-hpstr-to-hugo-hpstr-migration-gia>

我尝试了几种不同的方法来学习雨果，最终找到了一种适合我的方法，我希望它能帮助任何想要实现飞跃的人。

这个指南和附带的库应该可以帮助任何人从 Jekyll 切换到 Hugo，*或者* Hugo 到 Jekyll。

如果你熟悉迈克尔·罗斯的其他 Jekyll 主题，比如 T2 的《最小错误》，那么 HPSTR 应该不会有太多惊喜。

*   [MMI stakes/hpstr-Jekyll-theme](https://github.com/mmistakes/hpstr-jekyll-theme)
*   [dldx/hpstr-hugo-theme](https://github.com/dldx/hpstr-hugo-theme/releases)

Jekyll HPSTR 像任何主题一样有其怪癖，但我是一个新手，在不对代码进行任何更改的情况下，在本地派生和构建它并不困难。

当我发现有 Hugo 版本的 HPSTR 主题时，我知道这是我彻底了解 Hugo 的机会！

现在，我有了同一个网站的两个分支！

一辆由杰基尔驱动，另一辆由 T2[雨果驱动！！

如果你问我的话，我觉得这很好！

## 为什么

1.  `mmistakes/hpstr-jekyll-theme`已存档，您不能再提交问题或拉式请求。
2.  Hugo 得到了开发社区的大力支持，其功能和受欢迎程度都在快速增长。
3.  有很多很棒的 Hugo 主题我一直跃跃欲试，尤其是那些支持 [indieweb](https://infominer.id/web-work/tags/indieweb/) 的。
4.  目录允许在新主题之间轻松测试和切换。

## HPSTR 哲基尔 vs 雨果

由于 HPSTR 是一个比较老的 Hugo 主题，我需要找一个老版本的 Hugo 来运行它。我从发布版附近开始，并通过发布版向前推进。

### 发布

*   [MMI stakes/hpstr-Jekyll-theme](https://github.com/mmistakes/hpstr-jekyll-theme/releases)
*   [dldx/hpstr-hugo-theme](https://github.com/dldx/hpstr-hugo-theme/releases)

原来每个版本都是同时发布的，而且从一开始就是一起打造的！

关于`hpstr-hugo-theme`的好事情是它没有存档，如果你想打开任何[问题](https://github.com/dldx/hpstr-hugo-theme/issues)或[拉动请求](https://github.com/dldx/hpstr-hugo-theme/pulls)，那是可以做到的。

[![](img/139fc2dd695d4ed4749e17ff30a7f2af.png)](https://github.com/mmistakes/hpstr-jekyll-theme/releases)
[![](img/8e08cde50d75534973e7ff7bf3bdb7c2.png)T6】](https://github.com/dldx/hpstr-hugo-theme/releases)

### 根目录

[![](img/9435e1e94941786e06bcf4a18ea86d38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pVuYQI3H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/obcGfNc.png)
[![](img/2c4be5dfe86520726af69e5efe04699b.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s---UMAEX6F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/KZQODtU.png)

## 应该运行哪个版本的 Hugo？

当 HPSTR 主题处于活跃开发阶段时，我回顾了 hugo 发布的版本，发现:

### [2016 年 6 月 6 日 0 . 16 . 0](https://github.com/gohugoio/hugo/releases?after=v0.16.0)

> Hugo 0.16 是我们有史以来最好最大的版本。Hugo 社区通过持续的性能改进超越了自己，从项目网站到文档到博客到作品集的所有类型的网站都有漂亮的主题，并且增加了稳定性。

### [T1。Debian，Ubuntu 等的 deb 包。](#deb-packages-for-debian-ubuntu-etc)

> Hugo 从 2016 年 1 月开始成为 Debian 和 Ubuntu 官方库的一部分！

这是一个很好的发现，因为我是一个 Ubuntu 用户。

## [v 0.17](https://github.com/gohugoio/hugo/releases/tag/v0.30)-2016 年 10 月

不同的平台有无数的选择，这个版本的 Hugo 是在 10 月份发布的，比 HPSTR 的最终版本晚了一个月。

> Hugo 将通过我们的 0.17 版本走向全球。[...]在你的网站上添加其他语言简单明了。
> 
> Hugo 延续了每次发布都比上一次更快的趋势。

显然，它的速度足够快，人们开始在生产中使用 hugo 的网络服务器，大约在这个时候。

> 0.17 中的新功能:作为快照包提供
> 
> 感谢@dholbach 的贡献#2443 和指导，Hugo 现在可以作为一个快照包使用了！(快照是一种新的通用 Linux 包。)去 https://uappexplorer.com/app/hugo.hugo-authors 看看吧

### [雨果 v0.17 下载链接](https://github.com/gohugoio/hugo/releases/tag/v0.17)

现在我已经让雨果开始工作了，让我们看看我能走多远..

## [v 0 . 30 . 1](https://github.com/gohugoio/hugo/releases/tag/v0.30.1)-2017 年 10 月 16 日

> 雨果 0.30 是赛车版。雨果已经非常非常快了，但是他还想要更多。所以我们增加了快速渲染模式。很难解释，所以用 hugo server 启动 Hugo 开发服务器，开始编辑。实时重新加载变得如此之快！“如何做和做什么”在其他地方有详细的讨论，但是简短的版本是，我们现在只重新渲染你正在工作的站点的部分。

这是一个很大的版本，所以我会提前寻找最近的错误修复，但避免任何功能发布。

## [v 0 . 31 . 1](https://github.com/gohugoio/hugo/releases/tag/v0.31.1)-2017 年 11 月 27 日

因此..在这之后，2018 年初还有另一个大发布，但我想我会坚持这一个。它在 HPSTR 完成开发一年后出版。

## 测试安装

安装完成后，键入 I `hugo version`并阅读:

[![](img/5de4a1b748e00b4e729392bd2d556ddd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S-i_K24E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/J2lnKkR.png)

完美！！

## 克隆雨果主题。

有了一些先决条件，让我们开始主题设置的第一步:

*   [主题设置- HPSTR Hugo](https://dldx.org/hpstr-hugo-theme/theme-setup/)

即使是从一个目录迁移到另一个目录，在一个新的目录中重新开始并根据需要从原始目录中获取内容也更容易。

```
$ mkdir newProject
$ cd newProject
$ mkdir themes
$ cd themes
$ git clone https://github.com/dldx/hpstr-hugo-theme.git hpstr 
```

Enter fullscreen mode Exit fullscreen mode

并且你会看到`hpster`位于:`/web-work/themes/hpstr`。

## 示例站点

一旦你有了`newProject/themes/hpstr`，你会在`hpstr`目录中找到文件夹`exampleSite`。

只需将`exampleSite`的内容复制到`newProject`的根目录下，测试一下看能不能运行。

`$ hugo`

它应该打印出这样的内容:

```
Started building sites ...
Built site for language en:
0 draft content
0 future content
0 expired content
10 pages created
0 non-page files copied
15 paginator pages created
9 tags created
0 categories created
total in 100 ms 
```

Enter fullscreen mode Exit fullscreen mode

除非你得到了打印出来的结果，否则现在还不要改变整个站点的配置。

确保将`exampleSite`的内容放在项目目录的根目录中，并且目录的结构正确。

只是为了确定一下！我还将测试服务器，看看我是否得到了一个网站。

`$ hugo server`

```
Started building sites ...
Built site for language en:
0 draft content
0 future content
0 expired content
10 pages created
0 non-page files copied
15 paginator pages created
9 tags created
0 categories created
total in 61 ms
Watching for changes in /newProject/ {data,content,static,themes} 
```

Enter fullscreen mode Exit fullscreen mode

耶！！！

## 目录结构 HPSTR 杰基尔 vs 雨果

### 【mmistakes.github.io/hpstr-jekyll-theme/theme-setup/】T2

[![](img/ee92e4746587b75586b996cc10b86570.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d6bF_UU9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/EBIdUUo.png)

### [hpstr-Hugo-主题/主题-设置](https://dldx.org/hpstr-hugo-theme/theme-setup/#setup:b8b08bb87737c3c5c8e714d4f8821e60)

[![](img/84baf4165d3b0f2da15602a5f4a36d92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vwdUdqeP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/nnI1lou.png)

## 在用新的。

现在，它只是简单地移动内容，换出一些前端内容和配置格式。

## 内容

> [帖子存储在内容目录](https://dldx.org/hpstr-hugo-theme/theme-setup/#adding-new-content:b8b08bb87737c3c5c8e714d4f8821e60)中。默认情况下，只有`content/posts`中的内容会显示在`All Posts`部分，但是，您可以手动链接到其他部分。例如，如果你在`gallery/photo1.md`创建一个帖子，你的帖子会同时出现在主页和/gallery 下。

[![](img/8d99868ab25d68fa9f11826ec561d73b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FkxBCjKr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/0hsOjV7.png)

您会注意到您的根目录反映了主题的目录结构，因为主题总是保存着运行所必需的所有内容的备份文件。

[![](img/ebdf84027ae54b01a0d3e1cda60de26e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bgUWK4lJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/83WTq5g.png)

### 原型/

我输入了一些标签和类别:

`web-work/themes/hpstr/archetypes/default.md`

```
+++
Description = ""
Tags = ["resources", "web-work"]
Categories = ["howto", "tools"]
menu = "main"
+++ 
```

Enter fullscreen mode Exit fullscreen mode

## 数据/

从 YAML 到伦敦

[![](img/ebdf84027ae54b01a0d3e1cda60de26e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bgUWK4lJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/83WTq5g.png)

在`theme`目录中有一个 navigation.yml 文件，甚至还有一个完整的`exampleSite`文件，我们可以将它复制到我们的根数据目录中并进行定制。

一定要把`title:`改成`title =`等等。

```
[[links]]
title = "Webwork.tools"
url = "/webwork.tools/"

[[links]]
title = "Services"
url = "/services/"

[[links]]
title = "Mostly Free SEO Tools"
url = "/seo-tools/"

[[links]]
title = "GitHub Pages Starter Pack"
url = "/github-pages-starter-pack/"

[[links]]
title = "Practical Public Key Crypto"
url = "/practical-public-key-crypto/"

[[links]]
title = "InfoMine"
url = "http://infominer.id/" 
```

Enter fullscreen mode Exit fullscreen mode

## 正面物质

**主要区别**:

*   使用`type`而不是`layout`
*   使用 frontmatter 变量`date`来表示出版日期，而不是硬编码到标题中。

### HPSTR-Jekyll

```
--------
layout: post
title: "Sample Code Post"
description: "Examples and code for various HPSTR functions."
tags: [samples, code, snippets]
comments: true
image:
  thumbimg/pgp-og.png
  feature: pgp-banner.png
  background: triangular.png
modified: 2019-05-30T13:15:59-23:00
permalink: /sample-code/
-------- 
```

Enter fullscreen mode Exit fullscreen mode

### HPSTR-Hugo

```
--------
type: post
title: Sample Post
description: "Just about everything you'll need to style in the theme: headings, paragraphs, blockquotes, tables, code blocks, and more."
date: 2011-03-10

tags: [sample post]
image:
  featureimg/abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
-------- 
```

Enter fullscreen mode Exit fullscreen mode

## config.toml

现在我们有地方了！下一步是将`config.toml`从我们的示例站点的根复制到我们的存储库的根。

这是最后一段路，我们应该可以走了

```
baseurl = "https://infominer.id/web-work"
languageCode = "en-gb"
title = "Webwork.tools: Independent Web-Work and Skyscraper Publishing."
theme = "hpstr"
pluralizelisttitles = false
PygmentsCodeFences = true
Paginate = 5
disqusShortname = "hpstrhugo"
publishdir = "docs"
enableEmoji = true
[params]
    subtitle = "Digital Tools for a Digital Transformation."
    [params.author]
        name = "⧉ Infominer"
        avatar =img/info-id.png"
        bio = "Full-Time Crypto-Curation and Histories ⧉ #Bitcoin #Blockchain #DecentralizedID ⧉ Research, Publishing, #WebWork #Indieweb ⧉"
        github = "infominer33/web-work"
    [params.image]
      feature =img/web-work-tools.png" 
```

Enter fullscreen mode Exit fullscreen mode

*   [配置 Hugo](http://web.archive.org/web/20161110120102/http://gohugo.io/overview/configuration)

> `publishdir = "docs"`

通过在我们的 config.toml 中设置这个，我们将能够要求 github 页面从 docs 文件夹中发布。

## 重定向

有一点很重要要注意，如果你和我一起从 Jekyll HPSTR 切换到 HPSTR Hugo。HPSTR Hugo 让你所有的帖子都活在`/posts/`目录里。

如果你曾经像我一样让你的博客生活在网站的根目录下，那么就添加别名，Hugo 就是这么做重定向的。

*   [内容管理-别名](https://gohugo.io/content-management/urls/#aliases)

```
aliases:
  - /title-goes-here/
  - /other-title-too/ 
```

Enter fullscreen mode Exit fullscreen mode

## 建造和服务

您必须键入`hugo`来实际构建并发布到 docs 目录。

即使`hugo server`构建了新的更新，它似乎也不会更新返回给我们的主机提供商的文件。我不是很明白，但好像是这样。

## 在每个列表前使用换行符！

## 推特卡片

这一部分，我没有弄清楚，我太高兴玩各种雨果主题，暂时不用担心它。当我有一个明确的答案时，我会在这里更新。

我不明白为什么`.Title`不是`.title`。

我很确定我需要补充

`<meta name="twitter:site" content="{{ .site }}">`

但是我还没有成功。

```
<!-- Twitter Cards -->
<meta name="twitter:title" content="{{ .Title }}">
<meta name="twitter:description" content="{{ with .Description }}{{ . }}{{ else }}{{ $.Site.Params.subtitle }}{{ end }}">
{{ with .Site.Params.owner.twitter }}<meta name="twitter:creator" content="{{ . }}">{{ end }}
{{ if isset ($.Scratch.Get "Params") "image" }}
    {{ $imageparams := index ($.Scratch.Get "Params") "image" }}
    {{ with $imageparams.thumb }}
        <meta name="twitter:card" content="summary">
        <meta name="twitter:image" content="{{ . | absURL }}">
    {{ else }}
        <meta name="twitter:card" content="summary_large_image">
        <meta name="twitter:image" content="{{ $imageparams.feature | absURL }}">
    {{ end }}
{{ end }} 
```

Enter fullscreen mode Exit fullscreen mode

我还将这些行添加到我的配置文件中

```
 [twitter]
  card = "summary_image_large"
  site = "@infominer33"
  title =  "Webwork.tools: Independent Web-Work and Skyscraper Publishing."
  description = "Resources for Independent Webworkers: Web-Publishing, SEO, Static Site Generators, Ubuntu, Research Driven Content."
  image =img/web-work-tools.png" 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/eb3ff3ec656a4ad1954a72a4d6099b72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wUWNyegO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/e6egggQ.png)

## 测试分支

我已经为不同的网站尝试了一些 Hugo 主题，特别是一个旨在支持 indieweb 原则的主题。我[有几张 HPSTR-Hugo 在高手分支的演示图](https://infominer.id/web-woimg/)。我没有预先考虑并保存哲基尔版本的图像。

然而，我将留下这些分支，以确保任何人都能更轻松地从 Jekyll 迁移到 Hugo。

*   [https://github.com/infominer33/web-work/tree/test-hugo](https://github.com/infominer33/web-work/tree/test-hugo)
*   [https://github.com/infominer33/web-work/tree/hpster-jekyll](https://github.com/infominer33/web-work/tree/hpster-jekyll)

## 资源

*   [web.archive.org-安装 Hugo v0.17](http://web.archive.org/web/20161110121524/http://gohugo.io/overview/installing)
*   [web.archive.org-使用 Hugo v0.17](http://web.archive.org/web/20161103072343/http://gohugo.io/overview/usage/)
*   [创建一个测试分支并将变更合并回主测试](https://infominer.id/web-work/posts/branches-git/)
*   [再见杰基尔——你好雨果](https://www.jvt.me/posts/2019/01/04/goodbye-jekyll-hello-hugo/)
*   [GitHub Pages Starter-Pack](https://infominer.id/web-work/github-pages-starter-pack/)-[T3】扩展资源 T5】](https://infominer.id/web-work/github-pages-extended-resources/)

## 其他雨果题材

*   [https://github.com/EmielH/tale-hugo](https://github.com/EmielH/tale-hugo)

## 越过彩虹

*   [用 Org-Mode 和 Hugo 写博客](http://masayk.github.io/tech/hugo/)

## 在评论中伸手

我很想听听你最喜欢的主题是什么，杰基尔还是雨果。

随时问任何问题，我不是专家，但我对搜索引擎非常在行，我有很多意见。:)