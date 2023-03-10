# ★为 freek.dev 涂上新的一层油漆

> 原文：<https://dev.to/freekmurze/a-new-coat-of-paint-for-freek-dev-1mm3>

有趣的是，即使像博客应用这样简单的东西也永远不会完成。一年半前，我做了一个重大的更新，将我的博客从 WordPress 转移到 Laravel 应用程序。去年 8 月，我还[放弃了我的自定义管理部分，转而使用 Nova](https://twitter.com/freekmurze/status/1033145162514526209?lang=en) 。尽管还没过多少时间，但已经到了刷新整个博客的时候了。

在过去的几周里，我给了我的博客一些爱。在幕后，我清理了代码库(你可以在 GitHub 上看到[)。我还把主域名从 murze.be 转移到了](https://github.com/spatie/freek.dev) [freek.dev](https://freek.dev) 。我认为对大多数人来说，记住我的名字比记住我的昵称更容易。

今天，我的博客有了新的设计。毫不奇怪，这个博客现在使用的是 [Tailwind CSS](https://tailwindcss.com/) 的 v1。这个新设计主要是由我的同事 [Seb](https://sebastiandedeyne.com/) 完成的，他一如既往地做得非常出色。让我们深入了解一些变化。

## 新主页

这是旧主页的样子:

[![Old homepage](img/40172e2120d7d6e74c6ccf06da30cdb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZStDS9s0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-coat/old-homepage.png)

这是新的:

[![New homepage](img/4cd5dc3742330e304a11a490ebb473e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NrF7Awyx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-coat/new-homepage.png)

以前只是列出链接，现在主页还显示摘录，所以你已经知道点击链接后你会看到什么样的内容。

在标题下面，也可以清楚的看到是什么类型的帖子。在我的博客上，有三种类型的帖子。“原创”是我自己写的帖子，“链接”是其他人写的帖子的链接，而“推文”是我觉得足够有趣的推文，可以在我的博客上分类(这样我就可以很容易地搜索它们)。

注意到每个帖子上面的彩色水平线了吗？那个颜色不是随机的。红色表示关于 Laravel 的帖子，蓝色表示 PHP 帖子，黄色表示 JavaScript 相关内容，灰色表示所有其他帖子。

## 在社交平台上发布

在我以前的博客上，所有的内容都是交叉发布到媒体上的。因为现在大多数内容都在 Medium 的付费墙后面，所以我决定对 Medium 整合进行核爆。

Twitter 的整合仍然存在。每当我发布新内容时，它也会自动在推特上发布。当发表一篇“链接”类型的文章时，tweet 是博客文章的标题，用引号括起来，表示不是我写的。我把它改成了有趣的东西。表情符号代替引号。一个小细节，我知道。

> ？斯帕蒂[https://t.co/oKaFkXiIX8](https://t.co/oKaFkXiIX8)T2【3】的一个项目# PHPT4【4】拉勒韦尔
> 
> -小鹿的弗里克？(“t0”@ free kmouth”[2012 年 6 月 5 日，“T3”](https://twitter.com/freekmurze/status/1136266834599133184?ref_src=twsrc%5Etfw)

## 新增发言人页面

在过去的几年里，我在全球各种用户组和会议上做了很多演讲。在你的旧博客上，隐藏在“关于”页面的底部，你可以找到一个按时间顺序排列的所有演讲的列表。

[![Old speaker page](img/87d88417a35700c218a1b9e73fdaee71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--23PrzVSC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-coat/old-speakers.png)

在新的博客上，这个列表被移到了一个全新的[演讲者页面](https://dev.to/speaker)。[会谈](https://freek.dev/speaking#all-talks)现在按照主题排序。在那个页面上，你还会发现[一些我做的演讲的视频](https://freek.dev/speaking#highlights)和一些基本信息。

## 新的搜索页面

博客上的所有内容都通过 [Scout](https://laravel.com/docs/5.8/scout) 发送到 [Algolia](https://www.algolia.com/) 进行索引。在旧的博客上，你可以看到一个干巴巴的标题列表作为搜索结果。

[![Old search page](img/c44aa19b2c1fca688ba6f6e8144d0b1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D8ZHSDgr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-coat/old-search.png)

在新博客[上，搜索](https://dev.to/search)被移到了一个专门的页面。帖子的类型和发布日期被添加到搜索结果中。Seb 的一个很酷的功能是，你还可以使用箭头键来导航搜索结果。相当酷！

[![New search page](img/55bd3e4592e126d719ccdba135655b97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Cefqvus--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-coat/new-search.png)

## 合闸:一次跳闸停机记忆线

为了好玩，让我们来看看从我五年前开始写博客以来的所有更新。

这是第一个版本:

[![First version](img/5410d33536d3bcb6d8158fefb6bb65b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GY78RvAo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-coat/murze1.jpg)

2016 年 10 月左右，我换了 WordPress 主题。

[![Second version](img/ccec74a71e38100b6850eda018ffe41e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OXPW5vIV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-coat/murze2.png)

大约 1.5 年前，我[从 WordPress 迁移到 Laravel](https://freek.dev/906-on-migrating-my-blog-from-wordpress-to-a-laravel-application) 并开始使用 Tailwind。

[![Third version](img/744c1f2db517acab5c2cc23774f0e9ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y-LRQsZz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-coat/murze3.png)

这是当前的主页:

[![Current version](img/4cd5dc3742330e304a11a490ebb473e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NrF7Awyx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-coat/new-homepage.png)

我希望你和我一样喜欢这个新设计！