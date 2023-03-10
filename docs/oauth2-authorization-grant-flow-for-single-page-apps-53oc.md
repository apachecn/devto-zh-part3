# 单页应用程序的 OAuth2 授权代码流

> 原文：<https://dev.to/bwanicur/oauth2-authorization-grant-flow-for-single-page-apps-53oc>

*T1】原帖 T3】*

如果你可以访问你的 SPA 和后端服务器，你可以实现传统的 OAuth 授权代码流 T2。这对于支持旧的 OAuth 提供者可能很有用。

最近，我负责将几个第三方服务集成到一个应用程序中。这些服务中的大多数使用 OAuth2 协议来处理身份验证和授权。我与 OAuth 合作过很多次，但这是我第一次将 OAuth 认证服务集成到单页面应用程序(SPA) /后端微服务(API 等)架构中。不得不使用后端 API 和 SPA 带来了一些有趣的问题。

单页面应用程序可能使用 OAuth 的*隐式流*。隐式流通常用于无法访问后端服务器的客户端/ SPA 类型的应用程序。也许应用程序使用类似 Firebase 的东西作为后端。本文概述的解决方案是使用*授权授权流*。这种方法并不容易，除非一个人能够访问/控制后端服务器。

**一些基本规则:**

*   此解决方案涉及一些重定向，因此需要重新加载 SPA。如果这对你的需求来说有问题或不可接受，请立即跳槽。
*   注册的重定向 URI 可能与 SPA 没有相同的域。
*   该解决方案假设我们对自己的 SPA 应用程序使用 JWT 或某种基于令牌的身份验证。

### 隐式或授权代码流？

本文并不试图解决哪个 OAuth2 流最适合您的应用程序的问题。对于 spa 来说，这个行业似乎已经脱离了隐式流程，转而使用授权代码流程，拥有一个**公共客户端**。这个流程略有不同。没有秘密授权码被交换(这在步骤 2 中-见下文)。因为我可以访问和控制后端服务，所以我选择使用传统的授权代码流。

你觉得这些听起来像希腊语吗？如果是这样，不要担心。我们不会进入 OAuth2 的杂草中，也不会消失在争论哪个流是“唯一正确的方式”的兔子洞里。最佳解决方案取决于应用程序的目标。本文将描述一种解决方案。

### 简要 OAuth2 概述

即使上面写的一切对您来说都是全新的，您也有可能使用过支持 OAuth2 的应用程序。尤其是如果您使用了任何类型的单点登录按钮。想想那些随处可见的“使用(谷歌|脸书| Github |等)登录”按钮。

当您授权其中一个应用程序时，首先发生的是您必须通过用户名和密码验证自己。

