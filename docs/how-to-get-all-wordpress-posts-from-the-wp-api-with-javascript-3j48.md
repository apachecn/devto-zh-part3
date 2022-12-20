# 如何用 JavaScript 从 WP API 获取所有 WordPress 帖子

> 原文：<https://dev.to/jackedwardlyons/how-to-get-all-wordpress-posts-from-the-wp-api-with-javascript-3j48>

*这篇文章最初发表在我的博客上，[jacklyons . me](https://jacklyons.me/how-to-get-all-wordpress-posts-from-the-wp-api-with-javascript/)T3】*

就在最近，我被要求为一个客户搜集一个 Wordpress 博客来审核他们所有的帖子。很自然，第一个想法是导出所有的帖子，然而，在快速搜索之后，我偶然发现了 **[。使用该 API，您可以直接向任何 wordpress 站点发出请求，并以 JSON 对象的形式检索博客文章列表。](https://developer.wordpress.org/rest-api/reference/)**

现在就试试吧。把这个输入你的浏览器，你会看到我最近发表的 10 篇博客文章的列表:

```
https://jacklyons.me/wp-json/wp/v2/posts
```

就这么简单！每个 post 对象内部都有大量的数据。您可以提取发布日期、发布状态等信息。API 文档规定，每个请求最多只能检索 100 个帖子。在这篇文章中，我将向你展示如何创建一个函数来一次获取你所有的文章！当你抓取的网站有成百上千的帖子时，这是很有帮助的。

下面我创建了一个超级简单的 HTML 片段，你可以复制并粘贴到一个基本的 HTML 文件中。请注意，我正在使用一些现代浏览器和 ES2017 功能，所以你必须使用 Chrome 或 Firefox。此外，如果你在抓取一个有几百或几千个帖子的站点，可能需要一点时间。