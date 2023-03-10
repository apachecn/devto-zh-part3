# Twitter 账户活动 API 入门

> 原文：<https://dev.to/twitterdev/getting-started-with-twitter-s-account-activity-api-en8>

你曾经想要实时关注 Twitter 上的账户事件吗？本教程将帮助你开始使用 Twitter 的基于 webhook 的账户活动 API。

你可以在这里看教程，或者继续下面的阅读。

> ![unknown tweet media content](img/4a2c7f4887fb79a704c0b520aee6d9cb.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/amplify_video/1133865617461776385/vid/320x180/cwFnWjNP991O6hd7.mp4?tag=12" type="video/mp4"></video>![Twitter Dev profile image](img/2bfdc6a19ab3c91d7ebdba5e60890f91.png)Twitter Dev@ Twitter Dev![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)在这个新教程中，与我们伦敦 DevRel 团队的 Aurelia 和 Andy 一起学习如何开始使用 Twitter 的基于 webhook 的帐户活动 API。2019 年 5 月 31 日下午 16:39[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1134499619759644672)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1134499619759644672)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1134499619759644672)6

Twitter 的[账户活动 API](https://developer.twitter.com/en/products/accounts-and-users/account-activity-api.html) 实时向你发送与 Twitter 用户账户活动相关的事件。例如，每当订阅用户创建或删除一条推文、转发、接收或发送直接消息、关注新用户或喜欢一篇帖子时，帐户活动 API 都会传递一个 JSON 事件有效负载。你可以在 [Twitter 的开发者文档](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/overview)中找到账户活动 API 所服务的活动类型的完整列表。

### 基于 webhook 的 API

帐户活动 API 不同于 Twitter 的 REST 和流 API，因为它使用 REST 端点的组合进行设置和管理，并使用 webhooks 进行数据交付。如果您以前从未使用过它们，webhooks 通过将事件“推送”(也称为发布)到注册的 web 应用程序的 webhook URL 来传递数据。这是在特定事件(也称为帐户活动)发生时通过 HTTP 调用实时完成的。

Twitter 更常见的 API 是基于客户机-服务器方法的，在这种方法中，您可以创建一个客户机应用程序，它连接到 Twitter 的服务器以接收一些信息。这可以基于流模型，在这种模型中，您的客户端与 Twitter 服务器的连接始终保持开放，以实时接收数据，或者，它可以是客户端触发的请求，例如，如果您想要检索特定的 Tweet。

[![](img/5acd251f31a9cb10304d62b0aa17a5af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qF3ycnUN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vdkcvn6dsmhz7ix9hz80.png)

对于帐户活动 API，这种模式正好相反。不是您的客户端应用程序向 Twitter 的服务器发出连接请求，而是 Twitter 的服务器连接回您的 web 应用程序来监听数据。在这种情况下，Twitter 充当客户端，并打开到您的服务器的连接，以便在每次事件发生时向该 webhook 发布活动。

[![](img/46c515f698542be1833428bce378cf7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pMwTNuHk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23vp1uijm49gg2dkfaq7.png)

使用基于 webhook 的 API 有很多好处:

*   从计算资源的角度来看，webhooks 需要的资源比你有一个到流式 API 的持续开放连接时要少。
*   每个 POST 都有一个带有 HTTP 200 响应代码的活动交付确认。
*   如果某个活动没有成功交付，有一个内置的重试过程来重新发布，这减少了丢失数据的机会。
*   有了 webhooks，您的应用程序不再需要定期向 API 发出轮询请求来检查新数据是否可用。
*   新数据一旦可用，就会自动发送到您的服务器。因此，收到的数据几乎总是最新的。

### 你需要什么入门

您将需要以下内容:

1.  **[开发者账户](https://developer.twitter.com/en/account/get-started)** 需要使用任何 Twitter 的 API。你可以[申请一个](https://developer.twitter.com/en/apply-for-access)，如果你还没有的话。

2.  一个拥有正确权限的推特开发者应用程序。如果需要，您可以创建一个新的应用程序，方法是选择您的开发者门户的[应用程序部分下的“创建应用程序”按钮。](https://developer.twitter.com/en/apps)

    *   确保在应用程序页面的权限选项卡上启用“阅读、撰写和直接发送消息”。此外，请注意，权限设置对于以前具有较低权限设置的授权用户是不可追溯的。
    *   在“密钥和访问令牌”选项卡上，记下您的应用的消费者 API 密钥和 API 秘密。该信息将使您能够生成一个[不记名令牌](https://developer.twitter.com/en/docs/basics/authentication/guides/bearer-tokens)。
    *   您还必须生成应用程序的访问令牌和访问令牌密码。当你[注册你的 webhook URL](#securing-your-webhooks) 时，你将需要这些。
3.  **一个开发者环境**，你可以在你的开发者门户的 [Dev Environments 部分设置。选择您的应用程序并选择环境名称。当您开始使用](https://developer.twitter.com/en/account/environments)[不同帐户活动 API 端点](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/api-reference)时，您选择的名称将替换`:env_name`标记。

4.  **一个特定的、安全的 web 应用程序，URL 为**，用于通过 webhooks 接收数据。像 [ngrok](https://ngrok.com/) 这样的工具对于创建一个本地网址来测试开发中的 webhooks 可能是有用的。

### 保护你的网钩

为了通过 webhooks 接收来自 Twitter 的活动，您需要创建并保护一个 web 应用程序，该应用程序可以在您选择的 URL 上接收 HTTP (POST 和 GET)请求。这个应用程序需要使用您在开发环境中为帐户活动 API 设置的相同 Twitter 应用程序凭据进行保护，并编码为[使用这些凭据创建`crc_token`](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/guides/securing-webhooks) 。Twitter 将定期向您发送 webhook [挑战响应检查(CRC) GET 请求](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/guides/securing-webhooks.html)，针对这些请求，您的 web 应用程序需要返回使用 Twitter 应用程序凭证生成的`crc_token`，这些凭证与您的白名单开发人员环境应用程序的凭证相匹配。

您可以使用 [`POST account_activity/all/:env_name/webhooks`](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/api-reference/aaa-premium#post-account-activity-all-env-name-webhooks) 端点为帐户活动 API 注册一个 webhook URL。

帐户活动 API 的设置是通过 [API 请求](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/api-reference)完成的——开发者门户中没有内置用户界面。我们在 TwitterDev GitHub 页面上提供了一个示例仪表板工具，您可能会发现它对测试很有用:【https://github.com/twitterdev/account-activity-dashboard

您注册的 webhook URL 是帐户活动 API 将以包含事件数据的 HTTP POST 请求的形式发送帐户活动的地方。

### 认证

帐户活动 API 的身份验证可能比较棘手，因为 API 的不同功能有不同的身份验证方法。大多数 webhook 设置都是通过使用不记名令牌的[纯应用认证](https://developer.twitter.com/en/docs/basics/authentication/overview/application-only)来完成的。查看文档了解更多关于[如何建造一个](https://developer.twitter.com/en/docs/basics/authentication/guides/bearer-tokens)的信息。

订阅是通过应用程序用户验证为每个用户设置的。如果您想为自己的 Twitter 帐户接收事件，您可以使用由开发人员门户提供的 API 密钥和 API 秘密密钥生成的访问令牌，这是开始和测试 API 的好方法。如果你想接收与另一个 Twitter 账户相关的事件，该账户的所有者必须通过[三脚 OAuth 过程](https://developer.twitter.com/en/docs/basics/authentication/overview/3-legged-oauth)授予你许可。这个过程也是您需要在服务器端代码中实现的，因为这将使您能够接收用户的访问令牌。在[示例仪表板应用](https://github.com/twitterdev/account-activity-dashboard)中，我们使用[登录 Twitter](https://developer.twitter.com/en/docs/twitter-for-websites/log-in-with-twitter/guides/implementing-sign-in-with-twitter.html) 来获取每个用户的令牌，如果您想了解更多信息，请查看一下。

### 订阅

一旦您从[三脚 OAuth 进程](https://developer.twitter.com/en/docs/basics/authentication/overview/3-legged-oauth)获得了用户的访问令牌，您就可以使用它和应用程序用户认证一起为该用户创建新的订阅。

在这个阶段，你应该有这些东西:

1.  **正在运行的服务器端 web 应用**准备接收 webhook 事件。
2.  [**一个注册的 web hook URL**](#securing-your-webhooks)Twitter 将在这里以 POST 请求的形式发送事件(也称为帐户活动)。
3.  **一个或多个授权用户**，以[注册订阅](#subscriptions)的形式，您将收到他们的账户活动。

好吧！您已经准备好开始接收事件了！

### 事件

一旦用户订阅了您的 webhook URL，您就可以开始接收他们的事件。那么，事件到底是什么？在这种情况下，事件对应于与订阅用户的 Twitter 帐户相关的活动。每个事件都以 JSON 有效负载的形式交付。在下面的示例中，当用户收到一条直接消息时，会发送一个“消息创建”事件。有一个“文本”字段，包含消息的内容和其他信息。

```
{  "for_user_id":  "1102321381",  "direct_message_events":  [{  "type":  "message_create",  "id":  "1133674120380657669",  "created_timestamp":  "1559123947602",  "message_create":  {  "target":  {  "recipient_id":  "1102321381"  },  "sender_id":  "1029741837916012544",  "message_data":  {  "text":  "Good morning ☀️☕",  "entities":  {  "hashtags":  [],  "symbols":  [],  "user_mentions":  [],  "urls":  []  }  }  }  }],  "users":  {  "1102321381":  {  "id":  "1102321381",  "created_timestamp":  "1358552743000",  "name":  "Aurelia Specker",  "screen_name":  "AureliaSpecker",  "location":  "London, England",  "description":  "partner engineer @TwitterUK 👩‍💻 • instructor @CodeFirstGirls • outdoor enthusiast 🌄",  "protected":  false,  "verified":  false,  "followers_count":  589,  "friends_count":  745,  "statuses_count":  521,  "profile_image_url":  "http://pbs.twimg.com/profile_images/1025560966434443264/vsVPbXiT_normal.jpg",  "profile_image_url_https":  "https://pbs.twimg.com/profile_images/1025560966434443264/vsVPbXiT_normal.jpg"  },  "1029741837916012544":  {  "id":  "1029741837916012544",  "created_timestamp":  "1534344560624",  "name":  "Aurelia",  "screen_name":  "re_testing",  "location":  "London, England",  "description":  "I like traveling 🐪🌴☀️",  "protected":  false,  "verified":  false,  "followers_count":  4,  "friends_count":  10,  "statuses_count":  52,  "profile_image_url":  "http://pbs.twimg.com/profile_images/1029742489090121728/-83RqB5N_normal.jpg",  "profile_image_url_https":  "https://pbs.twimg.com/profile_images/1029742489090121728/-83RqB5N_normal.jpg"  }  }  }  {  "for_user_id":  "1102321381",  "direct_message_indicate_typing_events":  [{  "created_timestamp":  "1559123947551",  "sender_id":  "1029741837916012544",  "target":  {  "recipient_id":  "1102321381"  }  }],  "users":  {  "1102321381":  {  "id":  "1102321381",  "created_timestamp":  "1358552743000",  "name":  "Aurelia Specker",  "screen_name":  "AureliaSpecker",  "location":  "London, England",  "description":  "partner engineer @TwitterUK 👩‍💻 • instructor @CodeFirstGirls • outdoor enthusiast 🌄",  "protected":  false,  "verified":  false,  "followers_count":  589,  "friends_count":  745,  "statuses_count":  521,  "profile_image_url":  "http://pbs.twimg.com/profile_images/1025560966434443264/vsVPbXiT_normal.jpg",  "profile_image_url_https":  "https://pbs.twimg.com/profile_images/1025560966434443264/vsVPbXiT_normal.jpg"  },  "1029741837916012544":  {  "id":  "1029741837916012544",  "created_timestamp":  "1534344560624",  "name":  "Aurelia",  "screen_name":  "re_testing",  "location":  "London, England",  "description":  "I like traveling 🐪🌴☀️",  "protected":  false,  "verified":  false,  "followers_count":  4,  "friends_count":  10,  "statuses_count":  52,  "profile_image_url":  "http://pbs.twimg.com/profile_images/1029742489090121728/-83RqB5N_normal.jpg",  "profile_image_url_https":  "https://pbs.twimg.com/profile_images/1029742489090121728/-83RqB5N_normal.jpg"  }  }  } 
```

不可能选择接收某些类型的事件而排除其他类型的事件——您将总是接收与订阅用户相关的所有事件类型。事件类型的完整列表可在[开发者文档](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/guides/account-activity-data-objects)中查看。

如果您需要[重试](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/guides/activity-retries)，您可以注册一个付费选项来启用它们。通过重试，如果活动没有被提交，并且您的 webhook 没有返回成功的响应，Twitter 服务器将再次尝试发送活动。企业客户还通过[账户活动重放 API](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/guides/activity-replay-api.html) 获得了额外的冗余和恢复覆盖，提供了一种在前五天请求的时间段内重新发送活动的方式。

订阅越多，容量规划就变得越重要，以确保您的系统能够接收大量事件。

### 进一步资源

Twitter 的开发者文档包含更多资源，包括:

*   [导轨](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/guides/getting-started-with-webhooks)
*   [常见问题解答](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/FAQ)
*   [API 引用](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/api-reference)
*   [账户活动 API 参考指数](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/api-reference)
*   [快速入门指南](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/quick-start/enterprise-account-activity-api)(针对企业客户)

加入我们的[开发者社区](https://twittercommunity.com/)或者发微博给我们，地址是 [@TwitterDev](https://twitter.com/TwitterDev) ,如果这个教程启发了你去构建任何东西。请通过我们的[反馈频道](https://twitterdevfeedback.uservoice.com/)让我们知道您的想法。

[![](img/561a39ccfe18958837721337bc450154.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HkzFF5Fh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ba4nschfp5hs7k9djtl5.gif)