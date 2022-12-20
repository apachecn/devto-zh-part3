# 雨果 102:设置

> 原文：<https://dev.to/sarmis/hugo-102-setting-up-1fo9>

Hugo 是一个静态的站点生成器。md)到 html 页面。让我们来看看如何使用它来创建博客，并讨论一些可以改善体验的配置选项。

## 第一步——“安装”Hugo

1.  从 [Hugo Releases](https://github.com/gohugoio/hugo/releases) 下载您的操作系统的最新扩展版本
2.  将雨果提取到你选择的文件夹中
3.  (*可选*添加 Hugo 文件夹到 path - **如果不是**，那么用 Hugo 的完整路径替换`hugo`命令，即`"c:\program files\hugo\hugo"`

## 第二步——创建新博客

1.  启动新的命令提示符，并导航到将包含新站点的文件夹。
2.  使用`hugo new site my-blog`创建新的 hugo。将创建一个名为“我的博客”的文件夹。
3.  下载一个新主题，最好是 [beatiful hugo](https://github.com/halogenica/beautifulhugo/archive/master.zip) 。
4.  提取并复制`my-blog/themes/`中的新主题
5.  将主题文件夹从`beautifulhugo-master`重命名为`beautifulhugo`
6.  打开配置文件(`my-blog/config.toml`)并编辑主题行`theme = "beatifulhugo"`以选择新主题
7.  创建新帖子`hugo new post/first-post.md`
8.  运行开发服务器(`hugo serve -D -w`)
9.  在 http://localhost:1313 上预览[新站点——除非端口 1313 已经被使用，在这种情况下，新端口将在开发服务器的输出中列出](http://localhost:1313)

## 第三步-内容管理

虽然 hugo 中有很多组织内容的方法，但我更喜欢将每篇文章放在一个名为`yyyy-mm-dd title`的单独文件夹中，我也喜欢按年份对这些文件夹进行分组。最后，因为每篇文章都在各自的文件夹中，所以应该命名为`index.md`。

所以每个帖子的完整路径是`my-blog/content/post/yyyy/yyyy-mm-dd title/index.md`

```
my-blog
│
├── cv
│   └── index.md
│
├── content
│   ├── post
│   │   ├── 2018
│   │   │   ├── 2018-03-15 Hugo Gallery Shortcode
│   │   │   │   ├── index.md
│   │   │   │   └── feature.png  
│   │   │   │   
│   │   │   └── 2018-09-11 Hugo 101 Static Site Generators
│   │   │       ├── index.md
│   │   │       └── feature.png 
```

这种方法既将所有的帖子文件保存在一个文件夹中，又将它们与其余的帖子隔离开来，使得编辑更加容易，因为每次我们只需要处理一个包含我们所需要的内容的文件夹。

默认情况下，hugo 将使用文章标题来创建每个文章的 url，但是如果我们决定更改文章的标题，或者如果我们不需要将 url 与文章的标题匹配，这种方法可能会产生问题。此外，由于文章图像将在同一网址下，改变文章标题将需要改变所有图像的网址。一个更好的方法是使用“slug ”, slug 是我们分配给每篇文章的自定义 url 部分。

首先，我们需要指导 hugo 使用 slugs，因此我们编辑`my-blog\confg.toml`并添加以下行

```
[permalinks]
  post = "/:year/:slug/" 
```

这指示 hugo 为每篇文章创建一个由年份和 slug 组成的 url。

现在，为了给每个帖子设置 slug，我们需要理解这些 index.md 文件包含什么。
每个帖子文件(在我们的例子中为`index.md`)包含帖子的文本和一些元数据，称为`frontmatter`。frontmatter 必须出现在每个文件的开头`---`之间，由*名* : *值*对组成。例如，这一页的封面是

```
---
title: "Hugo 102: Setting Up"
date: 2019-03-15
tags: ["hugo"]
slug: "hugo-setup"
image: "2019/hugo-setup/featured.png"
--- 
```

日期的年份和 slug 用于构建每篇文章的 url。
注意，这里的 image 字段将用于文章的主页，因此，它的值必须相对于 blog 的根目录。

## 第 4 步-内容

每个帖子的实际内容都是从前面的事情之后开始的。我们写在头版下面的所有内容都会出现在邮报的正文里。内容应该在 [*markdown*](https://daringfireball.net/projects/markdown/) 中格式化，但普通的 html 也可以。

作为 markdown 的简短介绍:

*   在任何一行前面加上`#, ##, ###, ####, #####, ######`将会创建标题
*   在`* or **`中包含任何文本将使用*斜体*或**粗体**来呈现
*   用`-`作为任何行的前缀将创建列表

## 第五步-生成

默认情况下，hugo 会将所有的新帖子标记为草稿，在生成之前，会检查所有帖子的 frontmatter 中是否有
`draft: true`字段

使用`hugo`命令生成站点。它将创建一个名为`public`的文件夹，包含站点的所有内容。你可以通过上传`public`的内容到任何主机来发布你的站点。

###### [希腊开发商的原始帖子](https://greekdeveloper.com/2018/static-site-generators/)