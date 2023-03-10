# Ruby on Rails API 认证

> 原文：<https://dev.to/codemy/ruby-on-rails-api-authentication-2374>

如果你用 Ruby on Rails 构建任何类型的 API，如果你想保护你的端点，你必须处理认证。如果您使用带有预建身份验证的 device，您会发现在管理 API 端点身份验证方面，device 稍有不足。在本帖中，我们将向您展示一些保护 Rails API 端点的选项，以及如何将它们集成到 devise 中。

## 简单令牌认证

如果您有一个简单的需求，只需要通过向客户端提供一个令牌来进行基本的身份验证，那么就不用再考虑了。这颗宝石就像它的名字一样简单。你可以在这里查看。

它的工作方式是运行一个简单的命令，这个命令几乎为用户模型生成了迁移文件。

```
rails g migration add_authentication_token_to_users "authentication_token:string{30}:uniq" 
```

这实际上是在用户模型中添加了一个`authentication_token`字段。然后像这样向用户模型添加一个宏。

```
class User < ApplicationRecord
  acts_as_token_authenticatable
  # ...

end 
```

然后向 ApplicationController 添加另一个宏

```
acts_as_token_authentication_handler_for User, fallback: :none 
```

那么从 ApplicationController 继承的任何端点都将使用令牌身份验证。

在客户端上，为了与端点进行身份验证，用户必须通过报头中的`X-User-Email`和`X-User-Token`。对于简单的令牌认证 gem 来说，这就差不多了。因为 gem 已经可以和 device 一起工作了，所以你所有的 device 工具都应该工作得很好。

如果你有兴趣了解更多关于 JWT 认证的信息，请查看我们下面的免费视频。

[![Token Authentication Part 1](img/d5436cd518bd28686e5981482ff78e89.png)](https://www.codemy.net/posts/rails-api-token-authentication-part-1-006/sets/rails-api)

## JWT 认证与定计

谈到 api 认证，JSON Web Token 风靡一时。这是因为当涉及到保护您的 API 时，它使生活变得方便。怎么会？如果您正在为多个客户端构建 api，那么通过使用 JWT，您就有了一种可以跨所有平台工作的身份验证方法，无论您是在浏览器、移动应用程序还是任何类型的使用 API 数据的服务器端服务中对客户端进行身份验证。

它支持大量的特性，并允许您动态地为客户端生成令牌，而无需在数据库中存储任何东西，因为令牌本身包含会话信息。您可以生成一个令牌，并在其中嵌入任何类型的信息。例如

```
{  "user_id":  1,  "exp":  1516239022  } 
```

变成了

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoxLCJleHAiOjE1MTYyMzkwMjJ9.UCTyi_gPUNhfeelaWxMTfnPtRtNByDUMxKTRpkyewQI 
```

这是使用服务器上的密钥生成的加密安全令牌。这意味着它只能被任何一方用密钥解密。这意味着如果这个密钥被泄露，您的身份验证令牌方法将会被泄露。

然后，客户端将这个令牌嵌入通常称为`Authorization`的报头中。每个请求都需要在服务器上使用这个令牌进行身份验证。这意味着客户端发送的令牌需要在服务器上解码，数据可以在服务器端验证。你可以在我们的免费视频查看更多关于 JWT 的基础知识。

[![JSON Web Token Part 1](img/b09e6ed42acd0aa1cfeff8a7385bd78b.png)](https://www.codemy.net/posts/rails-api-json-web-token-part-1-035/sets/rails-api)

在视频中，我们将向您展示如何使用 jwt gem 生成一个基本的 JWT 令牌。我们将向您展示如何使用 JWT 来开发认证解决方案。

一旦你开始在你的 Rails API 中使用 JWT，你就会意识到它不能在你现有的设备上工作。这就是为什么我们最近发布了 2 集节目，向您展示如何通过创建定制的设计策略将您的 JWT 解决方案与设计相结合。你可以在下面看看。它们是会员专属的，所以你需要每月支付 9 美元成为会员，但是如果你只是想要代码示例，可以通过点击下面的图片免费获得。

[![Devise + JWT Integration Part 1](img/8ca231c04da3b2a11d0f8233ac20ddf4.png)](https://www.codemy.net/posts/rails-api-devise-and-jwt-integration-part-1-040/sets/rails-api)

[![Devise + JWT Integration Part 2](img/29b2e3c132fd4da24a4e66f485e2d47a.png)](https://www.codemy.net/posts/rails-api-devise-and-jwt-integration-part-2-041/sets/rails-api)

如果你正在用 Ruby on Rails 构建一个 API，并且不知道从哪里开始，我推荐你去 JWT。因为 JWT 更能代表未来，有一个你可以遵循的标准。拥有一个标准会很有用，因为你有一个可以在多种平台/语言下工作的认证系统。因此，如果您正在用多种语言构建微服务，那么您可以使用服务语言的库，并让不同的服务使用一个认证系统来相互协作。

作为一种解决方案，JWT 可以随着应用程序复杂性的增长而增长。您可以从无数据库的 JWT 解决方案开始，然后添加将令牌存储在数据库中的功能，以便您可以随意终止令牌。

在我们的网站上，我们使用 JWT 做很多事情，认证我们的用户，服务对服务的交流，认证视频播放。由于我们可以将数据嵌入令牌本身，这使得它对于许多类型的事情都非常灵活。