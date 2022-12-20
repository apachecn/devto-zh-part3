# OAuth 简化版

> 原文：<https://dev.to/simov/oauth-simplified-2pbd>

我们经常需要在 web 应用程序中实现某种登录功能，或者授权我们的应用程序以编程方式访问用户数据。

过去，这是通过直接使用用户密码进行授权来实现的。不幸的是，这导致了许多问题，因此发明了一种委托授权框架，称为 OAuth。

OAuth 有两个版本:OAuth 1.0a 和 OAuth 2.0，后者是目前使用最广泛的版本。OAuth 1.0a 和 OAuth 2.0 的所谓的*服务器端流程(授权码授予)*都需要一个服务器 app，并且涉及多个步骤来提高安全性。

这使得这两种类型的授权流实现起来有点复杂，所以很自然地，我们会寻找第三方模块随时投入使用。

# 登录

几年前，我在 NodeJS 应用程序中使用 OAuth 使用 Passport 实现登录功能。然而，问题是，Passport 从来没有围绕在一个应用程序中有多个登录提供商的想法而构建。虽然完全可能，但它需要由不同的人为每个提供商开发单独的模块。

除此之外，Passport 还在 OAuth 流之后发出一个额外的请求，以获取用户的更详细的资料。不幸的是，这不是规范的一部分，随着时间的推移，它成为一个障碍，因为我总是需要一些稍微不同的东西。

我最终在我的应用程序中使用了相当多的胶水代码，只是为了少数几个登录提供商。更糟糕的是，现在我不得不为它编写测试。

所以我问自己:

> 有没有可能创造出更简单的东西？

你看，已经存在的东西并不重要:

> 你可以创新**永远**！

你总是可以利用现有的解决方案，创造一个更好的、更简单的、独特的解决方案。

遇见 **[授予](https://github.com/simov/grant)** ！

# 目标

Grant 是围绕非常狭窄和极端的用例构建的:

> 支持**无限**个 OAuth 提供者，不需要**任何**胶水代码。

众所周知，良好的配置数据结构是每个优秀应用的支柱。它的核心就是:

> 一个简单而强大的配置数据结构。

将模块的所有方面定义为 JSON，为各种有趣的用例打开了大门:

*   每个环境的静态配置
*   每个提供者的嵌套[静态](https://github.com/simov/grant#connect-static-overrides)子配置
*   [每次授权尝试的动态](https://github.com/simov/grant#dynamic-http)配置

事实上，配置甚至不是必需的，它可以通过 GET 或 POST 请求动态传递:

> 使[授权](https://github.com/simov/grant)成为完全透明的 **[OAuth 代理](https://github.com/simov/grant#dynamic-oauth-proxy)** 。

这意味着您可以将它部署在堆栈的某个地方，并从任何其他服务器和编程语言访问它。

# 如何

首先，我们需要为我们想要登录的每个提供者注册一个 OAuth 应用程序。比如我们要为[谷歌](https://console.developers.google.com)注册 OAuth 2.0 客户端 app，为[推特](https://developer.twitter.com/en/apps)注册 OAuth 1.0a 客户端 app。我们 OAuth 应用程序的重定向 URL 应该**总是**以`/connect/[provider]/callback` :
结尾

```
http://localhost:3000/connect/google/callback
http://localhost:3000/connect/twitter/callback 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要一个配置文件，我们可以在其中放置 OAuth 应用程序凭证以及一些其他选项:

```
{  "defaults":  {"origin":  "http://localhost:3000",  "callback":  "/hello",  "state":  true},  "google":  {"key":  "...",  "secret":  "...",  "scope":  ["openid"],  "nonce":  true},  "twitter":  {"key":  "...",  "secret":  "..."}  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们的服务器可能看起来像这样:

```
var express = require('express')
var session = require('express-session')
var grant = require('grant-express')

express()
  .use(session({secret: 'dev.to'}))
  .use(grant(require('./config.json')))
  .use('/hello', (req, res) => res.end(JSON.stringify(req.query, null, 2)))
  .listen(3000) 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们通过在浏览器中导航到以下 URL 来登录 Google 和 Twitter:

```
http://localhost:3000/connect/google
http://localhost:3000/connect/twitter 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

> OAuth 从未如此简单！

并证明 [Grant](https://github.com/simov/grant) 附带了对 **180+** 登录提供者的支持，但是任何其他符合 OAuth 规范的提供者都应该开箱即用。

Grant 还开发了一个[示例应用程序](https://grant.outofindex.com)，展示了它背后的设计目标及其真正的潜力。可以想象，这个应用程序在服务器上几乎没有任何代码，只有一个 JSON 配置。

最后，模块的[官方文档](https://github.com/simov/grant#table-of-contents)是一个很好的信息来源，我试图在其中涵盖不同的特性和用例。

编码快乐！