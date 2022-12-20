# 用 OAuth 2.0 在 PHP 中创建和验证 jwt

> 原文：<https://dev.to/oktadev/create-and-verify-jwts-in-php-with-oauth-2-0-5713>

JSON Web 令牌(jwt)允许您实现无状态身份验证(不使用服务器端会话)。jwt 使用秘密密钥进行数字签名，可以包含关于用户的各种信息:JSON 格式的身份、角色、权限等。这些信息只是简单的编码而不是加密。然而，由于数字签名，在没有访问密钥的情况下，有效载荷不能被修改。

jwt 是一个相对热门的话题，因为它们被广泛使用(尤其是在单页面应用程序和 REST APIs 中)，但许多开发人员并不十分了解它们。在这篇文章中，我将讨论 jwt 是什么，它们解决什么问题，它们如何工作，以及如何安全地使用它们。然后，我将带您从头开始用 PHP(没有任何外部库)创建和验证 jwt 的过程。最后，我将展示如何使用 Okta 的 JWT 库来自动处理 Okta JWTs 的验证。Okta 是一个 API 服务，允许您创建、编辑和安全地存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)，当你完成后，回来学习更多关于 jwt 的知识。

## 关于用户认证的大秘密

在互联网黑暗的过去，有基于会话的认证。用户可以登录，如果服务器接受了他们的凭证，就会为他们创建一个会话(在文件中、在数据库中或者在内存中的键值数据存储中，比如 Memcached 或 Redis)。然后服务器会发回一个包含`SESSION_ID`的 cookie。用户的浏览器将为每个后续请求提供 cookie，服务器将知道用户的身份，而无需不断询问用户名和密码。

这种方法有一些缺点:例如，如果您想要水平扩展，您将需要一个用于会话的中央存储系统，这是一个单点故障。然而，让我告诉你一个大秘密:会话在过去是有效的，并且对于大多数用例来说仍然有效。如果你只有一个简单的网站，用户注册，然后登录，然后点击和做一些事情，服务器端会话是完美的。默认情况下，所有现代 Web 框架仍然以这种方式运行。如果您感兴趣的话，您甚至可以通过简单的会话获得 jwt 的所有加密优势。

然而，如果您正在构建支持机器对机器或客户机-服务器通信的 API 服务(如单页应用程序或移动应用程序)，jwt 就很有意义。如果一个请求涉及两方以上，或者如果您正在实现单点登录/联合登录系统，那么它们也是有意义的。

## jwt 如何工作

身份验证系统必须提供登录端点。用户将其凭据发送到登录系统(可以是第三方登录)。成功登录后，服务器会创建一个 JWT 并发送给客户端。客户端应用程序必须存储这个 JWT，并在每个后续的 API 调用中传递它。服务器可以使用 JWT 来验证 API 调用来自授权用户。认证系统能够仅使用秘密密钥来验证 JWT 及其有效载荷的完整性(无需任何数据库调用或网络操作)。

jwt 可以使用秘密(使用 HMAC 算法)或使用 RSA 或 ECDSA 的公钥/私钥对进行签名。

理解 jwt 不隐藏或掩盖它们持有的数据是非常重要的。有效载荷是普通的编码，没有加密，用户可以读取它(所以不要在那里存储任何敏感的东西)。只有签名被加密，并且可以被认证服务器用来验证令牌中的信息没有被修改。

## 安全存放和使用 jwt

客户端应用程序应该存储 JWT，并将它与每个请求一起发送给 API。如果令牌被盗，只要令牌有效，恶意第三方就可以冒充合法用户。因此，采取一切可能的措施来保证令牌的安全是至关重要的。

有两种存储令牌的标准方法:在浏览器的本地/会话存储中，或者在 cookie 中。以下是决定选择哪个选项时的主要风险和注意事项:

中间人攻击——您需要确保应用程序仅在 https 上工作，因此不可能通过拦截流量(例如在公共 wi-fi 网络中)来嗅探令牌。

跨站点脚本(注入 JavaScript，XSS)攻击–本地/会话存储可通过 JavaScript API 访问，这使其容易受到 XSS 攻击(如果黑客能够成功执行 XSS 攻击，允许他们在访问您的网站时在目标浏览器中运行自己的 JavaScript，本地/会话存储及其所有令牌都会受到损害)。完全保护一个网站免受 XSS 攻击并不总是一件小事，尤其是如果这个网站是基于用户生成的内容。因此，通常最好将令牌存储在 cookie 中。

