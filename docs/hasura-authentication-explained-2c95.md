# 解释了 Hasura 认证

> 原文：<https://dev.to/hasurahq/hasura-authentication-explained-2c95>

正如你可能从 [blog.hasura.io](//blog.hasura.io) 上的各种其他博客文章中所知道的，hasura 支持各种类型的认证，在下面的博客文章中，我想列出在生产中使用 Hasura 时有哪些认证选项。

[![](img/194b46cfa67bab16d9e738fc7561e2d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--plNNVMmg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8qbcqnju140d1idkzj1v.jpg)

我们将谈论以下事情:

*   [保护 GraphQL 端点](#secure-endpoint)
*   [认证网页挂钩](#auth-webhook)
    *   [如何开始使用自定义 webhook](#custom-webhook)
    *   [用 auth webhook 设置 Hasura](#custom-webhook-1)
*   [使用 JWT 认证](#jwt-root)
    *   [自定义 JWT 服务器](#custom-jwt-server)
    *   [Auth0](#auth0)
    *   [燃烧基地](#firebase)
    *   [AWS 认知到](#cognito)
*   [Hasura 权限系统](#permissions)

## 保护您的 GraphQL 端点

当创建 Hasura engine 的新实例时，您可能已经看到**在控制台顶部保护您的端点**:

[![hasura secure endpoint pic](img/9fd65de1cb0b3059ba0f15f7cdb6a97f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aLSD987x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6hty5icfrafqyq6z3hrp.png)

该链接指向描述如何通过传递环境变量`HASURA_GRAPHQL_ADMIN_SECRET`来保护 GraphQL 端点的 docs 部分。无论你是使用 Docker，Heroku 还是其他什么，这将是你要做的第一步。

因此，现在如果您尝试访问控制台，您将看到一个简单的“登录”页面，要求您指定您的密码

[![](img/0a756739cf71a98f8c7291c27fd9f341.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J-YSA6DN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pboxriw7rdoive30swvq.png)

名称`ADMIN_SECRET`表明，如果您在请求头中传递它，您将把 **admin** 权限授予 API 消费者。这就是为什么只在服务器到服务器的交互中使用它很重要，比如在无服务器的功能中使用它。

所以这不是真正的认证。这只是保护您的终端。那么，身份验证呢？什么是适合您的身份验证方法呢？

您可能需要使用 Firebase、Auth0、Firebase 函数、Cognito、您的自定义身份验证服务器、一些未知的身份验证提供者等

因此，在查看不同的身份验证实现以及下面的大量博客文章链接和示例应用程序之前，让我们将身份验证类型分为两种主要类型

## 认证网页挂钩

那么什么是 Webhook 呢？简而言之，每当您为 Hasura engine 设置特定的环境变量时，包括自定义 URL，所有请求头(除非您的 webhook 配置为使用 GET)都将被传递到这个自定义 URL。在您的 Auth webhook 中，您可以做任何您想做的事情，它必须返回`200`或`401`状态代码。除了`200`状态码，你还发送了一堆前缀为`X-HASURA-*`的变量，这些变量可以在 Hasura 权限系统中使用，我们将在后面讨论

### 我们如何开始定制 webhook？

让我们看看我们现有的关于 [passport-js](https://github.com/hasura/graphql-engine/tree/master/community/boilerplates/auth-webhooks/passport-js) webhook 样板示例的案例研究。

我们将从克隆 [passport-js](https://github.com/hasura/graphql-engine/tree/master/community/boilerplates/auth-webhooks/passport-js) 开始，并按照[指南](https://github.com/hasura/graphql-engine/tree/master/community/boilerplates/auth-webhooks/passport-js#deploy-with-heroku)将其部署在 Heroku 上

现在，部署完成后，您可以使用 npm start 运行它，发送`/login`或`/signup`请求，并取回用户名和令牌

```
curl -H "Content-Type: application/json" \                           
     -d'{"username": "test123456", "password": "test123"}' \
     https://passport-auth-hasura.herokuapp.com/login 
```

Enter fullscreen mode Exit fullscreen mode

结果将是:

```
{
  "id": 3,
  "username": "test123456",
  "token": "dd26537df94305f35dca9605b9fade7b"
}% 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候在我们刚刚创建的数据库上设置 Hasura 引擎了

```
docker run -d -p 8081:8080 -e HASURA_GRAPHQL_DATABASE_URL=DATABASE_URL \
-e HASURA_GRAPHQL_ENABLE_CONSOLE=true \
-e HASURA_GRAPHQL_ADMIN_SECRET=secret \
hasura/graphql-engine:v1.0.0-alpha41 
```

Enter fullscreen mode Exit fullscreen mode

访问控制台时，您将看到以下内容:

[![](img/412aa71ffe0afd73c867ebb5c11fde9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cEyfBA4n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tkskfz30evzc4dcn8g62.png)

正如您可以看到的,`users`表是在我们运行 knex 迁移时创建的，当时我们设置了 passport-js 样板文件，但是我们也想从我们的 GraphQL API 中使用它，所以请确保您单击了`Track`按钮，现在您将能够查询您的用户:

[![](img/36f96854693f16312a4642955bf65de4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1AnV-HDR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a9e2qatti7azxemoh56n.png)

那么我们如何登录呢？我们如何定义权限？

passport-js 示例使用 LocalStrategy，所以一个人必须使用用户名和密码进行身份验证，因此它将返回

```
{
  "id": 3,
  "username": "test123456",
  "token": "dd26537df94305f35dca9605b9fade7b"
} 
```

Enter fullscreen mode Exit fullscreen mode

那么我们如何构建我们的客户端应用程序呢？

这个想法是，我们需要尝试通过点击以下端点 https://.herokuapp.com/login 从控制台登录

结果得到 token。现在我们需要将每个请求中的令牌作为授权头传递给 Hasura，如下所示:

`"Authorization": "Bearer dd26537df94305f35dca9605b9fade7b"`

### 用 auth webhook 设置 Hasura

现在剩下要做的就是配置 webhook env 变量并设置权限。要添加 auth webhook，请停止 docker 容器并添加`HASURA_GRAPHQL_AUTH_HOOK` env 变量。

```
docker run -d -p 8081:8080 \
-e HASURA_GRAPHQL_DATABASE_URL=DATABASE_URL \
-e HASURA_GRAPHQL_ENABLE_CONSOLE=true \
-e HASURA_GRAPHQL_ADMIN_SECRET=secret \
-e HASURA_GRAPHQL_AUTH_HOOK=https://<heroku-app-name>.herokuapp.com/webhook \
hasura/graphql-engine:v1.0.0-alpha41 
```

Enter fullscreen mode Exit fullscreen mode

现在将会发生的是，当您传递 Authorization header 时，它将被传递给自定义 auth webhook 并由它进行处理。
我们来看看 passport-js 代码:

```
exports.getWebhook = async (req, res, next) => {
  passport.authenticate('bearer', (err, user, info) => {
    if (err) { return handleResponse(res, 401, {'error': err}); }
    if (user) {
      handleResponse(res, 200, {
        'X-Hasura-Role': 'user',
        'X-Hasura-User-Id': `${user.id}`
      });
    } else {
      handleResponse(res, 200, {'X-Hasura-Role': 'anonymous'});
    }
  })(req, res, next);
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们通过了`anonymous`角色或`user.id`。
现在我们可以在权限选项卡中相应地设置我们的权限。

让我们来看一个不同的用例——firebase 函数 [auth hook](https://github.com/hasura/graphql-engine/blob/master/community/boilerplates/auth-webhooks/firebase-cloud-functions/index.js)

想法是一样的——返回在 Hasura 权限系统中使用的`X-Hasura-User-Id`和`X-Hasura-Role`。但是具体来说，在这个 auth webhook 中，我们也用 firebase 验证了我们被传递的`id_token`是正确的

还有其他的 auth-webhooks 样板文件，你可以在这里查看。

让我们来看看 webhooks 的授权流

[![](img/42b4ed33390cc3f66526b5ed69c47f5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5WhTqzT4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.hasura.io/1.0/_images/webhook-auth1.png)

*   将头球传给哈苏拉
*   标题被转发到您的自定义 auth webhook
*   响应返回 200 或 401。
*   在 200 的情况下，它必须返回在 Hasura 许可系统中使用的`X-Hasura-*`变量

你可以在[文档](https://docs.hasura.io/1.0/graphql/manual/auth/webhook.html)中了解更多信息

## 使用 JWT 认证

JSON Web Tokens 是一个开放标准，Hasura 通过一个配置选项支持它。你需要用下面的值传递`HASURA_GRAPHQL_JWT_SECRET`:

```
{
  "type": "<standard-JWT-algorithms>",
  "key": "<optional-key-as-string>",
  "jwk_url": "<optional-url-to-refresh-jwks>",
  "claims_namespace": "<optional-key-name-in-claims>",
  "claims_format": "json|stringified_json"
} 
```

Enter fullscreen mode Exit fullscreen mode

*   标准 JWT 算法哈苏拉支持 HS256，HS384，HS512，RS256，RS384，RS512 算法
*   `key`-JWT 加密的公钥。
*   `jwk_url` -提供商 [JWK](https://tools.ietf.org/html/rfc7517) 网址。这用于某些提供者使密钥过期。
*   `claims_namespace` -默认情况下，Hasura 引擎在“[https://hasura.io/jwt/claims](https://hasura.io/jwt/claims)”名称空间下查找编码在令牌中的`x-hasura-*`前缀变量。如果将`claims_namespace`作为`HASURA_GRAPHQL_JWT_SECRET`的一部分传递，那么 Hasura 引擎将在这个名称空间下寻找`x-hasura-`变量

比如这个:

```
 "https://hasura.io/jwt/claims":  {  "x-hasura-allowed-roles":  ["editor","user",  "mod"],  "x-hasura-default-role":  "user",  "x-hasura-user-id":  "1234567890",  "x-hasura-org-id":  "123",  "x-hasura-custom":  "custom-value"  } 
```

Enter fullscreen mode Exit fullscreen mode

如果将`"claims_namespace": "customClaim"`提供给哈苏拉引擎，哈苏拉引擎将期望在解码后需要在`customClaim`中搜索所有的`x-hasura-*`变量

在如何使用 JWT 的文档中描述了其他选项，但简单来说，它看起来像这样:

[![](img/436535641e4aa916b8138dd96f331d2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RRzMAxZZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.hasura.io/1.0/_images/jwt-auth1.png)

**任何返回 JWT 令牌的**认证服务器都必须在配置的或`https://hasura.io/jwt/claims`名称空间下通过带有`x-hasura-*`声明的 JWT。

JWT 将由引擎按照`HASURA_GRAPHQL_JWT_SECRET`中提供的配置进行解码，并且所有`x-hasura-*`声明将被转发到许可系统。

注:`x-hasura-default-role`和`x-hasura-allowed-roles`为必填项

* * *

现在让我们来看看最简单的 JWT 令牌用例的案例研究

我们去[https://jwt.io/](https://jwt.io/)选算法为`RS256`

[![](img/663c1dd2312ce88780b35960a1805112.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h8T66PAj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hxl7n0htmzpjxx9fjyij.png)

让我们将有效载荷数据改为:

```
{  "sub":  "1234567890",  "name":  "John Doe",  "admin":  true,  "iat":  1516239022,  "myAmazingAuth":  {  "x-hasura-allowed-roles":  ["editor","user",  "mod"],  "x-hasura-default-role":  "user",  "x-hasura-user-id":  "1234567890",  "x-hasura-org-id":  "123",  "x-hasura-custom":  "custom-value"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

所以我们的`HASURA_GRAPHQL_JWT_SECRET`将会是

```
{  "type":"RS256",  "claims_namespace":  "myAmazingAuth",  "key":  "copypasted public key"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将前往控制台，并将我们的令牌作为`Authorization`不记名令牌进行传递。
我们的`x-hasura-*`声明将从令牌中提取，并传递到权限对话框，在那里您可以设置角色，甚至可以获得对特定列的真正细粒度的访问。我们稍后会谈到许可系统

[![](img/1343a920cd369fd22cadd12172e02844.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W5f_LE-T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zx75n0ok2i4983jfr0fl.png)

让我们来看看我们可以使用的常见身份验证技术。

### 自定义 JWT 服务器

您可以运行自己的自定义 JWT 服务器，它将处理令牌生成和 Hasura 自定义声明。有一篇非常棒的博文描述了

服务器是 jwt 的 passport 服务器，你可以在这里查看代码

### Auth0

Auth0 使用 JWK urls 和 firebase，所以有一个很酷的工具可以用来为 auth0 或 firebase 自动生成配置。你可以在这里查看

此外，对于 Auth0，您需要在 Auth0 仪表板下的规则字段中配置自定义声明

有一篇关于 Auth0 的博文。你可以在这里查看

### 燃烧基地

Firebase 也使用 JWK，所以`HASURA_GRAPHQL_JWT_SECRET`看起来就像

```
{"type":"RS512",  "jwk_url":  "https://www.googleapis.com/service_accounts/v1/jwk/securetoken@system.gserviceaccount.com"} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要向 Firebase 添加自定义声明，这样我们就能够在编码的令牌中包含`X-Hasura-*`变量。

有一篇很棒的[博客文章](https://blog.hasura.io/authentication-and-authorization-using-hasura-and-firebase/)描述了使用 Firebase 进行身份验证。

### 认知到

使用 AWS Cognito，您需要完成几个步骤才能使其工作，因此，尽管我不会在这篇特定的博客文章中深入探讨如何做到这一点，但后面会有更详细的博客文章。使用 Cognito 的主要思路类似于 Auth0 或 Firebase。您需要在某个地方定义自定义声明。对于 Cognito，您不能在接口中定义它，但是您可以在生成令牌时创建自定义 Lambda，如下所示:

[![](img/36a781f7f88eca22e4d2128913fe6d0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nRsdX1UB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2xdyb2ix5ugnt7ri1di6.png)

我们在这个例子中的 Lambda 将会非常简单:

```
exports.handler = (event, context, callback) => {
    event.response = {
        "claimsOverrideDetails": {
            "claimsToAddOrOverride": {
                "https://hasura.io/jwt/claims": JSON.stringify({
                    "x-hasura-allowed-roles": ["anonymous","user", "admin"],
                    "x-hasura-default-role": "anonymous",
                    "x-hasura-user-id": event.request.userAttributes.sub,
                    "x-hasura-role": event.request.userAttributes.sub === "18cc0fe3-ad0b-44f8-a622-fd470c7eeb78" ? "admin": "user",
                    "x-hasura-custom": "custom-value"
                })
            }
        }
    }
    callback(null, event)
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们添加自定义声明的方式。现在你可以注意到，我们将声明作为字符串化的 JSON 传递。这样做是因为 Cognito 不支持嵌套的定制声明。此外，您可以看到，我正在检查特定用户是否是管理员，如果是，我将返回一个管理员角色。

在 Hasura 端，当设置`HASURA_GRAPHQL_JWT_SECRET`时，我需要传递`jwk_url`和`claims_format`作为字符串化的 JSON

```
{  "type":"RS256",  "jwk_url":  "https://cognito-idp.{region}.amazonaws.com/{userPoolId}/.well-known/jwks.json",  "claims_format":  "stringified_json"  } 
```

Enter fullscreen mode Exit fullscreen mode

基本上就是这样。您的认知令牌将被解码并传递给 Hasura 权限系统

# 哈苏拉权限系统

Hasura 有一个非常精细的评估权限的方法。在上面的章节中，我列出了使用 Hasura 进行认证的不同方法，虽然这些方法各不相同，但结果都是一样的。`X-Hasura-*`变量传递给 Hasura 权限系统。

[![](img/210c8554ea91d9feb9fb8b28c6568788.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xIjbyy_x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/urf5fl50vs1on83xnqrg.png)

第一层权限是角色。基于传递给权限系统的`x-hasura-default-role`和`x-hasura-allowed-roles`变量定义角色。

第二层是基于`x-hasura-user-id`或传递给权限系统的任何其他自定义变量的自定义检查

[![](img/c6afcbeb59d0d9cbc84ae6278aba01da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZgMGITHo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wbflnw7uh7bm6tzn4m1c.png)

正如您在本例中所看到的，我们正在检查帖子内容是否为“custom_value ”,只有当它为“custom _ value”时，我们才允许用户选择字段。这意味着只有当帖子内容等于“自定义值”时，用户才能看到帖子

或者，我们可以这样设置:

[![](img/d72e16e5acceef1d6cc2d16ba59e7bdf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AYj24Mwy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p00snj2yggkgszt7ab3t.png)

这意味着用户只能看到他/她的帖子。

# 总结

从上面的总结中可以看出，Hasura 支持许多不同的认证技术，并且符合行业最佳实践。除此之外，Hasura 权限系统给你一个真正细粒度的访问控制，这是一个必须在生产应用程序。