# Twitter、Reddit、LinkedIn 和脸书的零 JavaScript 社交分享按钮

> 原文：<https://dev.to/swimburger/social-sharing-buttons-with-zero-javascript-to-twitter-reddit-linkedin-and-facebook-2fam>

在社交网络上分享内容是许多网站的重要功能。这是一个不难实现的常见功能，但通常外包给 JavaScript 库、专有供应商组件或直接来自社交平台的共享按钮。

有很多方法可以在你的网站上安装社交共享功能，而不需要开发，但最终都会归结为向你的网站添加更多的 JavaScript、CSS 和图像，最终会降低网站的速度。如果你直接使用来自社交平台的片段，你也允许这些社交网络收集你的访问者的数据。我们可以只用 HTML 来实现同样的功能，而不是减慢你的网站速度和提供广告简介。

## 社会共享基础知识

在实现社交分享按钮之前，我们需要确保当有人直接复制和粘贴链接时，我们的网页在社交媒体上看起来很棒。为了让我们的网页在社交媒体上呈现丰富的媒体内容，我们必须添加元数据，用标题、摘要、图像等描述页面内容。

对于 Twitter，使用以下元标签:

```
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:site" content="@nytimes">
<meta name="twitter:creator" content="@SarahMaslinNir">
<meta name="twitter:title" content="Parade of Fans for Houston’s Funeral">
<meta name="twitter:description" content="NEWARK - The guest list and parade of limousines with celebrities emerging from them seemed more suited to a red carpet event in Hollywood or New York than than a gritty stretch of Sussex Avenue near the former site of the James M. Baxter Terrace public housing project here.">
<meta name="twitter:image" content="http://graphics8.nytimes.cimg/2012/02/19/us/19whitney-span/19whitney-span-articleLarge.jpg"> 
```

Enter fullscreen mode Exit fullscreen mode

这个片段来自于 [Twitter 的文档](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/summary-card-with-large-image)。我们可以使用 [Twitter 的卡验证器](https://cards-dev.twitter.com/validator)来测试 URL。

对于脸书来说，这些是最重要的元标签:

```
<meta property="og:url"                content="http://www.nytimes.com/2015/02/19/arts/international/when-great-minds-dont-think-alike.html" />
<meta property="og:type"               content="article" />
<meta property="og:title"              content="When Great Minds Don’t Think Alike" />
<meta property="og:description"        content="How much does culture influence creative thinking?" />
<meta property="og:image"              content="http://static01.nyt.cimg/2015/02/19/arts/international/19iht-btnumbers19A/19iht-btnumbers19A-facebookJumbo-v2.jpg" /> 
```

Enter fullscreen mode Exit fullscreen mode

这个片段来自[脸书的文档](https://developers.facebook.com/docs/sharing/webmasters#markup)。

LinkedIn、Reddit 等更多平台方便地使用与脸书相同的开放图协议元标签。这里有一些工具可以验证你的页面在共享时的外观:

*   [脸书的脸书帖子分享调试器](https://developers.facebook.com/tools/debug/)
*   为 Reddit 帖子嵌入验证器
*   [LinkedIn 针对 LinkedIn 帖子的帖子检查器](https://www.linkedin.com/post-inspector/inspect/)

## 社交分享按钮

我们可以使用锚 HTML 元素，并将其指向社交平台提供的特定共享 URL，而不是使用 JavaScript 插件。每个共享 URL 都接受一个查询字符串参数来传递您自己的页面 URL。这里有一个完整 HTML 的例子:

```
<a href="https://twitter.com/intent/tweet?url=https%3a%2f%2fwww.swimburger.net%2fblog%2fazure%2fcreating-a-discord-bot-using-net-core-and-azure-app-services%3futm_source%3dtwitter%26utm_medium%3dsocial&via=RealSwimburger"
    target="_blank">
    Share to Twitter
</a>
<a href="https://www.reddit.com/submit?url=https%3a%2f%2fwww.swimburger.net%2fblog%2fazure%2fcreating-a-discord-bot-using-net-core-and-azure-app-services%3futm_source%3dreddit%26utm_medium%3dsocial&title=Creating+a+Discord+Bot+using+.NET+Core+and+Azure+App+Services"
    target="_blank">
    Share to Reddit
</a>
<a href="https://www.linkedin.com/shareArticle?mini=true&url=https%3a%2f%2fwww.swimburger.net%2fblog%2fazure%2fcreating-a-discord-bot-using-net-core-and-azure-app-services%3futm_source%3dlinkedin%26utm_medium%3dsocial"
    target="_blank">
    Share to LinkedIn
</a>
<a href="https://www.facebook.com/sharer.php?u=https%3a%2f%2fwww.swimburger.net%2fblog%2fazure%2fcreating-a-discord-bot-using-net-core-and-azure-app-services%3futm_source%3dfacebook%26utm_medium%3dsocial"
    target="_blank">
    Share to Facebook
</a> 
```

Enter fullscreen mode Exit fullscreen mode

以下是 URL 列表和一些有用的链接，以了解更多信息:

### 碎碎念

[https://twitter.com/intent/tweet?URL = https://www . swim burger . net/blog/azure/creating-a-discord-bot-using-net-core-and-azure-app-services&via = realsimburger](https://twitter.com/intent/tweet?url=https://www.swimburger.net/blog/azure/creating-a-discord-bot-using-net-core-and-azure-app-services&via=RealSwimburger)

阅读 twitter 的文档以获得额外的参数。

### Reddit

[http://www.reddit.com/submit?URL = http://example . com/yet/another/test&title = Hello，%20Reddit](http://www.reddit.com/submit?url=http://example.com/yet/another/test&title=Hello,%20Reddit) ！

来源: [Reddit 分享维基](https://www.reddit.com/wiki/submitting)

### 领英

[https://www.linkedin.com/shareArticle?mini=true&URL = https://www . swim burger . net/blog/azure/creating-a-discord-bot-using-net-core-and-azure-app-services&title = LinkedIn % 20 developer % 20 network&summary = My % 20 favorite % 20 developer % 20 program](https://www.linkedin.com/shareArticle?mini=true&url=https://www.swimburger.net/blog/azure/creating-a-discord-bot-using-net-core-and-azure-app-services&title=LinkedIn%20Developer%20Network&summary=My%20favorite%20developer%20program)

### 脸书

[https://www.facebook.com/sharer.php?href = https://www . swim burger . net/blog/azure/creating-a-discord-bot-using-net-core-and-azure-app-services&display = popup](https://www.facebook.com/sharer.php?href=https://www.swimburger.net/blog/azure/creating-a-discord-bot-using-net-core-and-azure-app-services&display=popup)

[欲了解更多选项，请查看脸书的股票文档](https://developers.facebook.com/docs/sharing/reference/share-dialog)

## 结论

社交平台会试图说服我们使用他们自己的分享按钮和 JavaScript。相反，我们可以用 HTML 提供共享功能，而不会影响速度或隐私。默认情况下，这些链接看起来很乏味，但是使用 CSS 和图片，我们可以提供一些非常吸引人的共享按钮！