跨站点请求伪造(CSRF)攻击–为 cookie 设置仅 https 标志消除了 XSS 攻击或中间人攻击的风险(因为这些 cookie 对 JavaScript 不可用，或者通过不安全的连接)。尽管如此，你仍然需要应对 CSRF 的风险。有不同的方法可以做到这一点——一个特别有效的方法是使用`SameSite=Strict` cookie 属性。大多数现代 Web 应用程序框架也包括一些默认的方式来处理 CSRF。

关于 JWTs 安全性，我想讨论的最后一个话题是如何撤销用户的访问权限(例如，用户通知您他们的令牌受到威胁，因此您想强制他们重新登录，或者用户被禁止访问您的网站，您想立即限制他们的访问)。

没有简单的答案，因为 jwt 的无状态特性——它们是自给自足的，并且(理论上)应该包括关于用户权限的所有必要信息，而无需咨询外部资源。这意味着您不能强制它们过期，因此您必须保持它们的过期时间较短(通常为 15 到 60 分钟，并使用刷新令牌，这些令牌在服务器端被跟踪，并在重新发布访问令牌之前验证其有效性)。如果您绝对必须能够立即踢用户，那么您必须在后端跟踪每个访问令牌，并验证它没有在每个请求上被列入黑名单——但是这种方法失去了 JWTs(无状态身份验证)的主要优势，并且您又回到了一个危险地接近服务器端会话的解决方案。

总而言之，下面是处理 jwt 的安全方法:

*   用一个强密钥来签署你的令牌，并保持它们的低到期时间。
*   将它们存储在仅支持 https 的 cookies 中。
*   如果不影响应用程序的功能，使用`SameSite=strict` cookie 属性。
*   如果`SameSite=strict`不适合你，使用你的 Web 应用框架的默认方式来处理 CSRF。
*   构建自己的 CSRF 令牌和后端代码来验证每个表单请求，如果您不幸使用了不能开箱即用处理 CSRF 的框架。
*   在信任 JWT 中的任何信息之前，请始终在服务器端验证签名。

## JWT 的结构

现在让我们开始处理 jwt 的本质细节。定义:

“JSON Web 令牌(JWT)是一个 JSON 对象，它在 RFC 7519 中被定义为一种在双方之间表示一组信息的安全方式。令牌由报头、有效载荷和签名组成。”

所以一个 JWT 就是这样一个格式的字符串:

```
header.payload.signature 
```

JWT 的报头部分包含关于应该如何计算 JWT 签名的信息。

JWT 的有效载荷部分是存储在 JWT 中的用户信息(也称为 JWT 的“声明”)。

签名是这样计算的:

```
data = base64urlEncode(header) + "." + base64urlEncode(payload)
hashedData = hash(data, secret)
signature = base64urlEncode(hashedData) 
```

这个秘密必须只有认证服务器(以及提供 API 的应用服务器，如果它不同于认证服务器的话)知道。

## 使用 PHP 从头开始创建和验证 jwt

我们将通过创建一个`/src`目录和一个简单的`composer.json`文件来启动一个新的 PHP 项目，这个文件只有一个依赖项(目前):DotEnv 库，它将允许我们在代码库之外的`.env`文件中保存我们的密钥:

`composer.json`

```
{  "require":  {  "vlucas/phpdotenv":  "^2.4"  },  "autoload":  {  "psr-4":  {  "Src\\":  "src/"  }  }  } 
```

我们还配置了一个 PSR-4 自动加载器，它会自动在`/src`目录中寻找 PHP 类。

我们现在可以安装我们的依赖项了:

```
composer install 
```

我们有一个`/vendor`目录，并且安装了 DotEnv 依赖项(我们也可以使用我们的自动加载器从`/src`加载我们的类，而不需要`include()`调用)。

让我们为我们的项目创建一个包含两行的`.gitignore`文件，因此`/vendor`目录和我们的本地`.env`文件将被忽略:

`.gitignore`

```
/vendor
.env 
```

接下来，我们将创建一个包含一个变量的`.env.example`文件:`SECRET`来保存我们的密钥(在生成和验证 jwt 时使用):

