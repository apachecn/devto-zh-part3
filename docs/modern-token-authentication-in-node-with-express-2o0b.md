# 具有 Express 的节点中的现代令牌认证

> 原文：<https://dev.to/oktadev/modern-token-authentication-in-node-with-express-2o0b>

令牌认证是当今最热门的 web 应用程序用户认证方式。人们对令牌认证很感兴趣，因为在某些情况下，它可以比传统的基于会话的认证更快，并且还允许您获得一些额外的灵活性。在这篇文章中，我将教你所有关于令牌认证的知识:它是什么，它是如何工作的，为什么你应该使用它，以及如何在你的节点应用程序中使用它。我们开始吧！

## 什么是令牌认证？

令牌身份验证是一种使用临时令牌(通常是 JSON Web 令牌)而不是实际凭证来验证用户进入应用程序的方法。

这在 web 身份验证环境中的工作方式如下:

*   用户想要登录网站
*   用户向网站提供他们的电子邮件地址和密码(他们的凭证)
*   网站为用户生成一个令牌
*   用户的浏览器存储令牌
*   当用户向网站发出后续请求时，他们的令牌将随请求一起发送
*   该网站将验证令牌，并使用它来确定用户是谁

这种方法的好处是，令牌包含有关用户的嵌入式信息，因此网站可以接收令牌并发现用户是谁以及他们拥有什么权限，而*不一定*需要与中央数据库对话。这意味着您可能不需要维护会话存储。

以下是流程的典型形象化视图:

