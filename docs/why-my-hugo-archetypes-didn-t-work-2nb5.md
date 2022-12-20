# 为什么我的雨果原型不起作用

> 原文：<https://dev.to/clairecodes/why-my-hugo-archetypes-didn-t-work-2nb5>

一个关于 Hugo 模板文件和 Markdown 文件扩展名的小故事。

## 雨果中的原型概述

我的个人博客的当前版本是用静态站点生成器[雨果](https://gohugo.io)构建的。Hugo 有[原型](https://gohugo.io/content-management/archetypes/)的概念，这些原型是用于使用`hugo new`命令创建新内容类型的模板文件。每当你创建一个新的帖子时，你就不必复制粘贴定制的 [front matter](https://gohugo.io/content-management/front-matter/) (文件顶部的 YAML、TOML 或 JSON 数据)或其他定制内容。

Hugo 站点中的每种内容类型都可以有自己的原型模板。例如，在我的网站上，我创建标准的博客文章和定制的日记风格的文章，作为内容类型`blog`和`dev-diary`。当我键入命令`hugo new dev-diary/example-post.md`时，我希望创建的文件与用`hugo new blog/example-post.md`创建的文件具有不同的 YAML 字段

我的博客的前一个版本是用 Jekyll 构建的，我构建了自己的 [Yeoman generator](https://github.com/claireparker/generator-clekyll) 以达到与 Hugo 的原型功能相同的效果(加上这是一个有趣的附带项目)。原型比手工制作的自耕农发电机要简单得多，除了我遇到的这个问题。

## 这个问题

我尽职尽责地按照说明从 [Hugo 的文档](https://gohugo.io/content-management/archetypes/)中创建了一个原型文件，并将其添加到我的站点的原型目录中，因此它看起来像这样:

```
archetypes
├── default.md
└── blog.md 
```

我使用了命令`hugo new blog/post.markdown`,但是我的新“博客”原型没有被使用。相反，新帖使用了“默认”原型。我断断续续地用不同的文件名成功了，但不知道为什么。我认为使用“-kind”标志会有所帮助，但结果是转移注意力。我一度放弃了定制原型，尽管仍然在我的[博客网站 repo](https://github.com/claireparker/hugo-blog/tree/master/archetypes) 中检查我的失败尝试(因为为什么不呢？).

## 掌脸时刻💡

我最近重命名了我的一个内容类型(`interesting`到`dev-diary`)，并改变了文件名的格式，然后我的原型神奇地开始工作了！但是为什么呢？

我的新内容文件的降价文件扩展名与原型的扩展名不匹配🤦‍♀️

通过使用命令`hugo new blog/post.markdown`，blog 原型没有被正确调用，因为它具有与模板文件不同的文件扩展名，即`archetypes/blog.md`。“之间的不匹配。降价"和"。md”扩展导致原型失败。

相反，我需要使用`hugo new blog/post.md`并匹配 markdown 文件后缀。

两者都有”。降价"和"。md”是[降价文件](https://en.wikipedia.org/wiki/Markdown)的有效扩展名，我可以互换使用它们。或许该”。markdown”扩展并不打算将原型作为 Hugo 团队有意识的设计选择来使用，但这确实让我困惑了一段时间！