`.env.example`

```
SECRET= 
```

我们将把`.env.example`复制到`.env`,在这里我们将填入我们实际的密钥(它将被 Git 忽略，所以它不会出现在我们的存储库中)。

我们需要一个`bootstrap.php`文件来加载我们的环境变量(稍后它还会为我们的项目做一些额外的引导)。

`bootstrap.php`

```
<?php
require 'vendor/autoload.php';
use Dotenv\Dotenv;

$dotenv = new DotEnv( __DIR__ );
$dotenv->load(); 
```

让我们创建一个简单的工具`generate_key.php`，它将为我们生成一个密钥，所以我们可以把它放在`.env`文件中:

`generate_key.php`

```
<?php

$secret = bin2hex(random_bytes(32));
echo "Secret:\n";
echo $secret;
echo "\nCopy this key to the .env file like this:\n";
echo "SECRET=" . $secret . "\n"; 
```

如果您在命令行中运行它，您应该得到这样的输出:

```
$ php generate_key.php

Secret:
7c32d31dbdd39f2111da0b1dea59e94f3ed715fd8cdf0ca3ecf354ca1a2e3e30
Copy this key to the .env file like this:
SECRET=7c32d31dbdd39f2111da0b1dea59e94f3ed715fd8cdf0ca3ecf354ca1a2e3e30 
```

按照说明将您的秘密密钥添加到`.env`文件中(不要担心，上面例子中的密钥在任何地方都不会用到)。

接下来，我们将构建一个工具来生成示例 jwt(带有硬编码的有效负载，您可以随意修改)。首先，我们将向我们的`bootstrap.php`文件添加一个`base64UrlEncode()`函数:

`bootstrap.php`

```
<?php
require 'vendor/autoload.php';
use Dotenv\Dotenv;

$dotenv = new DotEnv( __DIR__ );
$dotenv->load();

// PHP has no base64UrlEncode function, so let's define one that
// does some magic by replacing + with -, / with _ and = with ''.
// This way we can pass the string within URLs without
// any URL encoding.
function base64UrlEncode($text)
{
    return str_replace(
        ['+', '/', '='],
        ['-', '_', ''],
        base64_encode($text)
    );
} 
```

下面是`generate_jwt.php`工具:

`generate_jwt.php`

```
<?php
require 'bootstrap.php';

// get the local secret key
$secret = getenv('SECRET');

// Create the token header
$header = json_encode([
    'typ' => 'JWT',
    'alg' => 'HS256'
]);

// Create the token payload
$payload = json_encode([
    'user_id' => 1,
    'role' => 'admin',
    'exp' => 1593828222
]);

// Encode Header
$base64UrlHeader = base64UrlEncode($header);

// Encode Payload
$base64UrlPayload = base64UrlEncode($payload);

// Create Signature Hash
$signature = hash_hmac('sha256', $base64UrlHeader . "." . $base64UrlPayload, $secret, true);

// Encode Signature to Base64Url String
$base64UrlSignature = base64UrlEncode($signature);

// Create JWT
$jwt = $base64UrlHeader . "." . $base64UrlPayload . "." . $base64UrlSignature;

echo "Your token:\n" . $jwt . "\n";
?> 
```

您可以从命令行运行该工具，得到如下输出:

```
$ php generate_jwt.php
Your token:
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxLCJyb2xlIjoiYWRtaW4iLCJleHAiOjE1OTM4MjgyMjJ9.XDGnRBphMkjjAEjw0fF7_w2oRODEI3rUhzwd2GqmE7I 
```

