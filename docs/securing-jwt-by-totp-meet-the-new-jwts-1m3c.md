# 由 TOTP 保护 JWT，迎接新的 jwt

> 原文：<https://dev.to/alialp/securing-jwt-by-totp-meet-the-new-jwts-1m3c>

如果您已经在这里登陆，那么您已经对将您的应用程序的安全性提升到下一个级别感兴趣，并且很可能您已经是“Json Web Token”即 JWT 的忠实消费者。本文将向您介绍一种关于 JWT 使用的全新方法，这是两种非常著名的安全机制 JWT 和 TOTP 在其数字生活中的首次结合。

# 一期

对更安全的应用程序的需求是有限的，需要与安全利用同步，因为黑客总是领先一步:)

JWT 的设计目的不是为了携带密码等任何敏感数据，因为 JWT 的内容很容易被破译

> 生成一个 JWT 并访问 [jwt.io](https://jwt.io/) ，然后将您的令牌粘贴到调试器/编码部分以查看它的内容

> #### [哈希函数-维基百科](https://en.wikipedia.org/wiki/Hash_function)
> 
> 需要额外的引用来验证。通过增加对可靠来源的引用来改进这篇文章。未经来源的材料可能会被质疑和删除。(了解如何以及何时删除此模板消息)

但是 JWT 要求的是“数据完整性”，这意味着在令牌发出后，令牌所携带的数据不能被修改。这种信任是基于使用一些哈希算法来签署正在传送的数据，因此当它被验证时，可以再次计算哈希值，如果有任何不匹配，令牌将作为无效令牌被丢弃。

## -哈希算法不是不可逆的

虽然仍然有一些安全专家使用哈希算法来保护他们的应用程序，因为它的不可逆属性，哈希算法并不像他们一直认为的那样不可逆，为了更好地理解这一说法，让我们回顾一些场景。

> #### [MD5 -维基百科](https://en.wikipedia.org/wiki/MD5)
> 
> MD5 消息摘要算法是一种广泛使用的哈希函数，可以产生 128 位的哈希值。尽管 MD5 最初被设计用作加密散列函数，但人们发现它存在大量漏洞。它仍然可以用作校验和来验证数据完整性，但只能防止无意的损坏。

让我们假设 [MD5](https://en.wikipedia.org/wiki/MD5) 已经在一个应用
中被用于散列密码

```
MD5(“PASSWORD”) => 319f4d26e3c536b5dd871bb2c52e3178 
```

Enter fullscreen mode Exit fullscreen mode

当然，还没有可能的方法从“319 F4 d 26 E3 c 536 b5 DD 871 bb2c 52e 3178”中计算出“密码”，但是如果你有好的记忆力，到现在为止，你已经知道“319 F4 d 26 E3 c 536 b5 DD 871 bb2c 52e 3178”是“密码”的 MD5，那么当你可以记住它时，为什么还要计算它呢？

您只需生成所有可能的 MD5 密码组合，并将其保存到这样的数据库中

```
MD5(“PASSWORD”) => 319f4d26e3c536b5dd871bb2c52e3178
MD5(“password”) => 5f4dcc3b5aa765d61d8327deb882cf99
MD5(“pass”) => 1a1dc91c907325c69271ddf0c944bc72
.
.
. 
```

Enter fullscreen mode Exit fullscreen mode

生成不同密码的 MD5 等价物的所有可能组合需要非常非常长的时间，但是只需要做一次，对吗？因为“密码”的 MD5 值总是“319 F4 d 26 E3 c 536 b5 DD 871 bb2c 52e 3178”

> 记住，当某件事有可能做的时候，那么它很可能已经被做了。

你可以亲自查看一下 MD5 数据库[这里](https://md5decrypt.net/en/)

> #### [Md5 解密&加密——超过 10.000.000.000 个哈希](https://md5decrypt.net/en/)
> 
> 用 Md5 加密一个单词，或者通过与我们的在线解密器(包含 10，311，700，774 个免费的独特 Md5 散列)进行比较来解密您的散列。

因此

> **像 MD5 这样的哈希算法或者其他任何不可逆算法
> 并不是真的“不可逆”，而是“难以逆转”。**

## -服务器上的主密钥是可以计算的

找出散列数据背后的东西的另一种方法是找出用于数据散列的算法& Master_Key。在 JWT 的情况下，算法已经摆在桌面上(在这里阅读更多内容)，因此唯一缺少的是 Master_Key，有了算法和 master key 的知识，就可以生成无限的有效 jwt，而不是授权服务器。但是专家们总是会确保密钥位于一个安全的服务器上，没有人能够访问它。

当 Master_Key 不可访问时，我们可以强行重新生成它，要实现这一点，只需简单地向服务器请求一个有效的 JWT，就像这样

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvY2xhaW1zL3JvbGUiOiJhZG1pbiIsImV4cCI6MTU1MzI0ODI4NSwiaXNzIjoiaXNzdWVyIiwiYXVkIjoiYXVkaWVuY2UifQ.
wtSuqFESQjlL0igRC8OsxMK6SZIcKguL73sI-GjTE8k 
```

Enter fullscreen mode Exit fullscreen mode

然后像这样解码上面 JWT 的内容

[![](img/f84991b943311734e3e9138b6c77e0f1.png "WT.io")](https://res.cloudinary.com/practicaldev/image/fetch/s--yZ1ky81v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Al2BUf4Rov9EbF8H7lMBjVg.png)

有了 JWT 的内容和算法，一种简单的方法是尝试 Master_Key 的所有可能的组合来重新生成相同的 JWT，并且如果有足够的处理能力(超级计算机是优选的:)可用，Master_Key 可以在令牌到期之前被及时解密。

> 随着量子计算的到来，在不久的将来，处理能力将不再是问题

可以看出，除了与哈希数据解密或 JWT 的 Master_Key 暴力破解相关的处理费用，其余的都是简单且可实现的，因此需要一个更安全的 JWT 版本，这就是我设计 JWTS 或“Json Web Token Secure”的动机。

但是在进入 JWTS 之前，让我们看一下正常的 JWT 工作过程，以便有一个更好的背景。

# JWT

> #### [JWT。IO - JSON Web 令牌介绍](https://jwt.io/introduction/)
> 
> JSON Web Token (JWT)是一种简洁的、URL 安全的方式，用于表示要在双方之间传输的声明。了解更多关于它们的信息，它们是如何工作的，何时以及为什么应该使用 jwt。

下面一个正常实现的 JWT 可以看到
[![](img/0d5af6763bfd0993f60a619258bb128c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R_Tcqtok--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ApgxACJ0eqH7Bdqwhsq8B2Q.png)

C#中生成 JWT 的代码是这样的

```
var secretKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("MASTER_KEY"));
var signinCredentials = new SigningCredentials(secretKey, SecurityAlgorithms.HmacSha256);
var tokeOptions = new JwtSecurityToken(
    issuer: "issuer",
    audience: "audience",
    claims: new List<Claim>()
    {
        new Claim(ClaimTypes.Role,"admin"),
    },
    expires: DateTime.UtcNow.AddMinutes(30),
    signingCredentials: signinCredentials
);
var tokenString = new JwtSecurityTokenHandler().WriteToken(tokeOptions); 
```

Enter fullscreen mode Exit fullscreen mode

为了验证它，它是这样的

```
var validationParameters = new TokenValidationParameters
{
    ValidateIssuer = true,
    ValidateAudience = true,
    ValidateLifetime = true,
    ValidateIssuerSigningKey = true,
    ValidIssuer = "issuer",
    ValidAudience = "audience",
    IssuerSigningKey =
        new SymmetricSecurityKey(Encoding.UTF8.GetBytes("MASTER_KEY"))
};
var validator = new JwtSecurityTokenHandler();
//trying to parse the token s
var principal =
    validator.ValidateToken("eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvY2xhaW1zL3JvbGUiOiJhZG1pbiIsImV4cCI6MTU1MzI0MjE4OSwiaXNzIjoiaXNzdWVyIiwiYXVkIjoiYXVkaWVuY2UifQ.mmlPrmXQKi87bMipXoS7ITvF6VjWQqiCHZbUDePoCfY", validationParameters, _);
var role= principal.Claims.First(z => z.Type == ClaimTypes.Role).Value;
if (role.Equals("admin"))
{   
    //do admin stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

# TOTP

JWTS 使用“基于时间的一次性密码”来保护其令牌，因此有必要对 TOTP 做一些回顾。

> #### [基于时间的一次性密码算法-维基百科](https://en.wikipedia.org/wiki/Time-based_One-time_Password_Algorithm)
> 
> 基于时间的一次性密码算法(TOTP)是基于 HMAC 的一次性密码算法(HOTP)的扩展，通过从当前时间获取唯一性来生成一次性密码。

TOTP 的默认持续时间是 30 秒，这意味着将“从 UNIX 纪元(1970–01–01 00:00:00)到现在 UTC 时区的总秒数”分成“30 秒块”后，可以计算 30 秒的迭代次数，然后迭代次数将被传递给 TotpHash 函数，以生成一个 6 位数的代码，该代码将在大约 30 秒后有效。

```
public int Generate(string accountSecretKey)
{
    return TotpHash(accountSecretKey, GetCurrentCounter());
}
private long GetCurrentCounter()
{
    return (long) (DateTime.UtcNow - _unixEpoch).TotalSeconds / 30L;
}

private static int TotpHash(string secret, long iterationNumber, int digits = 6)
{
    return TotpHash(Encoding.UTF8.GetBytes(secret), iterationNumber, digits);
}
private static int TotpHash(byte[] key, long iterationNumber, int digits = 6)
{
    var bytes = BitConverter.GetBytes(iterationNumber);
    if (BitConverter.IsLittleEndian)
        Array.Reverse((Array) bytes);
    var hash = new HMACSHA1(key).ComputeHash(bytes);
    var index = hash[hash.Length - 1] & 15;
    return ((hash[index] & sbyte.MaxValue) << 24 | hash[index + 1] << 16 |
            hash[index + 2] << 8 | hash[index + 3]) % (int) Math.Pow(10.0, digits);
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，在对 JWT 和 TOTP 有了基本了解之后，是时候深入研究“Json Web Token Secure”了，因为它将被称为“JWTS”

# Json Web 令牌安全(JWTS)

您是否曾经想过用一个甚至您的应用程序都不知道的主密钥来发布一个 JWT？JWTS 是一种算法，通过结合 TOTP 和 JWT，使得 JWT 的最终安全性成为可能。

总而言之 JWTS 的工作原理如下
[![](img/ae4227457e3ce7b8439eec2246acfb7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qi9FnllB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AbUbrfNq8iyB3t7A4TcUcKw.png)

可以看出，JWTS 是 JWT 和“基于时间的一次性密码”的组合，其背后的思想是在无人知晓其签名密钥的情况下创建令牌，该签名密钥已被用于 JWT 的散列过程中。因此，即使黑客成功地破解了令牌的密钥，下一个令牌也会使用不同的密钥

## 举例

下面的例子说明了一个场景，其中客户端将尝试在两个不同的时刻从服务器获取同一个用户的两个 JWT，JWT 携带相同的数据。

[![](img/1b97f6689b82b4b80d445d62c7b95dcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XnkVhlIp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AvQwYBF5SZBjCrQA4apu-dw.png)

检索 JWT 接缝非常简单，该算法简单地使用 TOTP 算法生成一个基于时间的代码，然后用它来签署令牌，但剩下的问题将是，如何验证这个令牌，而它已签署的确切密钥是未知的？而答案是:我们会蛮力的:)

# 解

[![](img/33208dfae552ae8bd7e87668cf32a0e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Ezl3i0m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A6fRdEWDZVF5iqLm0k9dvxQ.png)

该令牌已经由 TOTP 密钥签名，该密钥每 30 秒改变一次，因此在验证该令牌时，TOTP 算法将生成不同的代码，因为该应用将处于不同的时刻，但是它的优点在于，过去生成的代码可以在未来重新生成，例如，可以重新生成由 TOTP 算法在 1 年前 1 月 1 日 00:00:00 生成的代码。

到目前为止，有可能为任何时刻重新生成 TOTP 密钥。此外，应用程序知道令牌的有效持续时间，结果，JWTS 算法只需要重新生成从“现在减去令牌的有效持续时间”到 1 分钟之后的所有代码(用于覆盖任何超时)，有了这些数据，JWTS 将尝试每个重新生成的代码作为 JWT 的主密钥来解码令牌， 如果任何代码的结果是成功的，那么这个令牌已经被属于我们项目的授权服务器签名了。

我已经为一种具有短令牌有效性的 JWT 应用程序设计了 jwt，因为通过增加令牌的有效性，算法需要尝试验证的代码数量也会增加，而且这个过程需要为每个请求执行，所以我建议小心使用:)但是如果您需要为具有长有效性的令牌使用 jwt， 可以通过改变 TOTP 迭代的次数来改变代码的数量，这意味着 TOTP 算法将例如每 10 分钟产生一个代码，而不是每 30 秒产生一个代码，因此令牌的安全性将降低，所以这是安全性强度和处理能力之间的挑战，对此的最佳判断将是应用的软件架构。

总之，如果您正在寻找一种方法来发布一个带有甚至您都不知道的密钥的令牌，请采取下一步，使用 JWTS 算法，此外，如果您感兴趣，您可以帮助我为其他平台实现 JWTS:)

你可以在这里找到 jwt[的 C#实现](https://github.com/AliTabryzy/JWTS/blob/master/dotnet/JWTS.NET/JWTS/JWTS.cs)

快乐编码:)