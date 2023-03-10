# 将开发人员的所有帖子推送到 Webhooks

> 原文：<https://dev.to/mrrobot/push-all-posts-of-devto-into-webhooks-3o0h>

*   **简介**

大家好，今天我想分享一个大多数人可能都知道的工具，但在它上面发帖总是有好处的。

> 向 Dev.to 上的每个帖子发出一个简单的 Webhook 请求。

这个系统非常简单，它将从 dev .到检索 [RSS 提要，每次有新条目时，](https://dev.to/feed) [IFTTT](https://ifttt.com) 将启动并在 Webhook 中发送一条消息。

在这个例子中，我的 Webhook 输出在 [Discord](https://discord.com) 上，这是一个被玩家、开发者、社区和其他人广泛使用的交流平台。

*   **设置**

**1)** 首先，请登录或创建您的 IFTTT 帐户。
**2)** 开始点击 **[新建小程序](https://ifttt.com/create)**
**3)** 如果 **RSS Feed** ▶️新建 Feed 条目▶️在 Feed URL 中，放入'[https://dev.to/feed](https://dev.to/feed)或'[https://dev.to/rss](https://dev.to/rss)'▶️**创建触发器**
**4)** 然后 **Webhooks** ▶️发出 web 请求。

对于 Discord 上的 webhooks 设置，下面是一个例子:

| **网址** | [https://discord . com/API/web hooks/524350588369240075/tcqj 9 ioe 9 vmlvglurmoen 0 iwnx 2d p 43 z-qcxua 5 VQ 3 jki OE 92 p 3 edijq 7 uapn](https://discord.com/api/webhooks/524350588369240075/tcQJ9ioe9VMlvgLuRMoen0iWNx2DP43z-qcxUA5Vq3jkioe92P3EdIjq7Uapn) |
| --- | --- |
| **方法** | 邮政 |
| **内容类型** | 应用程序/json |
| **正文** |  |

对于你的身体，你可以发送一个不和谐的嵌入信息。

```
 {  "embeds":  [{  "author":{  "name":"{{EntryTitle}}",  "url":"{{EntryUrl}}"  },  "footer":{  "text":"Auteur : {{EntryAuthor}}",  "icon_url":"https://cdn.discordapp.com/attachments/377205421640908810/526770363456159768/apple-icon-5c6fa9f2bce280428589c6195b7f1924206a53b782b371cfe2d02da932c8c173.png"  },  "color":1776669  }]  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你不想嵌入，而只是一个简单的消息，包含新文章的标题和 URL:

```
 {"content":  "[{{EntryTitle}}]({{EntryUrl}}) was posted by {{EntryAuthor}}"} 
```

Enter fullscreen mode Exit fullscreen mode

*   **不一致时 Webhook 的输出示例**

这里有一个例子，它每天都在我的服务器上发布，作为服务器上的几个开发者，我们希望尽可能快地让一切触手可及。因为这是最好的交流方式，无论是书面的还是口头的，所以我更愿意创建一个简单的 IFTTT 条件来获取 RSS 提要并将其放入。

[![An example of messages on Discord with the Webhook](img/aa7a4d657d3e007c4498cb0ba8c26a6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vUcaSLDF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q3u1prac179tgovfy4u8.png)

📷我还做了另一个 IFTTT 系统，这个已经在平台上准备好了。每天早晚各一次，推特账号[@ Splash _ daily](https://twitter.com/Splash_Everyday)发一张来自 [Unsplash](http://unsplash.com) 的照片。

__
顺便说一句，我正在寻找任何可以添加到我的网页挂钩列表中的 RSS 源，如果你有，就在下面评论它们，我会看看它是否有用！

| ☕ | 查看我的[推特账号](https://twitter.com/thomasbnt_)。您可以看到许多项目和更新。你也可以[在给我买杯咖啡上支持我，Stripe 或者 GitHub 赞助商](https://thomasbnt.dev/donate)。感谢阅读我的帖子！🤩 |
| --- | --- |