[![](img/c9173ad58adf3de1f42c86127e7f9509.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vJXpiktA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hgaxbho5fw827t8ut2k3.png)

在您证明了您就是您自己之后，您必须允许 OAuth2 应用程序代表您做各种事情。

[![](img/784834c43393943985cb9ce8e0a50ead.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p64PQOwS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7z9qmkdeyqz715760ktx.png)

一旦您完成了“OAuth2 歌舞”,来自 OAuth2 提供者的响应应该包括一个我们可以用来访问第三方应用程序的访问令牌。也许这包括让我们的应用程序访问用户的谷歌联系人或电子邮件。或者让我们的应用程序访问用户的脸书组。

一个重要的 OAuth2 细节。这个重定向 URL 需要提前向 OAuth2 提供者注册。

下面是一个来自 Google OAuth2 配置页面的示例，其中在重定向 URI 中使用了本地主机。请注意“授权重定向 URIs 部分”。

[![](img/2f22126b55218a79742954fc55f09b99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jVd3-ehG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6pzuxhs3xwk6arpwk913.png)

*注意*:一些 OAuth2 提供商不允许“localhost”作为重定向域。SSH 隧道/端口转发可用于开发目的，以解决这个问题。

整个简化的流程如下所示:

[![](img/19c95a8cf82e92b9e16003f8d7fed0be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SVvD-_eR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.google.com/drawings/d/e/2PACX-1vQnYFLXp2VUBCUxbj5SL9_tXYhrqg_1Q1w7nc-dO6zXyvcJ7UdE0izWjMR2qzdFoGYIOaQThIVXrf9d/pub%3Fw%3D1440%26h%3D1080)

您可以想象这发生在配置或设置页面的某个地方。当用户决定“启用”第三方服务时，OAuth 歌舞正在幕后进行。最后，我们有了一个访问令牌。有时甚至更多...

### 打开 ID 连接(OIDC)

哦不！另一个缩写。在官方术语中，OIDC 是 OAuth 的“侧面”。那只是意味着，它就像是 OAuth2 的“味道”。关于 OIDC，重要的是我们不仅得到一个访问令牌，而且还得到一个“ID 令牌”。这是一个唯一的标识符，可以链接到我们的用户。

当我们从支持 OIDC 的提供商那里得到响应时，我们将存储 id 令牌并将其与我们的用户相关联。在我们的工作流中(见下图)，用户已经“登录”，因此我们可以将 ID 令牌与用户相关联。

### 单点登录

简单的 OAuth2 流(如上所述)已经得到处理。因此，我们应该已经存储了从步骤 2 返回的 ID 令牌。现在我们已经准备好了所有的东西，接下来是大计划:

[![](img/611d8bc9163332c5b4880870abb0efec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cEClj6V0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.google.com/drawings/d/e/2PACX-1vS9rFUUkIViEfnAzQEXFrdughwuMxz8dScTodDwNeOrYCKhJld9EUlx44EYMPthEFjJSlcmaaFUzsKl/pub%3Fw%3D1440%26h%3D1080)

现在，让我们从头开始。用户点击我们的 SSO 按钮(“使用 Google 登录”)。如果用户已经登录 Google，步骤 1 和 2 将透明地发生。否则，用户必须使用用户名/密码进行身份验证。

步骤 2 中的 ID 令牌可用于识别我们的用户！

`SELECT * FROM users WHERE <xxxx>.id_token = <ID Token>`

*这是伪 SQL。这篇文章不是解决如何或在哪里存储令牌*

我们现在有我们的用户了！我们的 SPA 是无状态的，它使用自己的一组 JWT 令牌来验证请求。这意味着，为了让我们的用户“登录”，他们的请求每次都需要附带一个有效的 JWT 令牌。

此时，可以实现许多基于 cookie/会话的解决方案，而不是本文的解决方案。我们可以创建 JWT 令牌并将其填充到 cookie 中，然后用户将“登录”。但是，基于 cookie 的解决方案有一些限制。首先，只有当 SPA 和后端服务器在同一个域(frontend.myapp.com 和 api.myapp.com)时，它才容易工作。有办法绕过这个挑战，但是，我们的解决方案绕过了对 cookies 的需求。通过这种方法，我们还可以避免 cookies 的其他安全问题。好的，我们不使用 cookies。下一步是什么？

**第三步:**我们已经确定了用户。为什么不直接创建 JWT，并将它包含在返回到我们的 SPA 的重定向中呢？

事实证明，这种方法存在一个主要问题。恶意用户可以窃取并重复使用 JWT 令牌来冒充您的用户。因此，使用 URL 中包含的 JWT 令牌将我们的初始 GET 请求重定向回我们的 SPA(步骤 3)是不安全的。该 URL 将出现在浏览器历史记录中的其他位置。

由于我们不想在 URL 中使用任何敏感内容重定向回 SPA，因此我们可以创建一个短期的一次性令牌。

**步骤 4:**SPA 会在 URL 中看到这个令牌。SPA 将立即使用 SSO 令牌向后端服务器发出 POST 请求。

**步骤 5:** 后端服务器将使用 SSO 令牌来识别用户，立即使 SSO 令牌无效，然后在 POST 主体中使用 JWT 令牌进行安全响应。用户“已登录”

### 好/坏由此解

**坏:**

*   重定向？在许多 SPA 情况下，后端服务器只有一个任务:输出 JSON。因为我们在这里重定向，我们打破了这个简单的规则。现在我们的后端服务器只输出 JSON…除了这个*一次*。在我们的情况下，我认为这种牺牲是值得的。
*   要管理的额外令牌。它*应该*只是短暂的存在，然而，它是另一个可以破碎的移动的棋子。

**好:**

*   没有饼干！
*   无国籍，这与有多少水疗中心在运营是一致的。
*   使用授权代码流可以确保旧的 OAuth 提供者(可能不使用加密数据传输)只能通过这个流进行访问。隐式流(以及一般的 OAuth2)需要加密的数据传输。**这是选择这种方法的制胜点**。事实证明，该项目需要支持一些较小的 OAuth 提供者，这些提供者已经过时，并不总是遵守 OAuth 的安全标准。

#### 链接:

*   [OAuth 2.0 — OAuth](https://oauth.net/2/)
*   [OAuth 2 简化版 Aaron Parecki](https://aaronparecki.com/oauth-2-simplified/)
*   这是一个类似的方法，但是使用的是公共客户端。这可以在无法访问后端服务器时实现。也是伟大的安全笔记。[安全地使用 OIDC 授权代码流和带有单页应用程序的公共客户端](https://medium.com/@robert.broeckelmann/securely-using-the-oidc-authorization-code-flow-and-a-public-client-with-single-page-applications-55e0a648ab3a)