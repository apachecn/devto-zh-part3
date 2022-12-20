# 拥有 100%的搜索引擎优化

> 原文：<https://dev.to/thomasbnt/have-100-with-search-engine-optimization--f2p>

*   **简介**

你梦想有一个顶级的搜索引擎优化优化或简单地在你的网站上有更多的印象吗？接下来的内容是 web 开发中非常基础的。但是非常非常有用。

整个**搜索引擎优化**部分必须写在`<head>`标签中。请不要放入`<body>`。

*   **默认标签**

```
 <meta charset="utf-8">
        <!-- Let browser know website is optimized for mobile -->
        <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
        <!-- Your sublime title -->
        My title under 70 characters.
        <meta name="description" content="This is my loooong description, and ">
        <meta name="Keywords" content="seo,optimization,thisisakeyword">
        <!-- This is for index on search engine -->
        <meta name="robots" content="index, follow">
        <meta name="googlebot" content="index, follow">
        <!-- Your favicon in .png format or another -->
        <link rel="icon" type="image/png" href="folder/hey/favicon.png"/> 
```

Enter fullscreen mode Exit fullscreen mode

✅:描述必须长而详细。它不应该在几个页面上显示相同，否则你可能会在优化 malus。描述必须最长，在 150-160 个字符之间。

✅:描述必须长而详细。它不应该在几个页面上显示相同，否则你可能会在优化 malus。描述必须最长，在 150-160 个字符之间。

> ⚠在这两种情况下，避免重复，并尝试在每一页上修改它们。

*   **推特卡**

当你在社交网络上分享时，你通常会有一个很好的页面展示，但是请注意，我们可以改变 Twitter 和其他网络上的展示。为什么我认为推特事件很特别？因为它们有自己的标签，如果你用我们为脸书使用的标签，你会通过分享注意到它不起作用。下面我给你看一个例子。

```
 <!-- Twitter Card -->
        <meta name="twitter:url" content="https://my.link.org/">
        <!-- This is a large image, see below if you want a small, square image. -->
        <meta name="twitter:card" content="summary_large_image"/>
        <!-- Optional but displays who owns the website -->
        <meta name="twitter:site" content="@MyAccount"/>
        <meta name="twitter:title" content="My beautiful title" />
        <meta name="twitter:description" content="This is my description, it will only appear on Twitter."/> 
```

Enter fullscreen mode Exit fullscreen mode

[`summary_large_image`的例子(点击可完整显示):](https://twitter.com/Thomasbnt_/status/1217754449428525056)

> ![Thomas Bnt #Hacktoberfest ☕ profile image](img/58180d1e9603697ebda2f82132bcfd42.png)【托马斯 bnt 黑客故障】@ thomasnt _![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)【关于我的信息】
> ]fr〔t19〕🔑—0x 3430 B1 D7(“t20”)PGP . com . edu/PK/lookup？海……)【2020 年 1 月 16 日上午 10:24】[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1217754449428525056)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1217754449428525056)

[`summary`的例子(点击可完整显示):](https://twitter.com/Thomasbnt_/status/1445542186968256523)

> ![Thomas Bnt #Hacktoberfest ☕ profile image](img/58180d1e9603697ebda2f82132bcfd42.png)Thomas bnt # hacktoberfest☕@ Thomas bnt _![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我在 [@Spotify](https://twitter.com/Spotify) 上的每月播放列表！[#音乐](https://twitter.com/hashtag/Music)
> 
> [open.spotify.com/playlist/6P3kX…](https://t.co/mVw3XY3jY4)2021 年 10 月 06 日上午 00:11[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1445542186968256523)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1445542186968256523)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1445542186968256523)

然后你可以测试你网站上的 Twitter 卡是否正常。[点击此处并输入您的网址](https://cards-dev.twitter.com/validator)。

要了解更多关于这些限制以及如何用播放器、应用程序或其他程序定制这些标签的信息，你需要[文档](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/abouts-cards)来适当地告知你这些信息。

*   **打开图形(OG)**

SEO 部分的其余部分致力于开放图。主要用于脸书、Reddit 和许多其他网站，它是必须使用的基本代码。

```
 <meta property="og:title" content="My sublime giant title"/>
        <meta property="og:type" content="website"/>
        <meta property="og:image" content="https://my.link.org/favicon.png"/>
        <meta property="og:description" content="This is my description, that's never going to appear on twitter."/> 
```

Enter fullscreen mode Exit fullscreen mode

[脸书与 OG 的帖子示例](https://www.facebook.com/thepracticaldev/posts/1063561263820194)

* * *

Clem Onojeghuo 在 [Unsplash](https://unsplash.com/search/photos/search?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

| ☕ | 查看我的[推特账号](https://twitter.com/thomasbnt_)。您可以看到许多项目和更新。你也可以[在给我买杯咖啡上支持我，Stripe 或者 GitHub 赞助商](https://thomasbnt.dev/donate)。感谢阅读我的帖子！🤩 |
| --- | --- |