[![token authentication flow](img/e5ff896a1b57d911d60a8f0c30efa0b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ynel2BK2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-token-auth/token-authentication-flow-69804c12334715c597128cd9273bca5e32ed516b62987902310efc54d1840a40.png)

## 什么是 JSON Web 令牌？

在讨论 JSON Web 令牌之前，让我们澄清一些术语:

**认证**是验证用户身份的过程。

一个**令牌**是一个可以用来向服务器认证用户的对象。令牌包含用于识别和验证用户身份的嵌入式用户数据。

*JSON Web token(jwt)*是一个开放标准([在此了解更多关于 jwt 的信息](https://developer.okta.com/blog/2018/06/20/what-happens-if-your-jwt-is-stolen))，它定义了一种使用 JSON 对象在各方之间传输信息的安全方式。jwt 总是加密签名的(有时是加密的)，可以使用秘密密钥(对称的)或公共/私有密钥对(非对称的)进行签名。

jwt 是最受欢迎的令牌类型，通常是人们在提到*“令牌认证”*时的意思。

下面是一个典型的 JWT 压缩后的样子，URL 安全的形式:

```
eyJraWQiOiJ1dURLVTMxZWRvTi0wd0xMUnl1TW1vbmtBdi1OaFEwejZhWmxjdTN5NU8wIiwiYWxnIjoiUlMyNTYifQ.eyJ2ZXIiOjEsImp0aSI6IkFULnVfT094R3pXd1RjRFlseGZwcDVYXzNxdVIwdlJuc25YbXdMZld0TDFjdG8iLCJpc3MiOiJodHRwczovL2Rldi04MTk2MzMub2t0YXByZXZpZXcuY29tL29hdXRoMi9kZWZhdWx0IiwiYXVkIjoiYXBpOi8vZGVmYXVsdCIsImlhdCI6MTU0NjcyNjIyOCwiZXhwIjoxNTQ2NzI5ODI4LCJjaWQiOiIwb2Fpb3g4Ym1zQktWWGt1MzBoNyIsInNjcCI6WyJjdXN0b21TY29wZSJdLCJzdWIiOiIwb2Fpb3g4Ym1zQktWWGt1MzBoNyJ9.fhZRWThFxhiS6Pgrup6hM08oSqDPd1JrZSDIH_blD5S20c2hQQ3D3RZyhNKMnYclyus_mo-H-mS-Ak3YzM8S0JwZ8m3Vid4smW953peBMnmBNotAE--yE0toc2dIUG3BWQR34hah253bKmp17Yh6bWGwH60oQxnuM_NVUpa-NJMBc6-Mu5ra0lKfr60ne9-jKVFcavd9ZnVTLiug_sXFlhxgaQm4V_hhcvcLSwCXTiIcQsJkI0rP7WuVvjYVyK_sPeW3A44_T5qhyDR_E_mk1rHORlkMYGPg34mcwob5iA7alNZOnzN_7ApcbylDbK5KS1umBqqevtghEyjOEWQQmQ 
```

虽然乍一看这看起来很复杂，难以理解，但实际上并没有那么复杂！jwt 由三部分组成，由点(`.` ): `xxxxxx.yyyyyy.zzzzzz`分隔。这些部分分别代表 JWT 报头、有效载荷和签名。

让我们一次分析一个部分。

### JSON Web 令牌头

JWT 头是一个 Base64URL 编码的 JSON 对象。它包含描述令牌类型和正在使用的签名算法的信息，如 HMAC、SHA256 或 RSA。

例如:

```
{
  "typ": "JWT",
  "alg": "HS256"
} 
```

JWT 规范是灵活的，允许使用不同类型的算法，这就是为什么这个报头字段总是存在的原因。

### JSON Web 令牌有效负载

JWT 有效载荷包含称为*声明*的东西，这些声明是关于实体(通常是用户)和附加数据的声明。有三种不同类型的索赔:*注册*、*公共*和*私人*索赔。声明是 JSON Web 令牌中最“有趣”的部分，因为它们包含了有关用户的数据。

一组预定义的声明( [RFC 7519](https://tools.ietf.org/html/rfc7519#section-4.1) )是可选的，但也是推荐的。一些例子是 **iss** (发行者) **exp** (到期时间)，以及 **sub** (主题)。

自定义声明(您在创建令牌时定义的声明)用于在有权访问令牌的各方之间共享信息。它们既不是*注册的*也不是*公开的*，可以是你想让它们成为的任何东西。

在上面的示例令牌中，反序列化后的有效负载如下所示:

```
{
 "ver": 1,
 "jti": "AT.u_OOxGzWwTcDYlxfpp5X_3quR0vRnsnXmwLfWtL1cto",
 "iss": "https://dev-819633.oktapreview.com/oauth2/default",
 "aud": "api://default",
 "iat": 1546726228,
 "exp": 1546729974,
 "cid": "0oaiox8bmsBKVXku30h7",
 "scp": [
  "customScope"
 ],
 "sub": "0oaiox8bmsBKVXku30h7"
} 
```

如您所见，定义了各种声明，客户端和/或服务器可以使用这些声明来“了解”更多关于用户的信息。

### JSON Web 令牌签名

JWT 签名字段是通过获取编码的报头、编码的有效载荷、密钥，并使用报头中指定的算法对这些值进行加密签名来创建的。

例如，如果您使用标准的对称 HMAC SHA256 算法，签名将通过计算创建:

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
) 
```

发行者(通常是 web 服务器)使用这个签名字段来验证令牌的完整性，并确保它没有被第三方篡改或编辑。

可以使用 [jsonwebtoken.io](https://www.jsonwebtoken.io) 来摆弄 jsonwebtoken，并对其进行编码和解码！

## 节点+快递中的令牌认证

有许多方法可以将令牌身份验证集成到节点应用程序中。你可以使用像 *Passport* 这样的认证中间件，或者自己手动实现。

### 用护照令牌认证

[Passport](http://www.passportjs.org) 是一个流行的节点应用认证中间件。它非常灵活和模块化，可以不引人注目地放入任何基于 Express 的 web 应用程序中。它有一套全面的策略(认证机制)，支持使用*用户名和密码*、*脸书*、 *Twitter* 等进行认证。

如果你想开始用 Passport 处理令牌认证，我建议你查看一下官方文档。

### 手动将令牌认证添加到您的 Node + Express 应用中

如果您不想使用 Passport，或者如果您正在构建一个不是基于 connect 风格的中间件的应用程序，您可能需要自己实现令牌认证。

一个有用的工具是[nJWT](https://github.com/jwtk/njwt)——一个流行的 JavaScript 库，用于直接处理 JSON Web 令牌。如果你想了解更多关于如何使用 nJWT 的知识，我们已经写了一篇关于主题[的文章，你应该看看](https://developer.okta.com/blog/2018/11/13/create-and-verify-jwts-with-node)。

## 在 Node + Express 中搭建一个使用 Okta 进行令牌认证的简单 App

如果您想了解如何在 Node 中使用令牌认证构建一个真正的应用程序，请继续阅读。我将带您构建一个小型的基于 Express 的 Node 应用程序，并使用令牌认证来保护它(由 [Okta 的免费 API 服务](https://developer.okta.com/)提供支持)。

如果你还不熟悉 [Okta](https://developer.okta.com/) :这是一个简单的 API 服务，用于存储用户账户和管理用户认证和授权。

首先，请前往[https://developer.okta.com/](https://developer.okta.com/)创建一个账户，或者如果你已经注册，请登录。对开发者来说是免费的。

按照以下步骤在 Okta 中创建一个应用程序。完成这些之后，我将带您构建节点应用程序，并插入 Okta 应用程序来管理您的用户凭证和令牌认证。

1.  一旦你进入 Okta 仪表板，你会在屏幕的右上角看到一个 *Org URL* 值。保存该值以备后用，然后点击导航菜单上的**应用**。
2.  点击**添加应用**。
3.  选择**网页**，然后点击*下一步*。
4.  输入以下设置，然后点击**完成**。

[![okta create app](img/5d9186ddb77f2a6dd6cb8422ffa430d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BGsAXwny--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-token-auth/okta-create-app-aa44e824d222004526f3e468cd5519448b5263644cea1bbfbad2a0bdc0af79e3.png)

您将被重定向到**常规设置**页面。点击**编辑**，然后选中**客户凭证**的复选框(确保选中)，点击**保存**。

[![okta app settings](img/5ff075d1d526a2dd4f77928662899e50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1La5EOTy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-token-auth/okta-app-settings-a7888f8c04a81f22d20bb186faf46c994605cdc3e197f8f9659a7515eda2baec.png)

当你向下滚动时，你应该看到**客户端 ID** 和**客户端秘密**，保存这些信息以备后用。

### 添加自定义范围

范围定义并限制令牌授予的访问权限。您必须在 Okta 的授权服务器中定义自定义范围。为此:

1.  从导航菜单中选择 **API** ，然后点击**授权服务器**。
2.  点击**默认**链接。
3.  点击**示波器**菜单。
4.  点击**添加范围**。
5.  输入`customScope`作为名称，并添加描述，然后点击**创建**。

[![okta create scope](img/0031fad68dfcbaef1248a1f30711506c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aMGNs-TP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-token-auth/okta-create-scope-73de7e7ab61c55426e9b816406e609864fd4b431c2f8fbacfbbf3066318710df.png)

### 安装 HTTPie

[HTTPie](https://httpie.org) 是一个用户友好的命令行 HTTP 客户端。您将在本演示的其余部分需要它，所以如果您还没有安装，请继续安装。

### 请求 JWT

现在，我将带您从 Okta 请求一个访问令牌(一个 JWT ),稍后您将使用它对您的节点 API 进行身份验证。

首先，您需要从先前(您的客户端凭证)获取**客户端 ID** 和**客户端秘密**值，并将它们交换为令牌。为此，您必须向 Okta OAuth 服务器发出 OAuth 请求。

其工作方式是，您需要创建一个包含 HTTP 授权头的请求，如下所示:

`Authorization: Basic Base64Encode(<yourClientId>:<yourClientSecret>)`

对你的**客户 ID** 和**客户秘密**(用一个`:`字符连接)进行编码，创建这个头。

如果你愿意的话，可以使用 [base64encode](https://www.base64encode.org) 来手动对这些值进行 base64 编码。

一旦你这样做了，你应该有一个类似这样的头域:`Authorization: Basic MG9haW94OGJtc0JLXhIYjNjMWJITVdxVlhrdTMwaDc6MktxRQ1FaTWVhdXBvbWdCOXZiNkNPOXBtMnFjSw`

然后，您需要对您的 **Org URL** 值(您在 Okta 应用程序设置步骤中获得)加上带有标题`grant_type=client_credentials`的`/v1/token`进行 POST API 调用。

在终端中使用 HTTPie，这将看起来像:

```
http -f POST https://{yourOktaDomain}/oauth2/default/v1/token \
  'Authorization: Basic MG9haW94OGJtc0JLXhIYjNjMWJITVdxVlhrdTMwaDc6MktxRQ1FaTWVhdXBvbWdCOXZiNkNPOXBtMnFjSw' \
  grant_type=client_credentials scope=customScope 
```

您还需要提供`scope=customScope`值，否则您将看到一个错误消息:

```
{
  "error": "invalid_scope",
  "error_description": "The authorization server resource does not have any configured default scopes, 'scope' must be provided."
} 
```

假设一切正常，您应该会得到类似如下的 HTTP 响应:

```
{
  "access_token": "eyJraWQiOiJ1dURLVTMxZWRvTi0wd0xMUnl1TW1vbmtBdi1OaFEwejZhWmxjdTN5NU8wIiwiYWxnIjoiUlMyNTYifQ.eyJ2ZXIiOjEsImp0aSI6IkFULjZoZS1fbndIcmpmSHl6bjg3bUhNLWNVUnBUNTg3RVFBT2N6Ym1QRTNkSkkiLCJpc3MiOiJodHRwczovL2Rldi04MTk2MzMub2t0YXByZXZpZXcuY29tL29hdXRoMi9kZWZhdWx0IiwiYXVkIjoiYXBpOi8vZGVmYXVsdCIsImlhdCI6MTU0Njc2NDc4OCwiZXhwIjoxNTQ2NzY4Mzg4LCJjaWQiOiIwb2Fpb3g4Ym1zQktWWGt1MzBoNyIsInNjcCI6WyJjdXN0b21TY29wZSJdLCJzdWIiOiIwb2Fpb3g4Ym1zQktWWGt1MzBoNyJ9.fZCRSMASYjQqH-gnqsQ1tJa7QN8UJZ-iPT4UZE6Voq8YsWefpyjjroMlDzkSJZVRm_V47PGLrSu7sg6ranjZTTpx8f_Qk6zfDBfNTxnWpIqKXaotTE-foial9XBSMiyuArTVsbDtHBrb9EwBSqRzBmlI2uRP92bTggxGbgNMWnQukguD_pCGHiSeDN3Jy7R7EpKgSkDpRBhQXHp0Ly6cByUmjsseWEzZdCCiIVJh_m__KEoqX8vUC6xkUYdMHJ4GWH8kPb0Hcao2jkAJBSKQKose8a5vxDS-WwpWO482NyVxNDvxBgCIfn1tG-qL4Vbdxokw41o2M81MoqgdNZGHQA",
  "expires_in": 3600,
  "scope": "customScope",
  "token_type": "Bearer"
} 
```

您现在有了一个 **access_token** ，您可以使用它对您的节点应用程序进行身份验证(一旦构建完成)！

### 用 Node 和 Express 构建 API

让我们使用 Express 构建一个 API 服务器。首先创建一个`authapp`文件夹:

```
mkdir authapp
cd authapp 
```

初始化项目:

```
npm init 
```

继续按 enter 键接受所有默认设置。

安装快捷:

```
npm install express@4.16.4 
```

为 Node.js 安装 [Okta JWT 验证器，您可以用它来验证 Okta 访问令牌(由 Okta 授权服务器发布)。](https://github.com/okta/okta-oidc-js/tree/master/packages/jwt-verifier) 

```
npm install @okta/jwt-verifier@0.0.14 
```

在文件夹中创建一个`index.js`文件，然后将下面的代码复制并粘贴到该文件中:

```
const express = require('express');
const OktaJwtVerifier = require('@okta/jwt-verifier');

const clientId = "{yourClientId}";
const oktaDomain = "https://{yourOktaDomain}";

const oktaJwtVerifier = new OktaJwtVerifier({
  issuer: `${oktaDomain}/oauth2/default`,
  clientId: clientId
});

const app = express();
const port = 3000;

// public route
app.get('/api/publicInfo', (req, res) => {
  res.status(200).send('You are viewing public info');
});

// protected route
app.get('/api/profile', verifyToken, (req, res) => {
  oktaJwtVerifier.verifyAccessToken(req.token)
    .then(jwt => {
      res.send('You are viewing private profile info');
    })
    .catch(err => {
      res.sendStatus(403);
    });
});

function verifyToken(req, res, next) {
  const bearerHeader = req.headers['authorization'];

  if (bearerHeader) {
    const bearer = bearerHeader.split('  ');
    const bearerToken = bearer[1];
    req.token = bearerToken;
    next();
  } else {
    // Forbidden
    res.sendStatus(403);
  }
}

app.listen(port, () => console.log(`My App listening on port ${port}!`)) 
```

现在，让我解释一下这段代码的作用。

以`const oktaJwtVerifier = new OktaJwtVerifier`开头的行创建了一个绑定到发行者(授权服务器 URL)和客户机 ID(将使用这个授权服务器的 Okta 应用程序)的验证器实例。

然后我们创建了两条路线`/api/publicInfo`和`/api/profile`。`/api/publicInfo`是公共的，不需要令牌认证。当`/api/publicInfo`被调用时，它会以消息`You are viewing public info`响应。`/api/profile`受保护，需要令牌认证。它调用函数`verifyToken`来提取沿着 API 调用头传递的承载令牌。

以`oktaJwtVerifier.verifyAccessToken(req.token)`开头的行获取令牌并检查令牌是否有效。如果令牌有效，它将响应消息`You are viewing private profile info`，否则它将返回`403`，这意味着访问被禁止。

### 测试您的节点和 Express API

是时候测试一下了！您可以使用
启动 API 服务器

```
node index.js 
```

使用 HTTPie 向`/api/publicInfo`端点发出请求:

```
http GET :3000/api/publicInfo 
```

您应该会看到响应`You are viewing public info`。

现在尝试访问受保护的路线:

```
http GET :3000/api/profile 
```

您应该会看到响应`Forbidden`。

现在，用您之前获得的**访问令牌**再试一次(用您的令牌替换令牌部分):

```
http GET :3000/api/profile \
  'Authorization: Bearer eyJraWQiOiJ1dURLVTMxZWRvTi0wd0xMUnl1TW1vbmtBdi1OaFEwejZhWmxjdTN5NU8wIiwiYWxnIjoiUlMyNTYifQ.eyJ2ZXIiOjEsImp0aSI6IkFULjZoZS1fbndIcmpmSHl6bjg3bUhNLWNVUnBUNTg3RVFBT2N6Ym1QRTNkSkkiLCJpc3MiOiJodHRwczovL2Rldi04MTk2MzMub2t0YXByZXZpZXcuY29tL29hdXRoMi9kZWZhdWx0IiwiYXVkIjoiYXBpOi8vZGVmYXVsdCIsImlhdCI6MTU0Njc2NDc4OCwiZXhwIjoxNTQ2NzY4Mzg4LCJjaWQiOiIwb2Fpb3g4Ym1zQktWWGt1MzBoNyIsInNjcCI6WyJjdXN0b21TY29wZSJdLCJzdWIiOiIwb2Fpb3g4Ym1zQktWWGt1MzBoNyJ9.fZCRSMASYjQqH-gnqsQ1tJa7QN8UJZ-iPT4UZE6Voq8YsWefpyjjroMlDzkSJZVRm_V47PGLrSu7sg6ranjZTTpx8f_Qk6zfDBfNTxnWpIqKXaotTE-foial9XBSMiyuArTVsbDtHBrb9EwBSqRzBmlI2uRP92bTggxGbgNMWnQukguD_pCGHiSeDN3Jy7R7EpKgSkDpRBhQXHp0Ly6cByUmjsseWEzZdCCiIVJh_m__KEoqX8vUC6xkUYdMHJ4GWH8kPb0Hcao2jkAJBSKQKose8a5vxDS-WwpWO482NyVxNDvxBgCIfn1tG-qL4Vbdxokw41o2M81MoqgdNZGHQA' 
```

您现在应该会看到响应`You are viewing private profile info`:

[![token response](img/b1eeff33afec405e60cdbcc4b235da95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cgmcyd43--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-token-auth/token-response-e9ba0945796e078e7f1a287ddfcf0c8133a0f9e0ecbd23d0e4b70a74464a6b8f.png)

还有…就是这样！干得好！您已经构建了一个 Express 应用程序，它使用令牌身份验证为受保护的 API 端点提供服务。您使用 Okta 作为 OAuth 提供者，它管理您的自定义范围(和声明)、令牌等。您还使用了 JWT 验证中间件(Okta 的 JWT 验证器库),它自动为您处理验证令牌。

## 了解有关令牌认证和 Node + Express 的更多信息

我希望你在这篇文章中学到了新的东西！如果你觉得令牌认证有趣和/或有用，并想了解更多，这里有一些其他的好帖子可以看看:

*   [用 Node、React 和 Okta 建立用户注册](https://developer.okta.com/blog/2018/02/06/build-user-registration-with-node-react-and-okta)
*   如果你的 JWT 被偷了会怎么样？
*   [在 15 分钟内构建带有用户认证的 React 应用](https://developer.okta.com/blog/2017/03/30/react-okta-sign-in-widget)
*   [用 OpenID Connect 搭建一个简单的 Node.js 网站](https://developer.okta.com/blog/2017/10/19/use-openid-connect-to-build-a-simple-node-website)
*   【Java 应用的简单令牌认证

如果你对这篇文章有任何问题，请在下面添加评论。更多精彩内容，请在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，像我们一样关注[脸书](https://www.facebook.com/oktadevelopers/)，或者订阅[我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。