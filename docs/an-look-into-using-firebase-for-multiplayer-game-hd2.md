# 多人游戏中使用 Firebase 的探讨

> 原文：<https://dev.to/netcell/an-look-into-using-firebase-for-multiplayer-game-hd2>

> Firebase 很牛逼，除非你选错了产品做错了工作。为多人游戏供电的 Firebase 定价的理论分析。

# 云 Firestore vs 实时数据库

2017 年 10 月[，Firebase 推出](https://firebase.googleblog.com/2017/10/introducing-cloud-firestore.html)[云 Firestore](https://firebase.google.com/products/firestore/) 作为一个新的花式热云数据库。这项新服务与[实时数据库](https://firebase.google.com/products/realtime-database/)并行运行，可能会让新用户不知如何使用。Firebase 将用户推向新服务的努力使其更加混乱，给人一种 Firestore 将取代实时数据库的印象。当你进入实时数据库[上 Firebase 文档网站的任何一页时，你会在页面顶部看到一个高亮部分，鼓励你探索 Firestore。](https://firebase.google.com/docs/database/)

[![](img/95148d26a36b8164150c04639d52f997.png)](/assets/img/firestore-promo-docs.jpg)

在特性上，Firestore 实际上比实时数据库好得多:*更好的结构，更好的查询，更好的规则编写和更多的并发连接*。唯一的缺点:*它的定价*。虽然实时数据库的定价主要取决于您使用和传输的*数据量*，但 Firestore 的定价取决于您对数据库进行的*读/写操作量*。这使得 Firestore 对于实时应用程序来说很难使用，因为每个客户端都会对数据库更改做出反应并发出读操作，这需要花费金钱。

谁会想到实时数据库更适合实时应用呢？。

当然，没有人在实时多人游戏中使用数据库同步，但是回合制或异步多人游戏可以从 Firebase 方法中受益匪浅。然而，一个游戏会话可以轻松调用 1000 个操作，这意味着您可以通过免费计划每天支持 20 个游戏会话，通过每月 25 美元的计划每天支持 100 个游戏会话。另一方面，实时数据库可以免费支持 100 个 CCU，每月 25 美元可以支持 100，000 个 CCU。

[![](img/f6ffb3a0fc2a8b1897fb2520ff71177e.png)](/assets/img/firebase-database-plans.jpg)

最好同时使用这两个数据库，实时数据库用于游戏会话，Firestore 用于其他任何事情。这样，你可以只在游戏开始时打开与实时数据库的连接，并在游戏结束时用 [`firebaseRef.off()`](https://stackoverflow.com/questions/40186819/firebase-web-close-connection-to-the-realtime-database) 关闭连接，以优化同时连接数。

此外，如果你只是在游戏会话中使用实时数据库，那么扩展多个数据库会变得非常容易，因为你不需要在这些数据库之间共享任何数据。虽然 Firestore 应该能够无限自动扩展，但我不认为在这种情况下它的好处超过了成本。

# 云功能 HTTP 触发器

因为您正在对数据库进行更改，所以您可能希望使一些逻辑不依赖于客户端。云函数为您提供了创建 [HTTP 端点](https://cloud.google.com/functions/docs/calling/http)的能力。然而，这些函数是通过调用进行*计费的，这真的非常非常糟糕，因为如果您将它与 CORS 一起使用，就没有对端点的保护。*

[![](img/127b12a724fa27a089bc60775cf8aa60.png)](/assets/img/cloud-function-pricing.jpg)

人们可以向这些终端发出大量请求，您将因此被计费。从云函数内部拒绝代码不起作用，因为调用已经被计算在内。不过这并不是世界末日，因为您可以设置 CloudFlare 并使用[速率限制](https://www.cloudflare.com/rate-limiting/)功能来保护您的终端。该服务仅由好的请求计费[——允许到达您的端点的请求。](https://support.cloudflare.com/hc/en-us/articles/115000272247-Billing-for-Cloudflare-Rate-Limiting)

[![](img/ddc6f6a6c79ff627b0af285f53544146.png)](/assets/img/cloudfare-rate-limit-pricing.jpg)

然而，还有一个方面你需要考虑:定价。免费计划每月给你 125，000 次调用，每月 25 美元给你 200 万次调用。这远远低于 Firestore 上允许的写操作量。如果您不想使用 CloudFlare，或者如果您认为这种限制可能会带来麻烦，您可能希望探索迁移到您自己的服务器的选项。

关于云函数端点的一个非常有趣的点是，你可以使用像[`express.js`](https://expressjs.com/)[这样的框架将这些端点](https://firebase.google.com/docs/functions/http-events#using_existing_express_apps)包装成一个单一的云函数 HTTP 触发器。因为 Firebase 管理库可以在任何服务器上初始化，所以你可以把你的整个代码段带到另一个服务器上，它应该可以正常工作。这很重要，因为您可以开始开发具有云功能的应用程序，这使您的生活更加轻松，因为您知道您可以随时迁移。