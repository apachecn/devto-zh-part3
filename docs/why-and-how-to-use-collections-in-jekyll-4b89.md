# 为什么以及如何在 jekyll 中使用集合

> 原文：<https://dev.to/devdiaries/why-and-how-to-use-collections-in-jekyll-4b89>

## 为什么要用集合？

这个网站是建立在 Jekyll 的基础上的，我们稍后会写更多，但本质上这个网站是作为一个博客建立的。我们也有相当多的社交媒体帖子
，我们希望在我们的网站上提供更多的内容。通过这种方式，我们可以在社交媒体上分享一些内容，并让读者访问我们的网站以获取更多信息。因为我们已经有了长格式的博客帖子，这就是我们的[帖子](https://jekyllrb.com/docs/posts/)类型。我们希望以另一种方式提供另一种类型的内容，但不希望它是一个完整的帖子。进入我们所说的[_ 社交帖子](https://github.com/dev-diaries/web/tree/master/_social-posts)。这些是`collection`(看到我们在那里做了什么吗？)的所有社交媒体帖子。通过这种方式来显示它们，我们可以避免通过某个标签或类别对博客文章进行任何不必要的过滤。Jekyll collections 为我们提供了一种在博客上创建子类型的简单方法🙌。

## 如何

我们需要做的第一件事是更新我们的配置来“注册”新的
集合:

```
collections:
  social-posts:
    output: true 
```

这里我们将集合命名为`social-posts`，并声明我们希望它为每个文档输出一个页面。通过这种方式，每个社交帖子都有自己的专用页面，因此当用户访问我们的网站了解更多信息时，他们可以看到一整页专用于[文本导航的页面:例如，在单词](https://dev.to/social-posts/text-navigation/)之间跳转。

现在我们有了这个，我们需要创建一个目录，这样 Jekyll 就知道如何获取社交帖子。因此，在我们项目的根目录中，我们创建了目录:

```
mkdir _social_posts 
```

然后在该目录中创建我们的帖子。我们将目录(cd)更改为
我们刚刚创建的目录(文件夹)，然后创建(触摸)第一个帖子:

```
cd _social__posts
touch text-navigation.md 
```

答对了。我们有一个新的社交媒体帖子集，只有一个帖子(文本导航)。想看看我们是如何把它整合到这个网站上的吗？检查我们的[提交](https://github.com/dev-diaries/web/commit/3176105dac2f421246512f4dd0946627ef2ec47a)，因为*不谈论它，编码它*。我们会发布更多关于这个网站是如何建立的，当我们添加更多功能时，我们当然会写博客，因为*分享是关怀*。

[开发人员日记上的原始帖子](https://www.dev-diaries.com/blog/why-and-how-to-use-collections-in-jekyll/)