# blog-cli:一个使用静态站点生成器写博客的 cli

> 原文：<https://dev.to/kahlil/blog-cli-a-cli-for-blogging-with-static-site-generators-5b83>

我的博客是和雨果一起建的。我的每个博客都是用静态站点生成器或基于 CMS 的文件创建的。我喜欢静态网站生成器，它们使内容管理变得简单，安全，用它们来建立网站很有趣。

对我来说，他们有一个问题:写博客很烦人。一般来说，帖子的文件需要有日期和标题，然后你需要在帖子的前面放上内容。这一切只是非常乏味和恼人的。

所以我做了 blog-cli。它用正确的文件名在正确的位置为我创建了 Markdown 文件，插入了基本的前台内容，并在我最喜欢的 Markdown 编辑器中打开了该文件。这意味着我在 1 秒钟内从发表想法到写作。

这应该适用于大多数静态站点生成器。至少对于简单的设置是这样。

这是它的工作原理。

首先你必须安装博客命令行界面。为此，您需要 Node.js 和 npm。

```
npm install --global @kahlil/blog-cli 
```

Enter fullscreen mode Exit fullscreen mode

然后你需要告诉 blog-cli 你希望它把你的文章放在哪里。

```
blog --path ~/my-blog/posts 
```

Enter fullscreen mode Exit fullscreen mode

然后你需要告诉 blog-cli 你最喜欢的 Markdown 编辑器。

```
blog --editor 'ia writer' 
```

Enter fullscreen mode Exit fullscreen mode

现在你已经设置好了，你可以创建一个新的文章，并通过简单地指定一个 slug 在你的编辑器中打开它。

```
blog my-new-cool-post 
```

Enter fullscreen mode Exit fullscreen mode

这将在您指定的目录中创建一个文件名为 2019-01-17-my-new-cool-post.md 的新文件，在本例中为~ ~/my-blog/posts。

插入的前部物质看起来像这样:

```
---
draft: true
date: 2019-01-18T10:03:48.620Z
title: ""
--- 
```

Enter fullscreen mode Exit fullscreen mode

* * *

现在，如果你是酷孩子俱乐部的一员，那么你可能会把你的文件保存在一个 Git 存储库中，提交新的博客文章，并把它们推送到 Github，然后它会被部署到 Netlify。

事实证明，blog-cli 也可以帮助您做到这一点！

```
blog --publish 
```

Enter fullscreen mode Exit fullscreen mode

将自动提交所有更改，并显示消息“new post”并执行 git 推送。

很漂亮，对吧？！如果你也是静态站点生成器博客，我希望 blog-cli 可以帮助你。

如果您有任何改进的想法，请在 GitHub 上发送问题或 PR [。](https://github.com/kahlil/blog-cli)