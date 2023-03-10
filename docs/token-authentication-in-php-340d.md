# PHP 中的令牌认证

> 原文：<https://dev.to/oktadev/token-authentication-in-php-340d>

JSON Web 令牌(jwt)已经成为移动应用程序、单页面 Web 应用程序和机器对机器通信的无状态认证的事实上的标准。由于一些关键优势，它们已经取代了传统的身份验证方法(服务器端会话):

*   它们是分散的和可移植的(您可以从一个专用服务请求一个令牌，然后在多个后端使用它)
*   不需要服务器端会话——一个 JWT 可以包含所有需要的用户信息，并且这些信息不会被修改
*   它们性能良好，易于扩展

在开始使用 jwt 之前，重要的是要理解 jwt 是编码的，而不是加密的——它们不隐藏其中包含的数据，用户可以读取这些数据。您不应该在 JWT 中存储任何敏感信息。

有许多旨在帮助您在应用程序中使用 jwt 的库。在本文中，我将首先带您使用 [firebase/php-jwt](https://github.com/firebase/php-jwt) 包构建和验证您自己的 jwt。然后，我将向您展示如何在 Okta 中创建一个机器对机器的应用程序，并使用客户端凭证流从您的 Okta 服务器获取一个 JWT 访问令牌。

完成例子的要求是:Okta 账号(免费)、PHP、Composer、`openssl`命令行工具。

## 为什么是 Okta？

[Okta](https://developer.okta.com/) 是一个 API 服务，允许你创建、编辑、安全存储用户账号和用户账号数据，并将它们与一个或多个应用连接。[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)，当你完成后，回来学习更多关于 PHP 中的令牌认证。

Okta 中有不同的身份验证流程，这取决于客户端应用程序是公共的还是私有的，以及是否有用户参与或者通信只是机器对机器的。您将实现的[客户端凭证流](https://developer.okta.com/docs/guides/implement-client-creds/-/use-flow/)最适合于机器对机器的通信，在这种通信中，客户端应用程序是私有的(并且可以被信任来保守秘密)。

## 什么是 jwt？

jwt 是 base64 编码的字符串。它们是独立的，并且是加密签名的，这意味着它们的内容可以被检查和验证。jwt 可以使用共享秘密(使用 HMAC 算法)或使用 RSA 或 ECDSA 的公钥/私钥对进行签名。如果恶意用户更改了令牌内容，JWT 将无法通过验证。

JWT 的格式为:`header.payload.signature`

头组件包含有关签名方法的信息。有效负载部分是关于用户的信息(也称为 JWT 的“声明”)。签名由认证服务器使用共享秘密或私钥来计算。

如果你想了解更多关于如何安全地使用 jwt，以及如何在 PHP 中从头开始构建和验证它们(不使用任何外部库)，你可以查看我以前的文章[用 OAuth 2.0 在 PHP 中创建和验证 jwt](https://developer.okta.com/blog/2019/02/04/create-and-verify-jwts-in-php)。

## 在 PHP 中使用 JWTs 配合 OAuth 2.0 和 OpenID 连接

在解释 jwt 在 OAuth 2.0 和 OpenID Connect 中的作用之前，澄清信息安全中身份验证和授权的概念是很重要的。

身份验证意味着确认用户就是他们声称的那个人。授权意味着证明经过身份验证的用户有权在系统中执行某些操作。

[OpenID Connect](https://openid.net/what-is-openid/) 是认证协议， [OAuth 2.0](https://oauth.net/2/) 是授权的开放标准。OpenID Connect 使用 ID 令牌，OAuth 2.0 使用访问令牌。它们一起为 web/移动应用程序和服务器中的用户(或机器)的认证和授权提供了一个完整的框架。

OAuth 2.0 令牌不一定必须是 jwt，但是许多实现(包括 Okta)都使用 jwt，因为它们具有令人满意的属性。

另一方面，OpenID 连接(OIDC)令牌总是 jwt。Okta 使用公钥/私钥对签名方法。ID 令牌使用私有 JSON Web Key (JWK)进行签名，您可以在这里找到它的规范: [JSON Web Key (JWK)](https://tools.ietf.org/html/rfc7517) 。如果您希望能够验证 Okta 令牌，您需要检索这些密钥并将它们缓存在您的服务器上(或者，您可以要求 Okta 为您验证令牌，但这需要额外的到授权服务器的往返行程)。

OIDC ID 令牌包括以下常见声明:

*   `iss` (issuer)声明与您的 Okta 授权服务器的标识符匹配
*   `aud`(受众)声明应该与用于请求 ID 令牌的客户端 ID 相匹配
*   `iat`(发布时间)声明表明该 ID 令牌是何时发布的
*   `exp`(到期时间)声明是该令牌将到期的时间
*   `nonce`声明值应该与请求 ID 令牌时传递的值相匹配

## 在 PHP 中创建和验证 jwt

您将使用`firebase/php-jwt`包来创建和验证您自己的 jwt。我还将向您展示如何使用 base64 解码来读取 JWT 的声明，并证明它是简单编码的，而不是加密的(提醒:不要在 JWT 中存储任何敏感信息)。

首先创建一个新目录，并将其初始化为一个新的 PHP 项目，依赖于`firebase/php-jwt` :

```
composer require firebase/php-jwt 
```

接下来，在命令行上使用 openssl 创建一个私有/公共密钥对:

```
openssl genrsa -out mykey.pem 1024
openssl rsa -in mykey.pem -pubout > mykey.pub 
```

您将使用这些来签名和验证您的 jwt。

在目录中创建一个包含以下内容的`.gitignore`文件，这样密钥文件和您的`.env`文件就不会被添加到 Git 存储库中:

```
/vendor/
.env
mykey.pem
mykey.pub 
```

创建一个文件`bootstrap.php`来加载供应商库:

```
<?php
require 'vendor/autoload.php'; 
```

创建一个文件`jwt-generate.php` :

```
<?php
require('./bootstrap.php');

use \Firebase\JWT\JWT;

$privateKey = file_get_contents('mykey.pem');

$token = array(
    "iss" => "http://example.org",
    "aud" => "http://example.com",
    "iat" => 1356999524,
    "nbf" => 1357000000
);

/**
 * IMPORTANT:
 * You must specify supported algorithms for your application. See
 * https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40
 * for a list of spec-compliant algorithms.
 */
$jwt = JWT::encode($token, $privateKey, 'RS256');

echo $jwt . "\n"; 
```

这段代码生成一个 JWT 并打印出来。它使用私钥和 RS256 算法对其进行签名。

从命令行运行代码，您会看到类似这样的内容:

```
php jwt-generate.php
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC9leGFtcGxlLm9yZyIsImF1ZCI6Imh0dHA6XC9cL2V4YW1wbGUuY29tIiwiaWF0IjoxMzU2OTk5NTI0LCJuYmYiOjEzNTcwMDAwMDB9.m_RtU0biD_xRy0DUVH7QWoNaLRqLWZadzpYO7tKnYbMlcRw8gYzZ9xI4aUJFf5BnyzKHY1LKmBdcU_-jotiJOZ0Ro_kRvcEPQRuu3OgLgQPTdyFHPrBRzGnQAAgfUdyHl1mKIJqe2bjdkiGDx8ShXnn3VZxpXzQLEDrH51IKuFI 
```

您将创建的下一个工具是`jwt-decode.php`，它接受一个 JWT 作为参数，并返回解码后的声明(不使用任何密钥，甚至不使用 JWT 库):

`jwt-decode.php`

```
<?php
require('./bootstrap.php');

// read a JWT from the command line
if (! isset($argv[1])) {
    exit('Please provide a key to verify');
}

$jwt = $argv[1];

list($header, $payload, $signature) = explode(".", $jwt);

$plainHeader = base64_decode($header);
echo "Header:\n$plainHeader\n\n"; 

$plainPayload = base64_decode($payload);
echo "Payload:\n$plainPayload\n\n"; 
```

如果您使用上一步中的示例键运行它，您将看到如下输出:

```
php jwt-decode.php eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC9leGFtcGxlLm9yZyIsImF1ZCI6Imh0dHA6XC9cL2V4YW1wbGUuY29tIiwiaWF0IjoxMzU2OTk5NTI0LCJuYmYiOjEzNTcwMDAwMDB9.m_RtU0biD_xRy0DUVH7QWoNaLRqLWZadzpYO7tKnYbMlcRw8gYzZ9xI4aUJFf5BnyzKHY1LKmBdcU_-jotiJOZ0Ro_kRvcEPQRuu3OgLgQPTdyFHPrBRzGnQAAgfUdyHl1mKIJqe2bjdkiGDx8ShXnn3VZxpXzQLEDrH51IKuFI

Header:
{"typ":"JWT","alg":"RS256"}

Payload:
{"iss":"http://example.org","aud":"http://example.com","iat":1356999524,"nbf":1357000000} 
```

这证明了所有的声明都可以在不使用任何密钥的情况下自由阅读。密钥用于验证签名(仅使用公钥)，这是您将构建的最后一个工具:`jwt-verify.php`

`jwt-verify.php`

```
<?php
require('./bootstrap.php');

use \Firebase\JWT\JWT;

$publicKey = file_get_contents('mykey.pub');

// read a JWT from the command line
if (! isset($argv[1])) {
    exit('Please provide a key to verify');
}

$jwt = $argv[1];

$decoded = JWT::decode($jwt, $publicKey, ['RS256']);

/*
 NOTE: This will now be an object instead of an associative array. To get
 an associative array, you will need to cast it as such:
*/
print_r((array) $decoded); 
```

同样，使用您生成的令牌从命令行运行它:

```
php jwt-verify.php eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC9leGFtcGxlLm9yZyIsImF1ZCI6Imh0dHA6XC9cL2V4YW1wbGUuY29tIiwiaWF0IjoxMzU2OTk5NTI0LCJuYmYiOjEzNTcwMDAwMDB9.m_RtU0biD_xRy0DUVH7QWoNaLRqLWZadzpYO7tKnYbMlcRw8gYzZ9xI4aUJFf5BnyzKHY1LKmBdcU_-jotiJOZ0Ro_kRvcEPQRuu3OgLgQPTdyFHPrBRzGnQAAgfUdyHl1mKIJqe2bjdkiGDx8ShXnn3VZxpXzQLEDrH51IKuFI

Array
(
    [iss] => http://example.org
    [aud] => http://example.com
    [iat] => 1356999524
    [nbf] => 1357000000
) 
```

请注意，在验证真实的 JWT 时，您还必须确保它没有过期或被列入黑名单。此外，请确保不要依赖于 JWT 中指定的算法，而是使用与签署 JWT 相同的算法！这是一个非常重要的主题，需要在下一部分中提供一些额外的细节。

## PHP 中 JWT 签名算法的安全攻击

如果您仔细观察 JWT 标题，您会注意到`alg`字段:

```
Header:
{
    "typ":"JWT",
    "alg":"RS256"
} 
```

它指定了用于签署 JWT 的算法。JWT 的所有实现都必须支持的一个特殊算法是“无”算法(根本没有签名)。如果我们修改 JWT 来指定这个算法，并且后端依赖这个字段进行验证，那么后端可能会接受我们的 JWT 为正确签名，即使我们只是编造了它！

当依赖于 JWT 中指定的算法时，还有另一种类型的攻击:如果您将算法从 RS256(使用公钥/私钥对)切换到 HS256(使用具有共享秘密的散列)，则签名将使用 HS256 算法进行验证，但使用公钥作为秘密(提示:检查`jwt_verify.php`如何工作)。因为公钥是已知的，所以您可以用它正确地签署 jwt，它们将被接受。

你可以通过阅读[攻击 JWT 认证](https://www.sjoerdlangkemper.nl/2016/09/28/attacking-jwt-authentication/)或者观看 Aaron Parecki 的演讲 [OAuth:当事情出错](https://youtu.be/H6MxsFMAoP8?t=1435)来了解更多关于这些(以及其他)攻击的信息。

结论:始终使用相同的算法对 jwt 进行签名和验证。忽略 JWT 报头中指定的算法。

## 在 PHP 中创建一个机器对机器的应用程序并验证 JWTs

在这一节中，我将向您展示如何在 Okta 中创建一个机器对机器的应用程序，以及如何使用`okta/jwt-verifier`库通过客户端凭证流从 Okta 授权服务器获取 JWT 访问令牌。

客户端凭据流最适合于机器对机器的通信(在这种通信中，可以信任客户端会保守秘密)。下面是流程的文档: [Okta:客户端凭证流程](https://developer.okta.com/authentication-guide/implementing-authentication/client-creds/)。

如果你还没有创建你的[永远免费的 Okta 开发者账户](https://developer.okta.com/signup/)，现在就创建，然后继续学习教程。

登录进入**应用**，然后点击**添加应用**:

[![Add an application](img/8f9195a28f8741607d963733ff4c1189.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gH4CyBlg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/php-token-authentication/add-application-9fe865b9bd873afff5165247c904c9c1831ca365c18ef8b45d5f758772f29756.png)

选择**服务(机器对机器)**，点击**下一步**:

[![Select Service (Machine-to-Machine)](img/e416a14e2d27714284b91b1453efbc2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zN1xCacS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/php-token-authentication/new-application-a23dfe90cf8631c68a5106ca02bb490cb578057587fa1c11841475a91f64b41e.png)

为您的应用程序输入一个标题，然后点击**完成**。记下显示在下一个屏幕上的`Client ID`和`Client secret`字段中的值，在构建应用程序时会用到它们。

在创建应用程序之前，在 Okta 中还有一件事需要配置:您需要为您的应用程序创建一个范围。

转到 **Api >授权服务器**，记下`Issuer URI`字段(配置应用程序时将需要它)，并单击* *默认的**授权服务器。进入**范围**选项卡，点击**添加范围**。像这样设置您的范围:

[![Edit scope](img/264e61fc7ab6a82e3b161e8217c1fc7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FoWrbPjk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/php-token-authentication/edit-scope-947f582b0bf7e8bdee2c6d2c11980ddbc56b3e505a7fbc840dbf85075ffb4048.png)

如果你做得正确，你应该复制 4 个值:`Client Id`、`Client Secret`、`Issuer URI`和`Scope` ( `token_auth`)。

## 使用客户端凭证流在 PHP 中生成 JWT 访问令牌

我将向您展示如何从 Okta 授权服务器为您的机器对机器应用程序获取访问令牌，以及如何验证令牌(如果由第三方接收的话)。我们将使用`okta/jwt-verifier`库。

首先创建一个`.env.example`文件，如下所示:

```
CLIENT_ID=
CLIENT_SECRET=
ISSUER=
SCOPE= 
```

然后将其复制到`.env`(记住，该文件应该在`.gitignore`中，因此不会添加到回购中)，并填写您在上一节中的详细信息。

您需要安装新的依赖项。除了`okta/jwt-verifier`库，你还需要`vlucas/phpdotenv`以便应用程序可以读取你的`.env`文件，以及`guzzlehttp/psr7`(Okta 要求)。您应该已经有了前面例子中的`firebase/php-jwt`(另一个 Okta 要求)。

运行以下命令:

```
composer require okta/jwt-verifier vlucas/phpdotenv guzzlehttp/psr7 
```

更新`bootstrap.php`文件:

```
<?php
require 'vendor/autoload.php';
use Dotenv\Dotenv;

$dotenv = Dotenv::create( __DIR__ );
$dotenv->load(); 
```

您将构建的第一个工具是`okta-jwt-get.php`(用于从 Okta 授权服务器请求访问令牌):

`okta-jwt-get.php`

```
<?php
require('./bootstrap.php');

echo "Obtaining token...";

// prepare the request
$uri = getenv('ISSUER') . '/v1/token';
$clientId = getenv('CLIENT_ID');
$clientSecret = getenv('CLIENT_SECRET');
$token = base64_encode("$clientId:$clientSecret");
$payload = http_build_query([
    'grant_type' => 'client_credentials',
    'scope' => getenv('SCOPE')
]);

// build the curl request
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $uri);
curl_setopt( $ch, CURLOPT_HTTPHEADER, [
    'Content-Type: application/x-www-form-urlencoded',
    "Authorization: Basic $token"
]);
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, $payload);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

// process and return the response
$response = curl_exec($ch);
$response = json_decode($response, true);
if (! isset($response['access_token'])
    || ! isset($response['token_type'])) {
    exit('failed, exiting.');
}

echo "success!\n";

// here's your token to use in API requests
print_r($response['access_token']);

echo "\n"; 
```

从命令行运行它，您应该得到如下输出:

```
php okta-jwt-get.php

Obtaining token...success!
eyJraWQiOiJJekZFTW5QNjdDZzhobHBBelRzYmR5WnFvQlZsbzZSaklvaUpNc2EyVG9rIiwiYWxnIjoiUlMyNTYifQ.eyJ2ZXIiOjEsImp0aSI6IkFULjdjNENXclE0d2JyUktOVzZteURnN0M3S1RDLXBtVjU5MGVIWnFyblNhdDgiLCJpc3MiOiJodHRwczovL2Rldi0zNTQ2ODUub2t0YXByZXZpZXcuY29tL29hdXRoMi9kZWZhdWx0IiwiYXVkIjoiYXBpOi8vZGVmYXVsdCIsImlhdCI6MTU1NDcyNTg0OSwiZXhwIjoxNTU0NzI5NDQ5LCJjaWQiOiIwb2FrNmkwb3V3c1lNdmJJczBoNyIsInNjcCI6WyJ0b2tlbl9hdXRoIl0sInN1YiI6IjBvYWs2aTBvdXdzWU12YklzMGg3In0.MNKyJ6cuTQO-U3g7Jlg01T1CPtRGQ3sZEnS4hlWIz3KPk4Ff243rLx76yoeZ8tE-ge2mCDZDSZQ1nsYkEZ1jjJzT-4FjrY7uTRWDI2tjrjDOj1E7SHh3ukxNG7_JuuDZJJzKs7QxEA0LkExiRjfG3BsiCTMDtWjIsR0_5HKfOE_-hwkZi2dZC3KPI_RjSCLmfok-7juqpoD9Q_8sdjRtP3TOg9HzHhCf6kcZxx5To5rtahxWNJ6den7tslkIDxXuaVRiralUcgkTo1wzNu9cB3BO6piUSYecodaUIIO4LmSF8FdTy4lBMBedrr-T2mf5RIB63KR6GBptuy86eRVUSw 
```

第二个工具是`okta-jwt-verify.php`。它接受来自命令行的访问令牌并对其进行验证。

`okta-jwt-verify.php`

```
<?php
require('./bootstrap.php');

if (! isset($argv[1])) {
    exit('Please provide a key to verify');
}

$jwt = $argv[1];

$jwtVerifier = (new \Okta\JwtVerifier\JwtVerifierBuilder())
    ->setAudience('api://default')
    ->setClientId(getenv('CLIENT_ID'))
    ->setIssuer(getenv('ISSUER'))
    ->build();

try {
    $jwt = $jwtVerifier->verify($jwt);
} catch (Exception $e) {
    exit($e->getMessage());
}

// Displays Claims as a JSON Object
print_r($jwt->toJson());

echo "\n"; 
```

使用您刚刚收到的访问令牌运行它，您应该会看到类似的输出:

```
php okta_jwt_verify.php eyJraWQiOiJJekZFTW5QNjdDZzhobHBBelRzYmR5WnFvQlZsbzZSaklvaUpNc2EyVG9rIiwiYWxnIjoiUlMyNTYifQ.eyJ2ZXIiOjEsImp0aSI6IkFULjdjNENXclE0d2JyUktOVzZteURnN0M3S1RDLXBtVjU5MGVIWnFyblNhdDgiLCJpc3MiOiJodHRwczovL2Rldi0zNTQ2ODUub2t0YXByZXZpZXcuY29tL29hdXRoMi9kZWZhdWx0IiwiYXVkIjoiYXBpOi8vZGVmYXVsdCIsImlhdCI6MTU1NDcyNTg0OSwiZXhwIjoxNTU0NzI5NDQ5LCJjaWQiOiIwb2FrNmkwb3V3c1lNdmJJczBoNyIsInNjcCI6WyJ0b2tlbl9hdXRoIl0sInN1YiI6IjBvYWs2aTBvdXdzWU12YklzMGg3In0.MNKyJ6cuTQO-U3g7Jlg01T1CPtRGQ3sZEnS4hlWIz3KPk4Ff243rLx76yoeZ8tE-ge2mCDZDSZQ1nsYkEZ1jjJzT-4FjrY7uTRWDI2tjrjDOj1E7SHh3ukxNG7_JuuDZJJzKs7QxEA0LkExiRjfG3BsiCTMDtWjIsR0_5HKfOE_-hwkZi2dZC3KPI_RjSCLmfok-7juqpoD9Q_8sdjRtP3TOg9HzHhCf6kcZxx5To5rtahxWNJ6den7tslkIDxXuaVRiralUcgkTo1wzNu9cB3BO6piUSYecodaUIIO4LmSF8FdTy4lBMBedrr-T2mf5RIB63KR6GBptuy86eRVUSw

stdClass Object
(
    [ver] => 1
    [jti] => AT.7c4CWrQ4wbrRKNW6myDg7C7KTC-pmV590eHZqrnSat8
    [iss] => https://dev-354685.oktapreview.com/oauth2/default
    [aud] => api://default
    [iat] => 1554725849
    [exp] => 1554729449
    [cid] => 0oak6i0ouwsYMvbIs0h7
    [scp] => Array
        (
            [0] => token_auth
        )

    [sub] => 0oak6i0ouwsYMvbIs0h7
) 
```

如果令牌无效或过期，您将看到一条错误消息。

这就是 JWTs 和 Okta 的客户凭证流程的要点。关于这些主题还有很多要学习的，请查看下一节的参考资料，找到一些有用的链接，以便进一步探索。

## 了解 PHP、OAuth 2.0 和 OpenID Connect 中 jwt 的更多信息

你可以在这里找到完整的代码示例: [GitHub 链接](https://github.com/oktadeveloper/okta-php-core-token-auth)

如果您想更深入地了解本文涉及的主题，以下资源是一个很好的起点:

*   [用 OAuth 2.0 在 PHP 中创建并验证 JWTs】](https://developer.okta.com/blog/2019/02/04/create-and-verify-jwts-in-php)
*   [在 5 分钟内为你的 PHP 应用添加认证](https://developer.okta.com/blog/2018/07/09/five-minute-php-app-auth)
*   [在 PHP 中构建简单的登录](https://developer.okta.com/blog/2018/12/28/simple-login-php)

喜欢你今天学到的吗？在 [Twitter](https://twitter.com/oktadev) 上关注我们，订阅我们的 [YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)获取更多精彩内容！