然后，您可以在 [https://jsonwebtoken.io](https://jsonwebtoken.io) 检查令牌，查看头部和有效负载，并确认它们与示例匹配。

我们将构建的下一个工具将允许您验证由`generate_jwt`工具创建的 jwt(通过验证到期时间和签名)。我们将使用 Carbon 来帮助我们计算到期时间，所以让我们添加库:

```
composer require nesbot/carbon 
```

以下是验证脚本:

`validate_jwt.php`

```
<?php
require 'bootstrap.php';
use Carbon\Carbon;

// get the local secret key
$secret = getenv('SECRET');

if (! isset($argv[1])) {
    exit('Please provide a key to verify');
}

$jwt = $argv[1];

// split the token
$tokenParts = explode('.', $jwt);
$header = base64_decode($tokenParts[0]);
$payload = base64_decode($tokenParts[1]);
$signatureProvided = $tokenParts[2];

// check the expiration time - note this will cause an error if there is no 'exp' claim in the token
$expiration = Carbon::createFromTimestamp(json_decode($payload)->exp);
$tokenExpired = (Carbon::now()->diffInSeconds($expiration, false) < 0);

// build a signature based on the header and payload using the secret
$base64UrlHeader = base64UrlEncode($header);
$base64UrlPayload = base64UrlEncode($payload);
$signature = hash_hmac('sha256', $base64UrlHeader . "." . $base64UrlPayload, $secret, true);
$base64UrlSignature = base64UrlEncode($signature);

// verify it matches the signature provided in the token
$signatureValid = ($base64UrlSignature === $signatureProvided);

echo "Header:\n" . $header . "\n";
echo "Payload:\n" . $payload . "\n";

if ($tokenExpired) {
    echo "Token has expired.\n";
} else {
    echo "Token has not expired yet.\n";
}

if ($signatureValid) {
    echo "The signature is valid.\n";
} else {
    echo "The signature is NOT valid\n";
}
?> 
```

如果你想验证一个 JWT，你可以通过命令行:

```
$ php validate_jwt.php eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxLCJyb2xlIjoiYWRtaW4iLCJleHAiOjE1OTM4MjgyMjJ9.XDGnRBphMkjjAEjw0fF7_w2oRODEI3rUhzwd2GqmE7I 
```

您应该得到这样的输出:

```
Header:
{"typ":"JWT","alg":"HS256"}
Payload:
{"user_id":1,"role":"admin","exp":1593828222}
Token has not expired yet.
The signature is valid. 
```

您可以通过更改令牌的到期时间、在生成和验证令牌之间更改密钥、修改有效负载而不重新生成签名等方式进行试验。

这就是构建和验证 jwt 的全部内容。当然，你很少需要自己去做，因为有很多库可以做这个。

## 在 PHP 中使用 jwt 进行访问令牌

Okta 使用 JWT 访问令牌来实现 Oauth 2.0。它们使用私有 JSON Web 密钥(JWK)进行签名。

验证访问令牌的高级概述如下:

*   检索并解析 Okta JSON Web Keys (JWK ),应用程序应该定期检查并缓存这些键
*   解码访问令牌，它是 JSON Web 令牌格式的
*   验证用于签署访问令牌的签名
*   验证访问令牌中的声明

Okta 为 PHP 提供了一个库(Okta JWT 验证器),它可以集成到任何 PHP 项目中，以提供对 Okta 访问令牌的无缝验证。

## JWT 督是如何工作的

Okta JWT 验证器可以通过 composer 安装:

```
composer require okta/jwt-verifier 
```

该库需要一个 JWT 库和一个 PSR-7 兼容库。您可以像这样安装一个现有的:

```
composer require spomky-labs/jose guzzlehttp/psr7 
```

或者，您也可以提供自己的实现。要创建自己的适配器，只需在自己的类中实现`Okta/JwtVerifier/Adaptors/Adaptor`。

## 了解关于 PHP、JWTs 和安全认证的更多信息

你可以在这里找到完整的代码示例: [GitHub 链接](https://github.com/oktadeveloper/okta-php-core-jwt-example)

如果您想更深入地了解本文涉及的主题，以下资源是一个很好的起点:

*   [验证访问令牌](https://developer.okta.com/authentication-guide/tokens/validating-access-tokens)
*   [PHP+Okta 入门](https://developer.okta.com/code/php/)
*   [奥克塔 JWT 督库](https://github.com/okta/okta-jwt-verifier-php)
*   如果你的 JWT 被偷了会怎么样？
*   [在 5 分钟内为你的 PHP 应用添加认证](https://dev.to/oktadev/add-authentication-to-your-php-app-in-5-minutes-58h1-temp-slug-5508529)

喜欢你今天学到的吗？在 [Twitter](https://twitter.com/oktadev) 上关注我们，在[脸书](https://www.facebook.com/oktadevelopers)上喜欢我们，在 [LinkedIn](https://www.linkedin.com/company/oktadev/) 上关注我们，并订阅我们的 [YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)以获取更多精彩内容！