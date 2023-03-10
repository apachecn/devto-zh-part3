# 向 Hakyll 帖子添加 Twitter 卡片

> 原文：<https://dev.to/riccardoodone/adding-twitter-cards-to-hakyll-posts-gg2>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-04-15-adding-twitter-cards-to-hakyll-posts.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

## [T1】简介](#intro)

默认情况下，Hakyll 不会为 Twitter 卡片添加合适的 meta 标签。出于这个原因，如果我们试图在 tweet 上发布一个 Hakyll 帖子的链接，将不会有卡片呈现。我们可以通过使用 [Twitter 的卡验证器](https://cards-dev.twitter.com/validator)来确认这一点:

[![](img/0082bee55131ce370d8e134b4cd54bea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QJ7XP3IF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2h3o17n7mkv1088l6p6z.png)

这可以通过几行代码来解决:

[![](img/b48e46c91182da677b1290263b3754a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rLhyHqn---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ldzg7jeom0ph10thfejq.png)

## 几行代码

正如在[文档](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/abouts-cards)中所解释的，在 HTML 文档的`head`中添加以下内容就足够了:

```
<meta name="twitter:card" content="summary" />
<meta name="twitter:site" content="@nytimesbits" />
<meta name="twitter:creator" content="@nickbilton" />
<meta property="og:url" content="http://bits.blogs.nytimes.com/2011/12/08/a-twitter-for-my-sister/" />
<meta property="og:title" content="A Twitter for My Sister" />
<meta property="og:description" content="In the early days, Twitter grew so quickly that it was almost impossible to add new features because engineers spent their time trying to keep the rocket ship from stalling." />
<meta property="og:image" content="http://graphics8.nytimes.cimg/2011/12/08/technology/bits-newtwitter/bits-newtwitter-tmagArticle.jpg" /> 
```

Enter fullscreen mode Exit fullscreen mode

在 Hakyll 中，我们可以将 meta 标签添加到`templates/default.html`中。我们只需要确保它们只出现在帖子页上。为此，我们只需检查`description`键是否出现在上下文中:

```
$if(description)$
  <meta name="twitter:card" content="summary" />
  <meta name="twitter:site" content="MY_TWITTER_HANDLE" />
  <meta name="twitter:creator" content="MY_TWITTER_HANDLE" />
  <meta property="og:url" content="$url$" />
  <meta property="og:title" content="$title$" />
  <meta property="og:description" content="$description$" />
  <meta property="og:image" content="$cover_image$" />
$else$
$endif$ 
```

Enter fullscreen mode Exit fullscreen mode

然后在每个帖子的元数据中，我们需要添加`description`和`cover_image` :

```
---
...
description: Some description of my post
cover_image: https://example.com/cover_image
--- 
```

Enter fullscreen mode Exit fullscreen mode

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！