# 杰基尔的布尔玛清洁主题入门

> 原文：<https://dev.to/chrisrhymes/getting-started-with-bulma-clean-theme-for-jekyll-1p3d>

我为 Jekyll 制作了一个基于布尔玛前端框架的[主题。它可能看起来很熟悉，因为我也在我的网站上使用它。如果你喜欢这个主题的外观，那么我想我会写一篇关于如何在你自己的网站上使用它以及如何在 GitHub 页面上使用它的博文。](https://rubygems.org/gems/bulma-clean-theme)

## 入门

首先，您需要在您的计算机上运行一个 Jekyll 的本地实例。我假设你熟悉 Jekyll，并且已经安装了你需要的所有东西。如果不是这样，请查看 Jekyll 网站上的文档。对于这个例子，我们称这个网站为 myblog。

创建一个新的 Jekyll 安装，然后进入 myblog 目录:

```
jekyll new myblog
cd myblog 
```

Enter fullscreen mode Exit fullscreen mode

然后将主题添加到 Gemfile:

```
gem "bulma-clean-theme" 
```

Enter fullscreen mode Exit fullscreen mode

然后将主题添加到 your _config.yml:

```
theme: bulma-clean-theme 
```

Enter fullscreen mode Exit fullscreen mode

然后运行 bundle 来安装所有东西

```
bundle 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以通过运行常用命令
来预览您的站点

```
bundle exec jekyll serve 
```

Enter fullscreen mode Exit fullscreen mode

## 创建页面

只要设置了 frontmatter，页面就可以是 html 或 markdown 文件。如果您愿意，pages 可以使用一些设置来进一步定制默认主题。

```
--------
layout: page
title: Page Title
subtitle: Page Subtitle
image: /path/to/image.jpg
description: The pages meta description
hero_image: /path/to/hero-image.jpg
hero_height: is-fullheight
-------- 
```

Enter fullscreen mode Exit fullscreen mode

如果你没有设置字幕、图像、英雄图像或英雄高度，那么将使用默认值，或者在英雄图像的情况下，不会在英雄上使用任何图像。

该主题使用 jekyll-seo-tag 插件，因此它将使用您在 frontmatter 中提供的信息来自动填充 meta 标签并打开 graph 标签。

## 帖子

帖子是按照标准的 Jekyll 方法创建的，位于 _posts 目录中，每个帖子都被命名为 YYYY-MM-DD-name-of-post . markdown。对于帖子 frontmatter，您可以使用与页面相同的设置，只是您需要更改帖子的布局并添加日期和作者设置。

## 博客页面

对于博客主页，用`layout: blog`创建一个 blog.html 页面，并使用普通页面的其他设置。该主题设置为使用 jekyll-paginate，因此您只需向 your _config.yml
添加分页选项

```
# Pagination settings
paginate: 5
paginate_path: "/blog/page:num" 
```

Enter fullscreen mode Exit fullscreen mode

## 站点默认值

如果您不想单独设置每个 hero_image，那么您可以在 your _config.yml 中设置默认值。它还会打开帖子页面右侧的边栏，显示最新帖子的链接。

```
defaults:
  -
    scope:
      path: ""
      type: "posts"
    values:
      author: "Author Name"
      layout: post
      hero_image: /path/to/hero-image.jpg
      show_sidebar: true 
```

Enter fullscreen mode Exit fullscreen mode

## 样式

该主题使用布尔玛前端框架，提供了广泛的 sass 变量定制。如果您想要覆盖任何标准变量，比如主主题颜色，那么在导入主样式表之前，在 assets/css/app.scss 中的新文件中设置一个 sass 变量。

```
--------
--------
$primary: #333333;
// Import Main CSS file from theme
@import "main"; 
```

Enter fullscreen mode Exit fullscreen mode

如果你愿意，你也可以在这个文件中添加任何你自己定制的 css。

### 导航

一旦你创建了文章和页面，你需要为访问者创建一个访问它们的方法。这个主题使用了布尔玛导航条，它是通过一个简单的 yaml 文件配置的。你所需要做的就是在`_data`目录下创建一个 navigation.yml 文件，其格式如下，包含你想要包含在顶部导航中的页面。您现在还可以向下拉菜单中添加项目。

```
- name: Page 1
  link: page-1
- name: Blog
  link: blog
  dropdown: 
    - name: Page 2
      link: page-2 
```

Enter fullscreen mode Exit fullscreen mode

布尔玛非常方便地将相同的 HTML 转换成手机/平板电脑友好的导航，所以你只需要为所有屏幕尺寸设置一次菜单。

## 对 Github 页面使用 bulma-clean-theme

对于使用 Github 页面的站点，您需要做的就是更新 _config.yml，以便它使用 remote_theme 而不是 theme，并将其更新为 chrisrhymes/bulma-clean-theme，以便它知道在构建您的站点时从哪个 GitHub repo 中提取主题。

```
#theme: bulma-clean-theme
remote_theme: chrisrhymes/bulma-clean-theme 
```

Enter fullscreen mode Exit fullscreen mode

然后像往常一样将您的更改推送到 Github。

似乎有一个问题，Jekyll 不能在本地使用 remote_theme，所以当在本地工作时，你必须重新添加主题，但记住在推回 GitHub 之前再次注释掉主题，否则你可能会收到一封构建警告电子邮件。

## 我希望你不会感到不知所措

看起来似乎有很多事情要做，但实际上，建立并运行一个网站并不需要很长时间。所有的选项都在那里，以防万一你想进一步定制主题，使之更加个性化，但你可以只使用基本的最小设置来启动和运行。

## 反馈和问题

如果你对如何改进这个主题有任何反馈、想法或问题，那么请在主题的 GitHub 页面上创建一个问题，我会尽快尝试研究一下。这个主题仍然很新，我对未来的增强有很多想法，所以当任何新功能可用时，我会在我的网站上写一篇新的博客。