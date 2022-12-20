# 块堆栈认证服务器端— node.js

> 原文：<https://dev.to/blockstack/blockstack-authentication-server-side-nodejs-3em2>

*本文最初由乔治·班纳特在 Medium.com 发表[，经许可后转载。](https://medium.com/@geeogi/ef9bf3801c15)*

块堆栈身份验证流程使用户能够以完全客户端的方式进行身份验证。这样，用户可以移植他们的身份和私人数据，而不需要第三方认证。

**但是……**如果需要向服务器认证呢？用户可能需要通过第三方认证才能访问他们信任的服务。幸运的是，Blockstack 拥有支持这个用例所需的所有方法。

### **客户端调教**

该流程的前半部分与数据块堆栈指南中描述的仅客户端流程完全相同。您的应用程序将生成一个具有必要范围的 **authRequest** ，并将您的用户重定向到 blockstack 浏览器。您的用户将返回带有 **authResponse** JWT 的回调 URL，其中包含认证用户所需的所有信息。您需要将 **authResponse** 发送到您的服务器进行认证。

### **服务器设置**

**authResponse** 令牌包含认证您的用户所需的所有信息，包括唯一标识符、电子邮件地址和获取用户配置文件数据所需的 URL。

*   首先，我们可以使用**block stack . verifyauthresponse()**方法来验证 **authResponse** 是否有效
*   然后，解码 JWT 以显示用户的唯一标识符、电子邮件和个人资料 URL
*   然后，对配置文件 URL 执行 GET 请求，以检索用户名

```
\*\* FOR DEMONSTRATION PURPOSES ONLY \*\*

import request from "request-promise" import { verifyAuthResponse } from "blockstack" import { decodeToken } from "jsontokens"

async function blockstackAuth(req: Request, res: Response) { const LOOKUP\_URL = "https://core.blockstack.org/v1/names" const authResponse = req.body.authResponse let token: any try { const valid = await verifyAuthResponse(authResponse, LOOKUP\_URL) token = decodeToken(authResponse).payload if (!valid || !token) { throw new Error("invalid authResponse.") } } catch (e) { return res.status(400).send(e) } const userJSON = await request(token.profile\_url) const userToken = JSON.parse(userProfile)[0].decodedToken const userData = userToken.payload.claim const userUniqueIdentifier = token.iss const userEmail = token.email const userFullName = userData.name // Some logic relating to the now authenticated user e.g. sign-up

res.status(200).send({ message: "all good!" }) } 
```

### * *Catch —时钟偏斜**

时间敏感的 JWT 认证会遭受时钟偏移，由此发行者的时间和认证者的时间不同步。

如果您发现您的服务器认为一个块堆栈 **authResponse** 无效，这可能是因为您的服务器的时间落后于发出 **authResponse 的服务器的时间。**该* * block stack . verifyauthresponse()**方法将** **未来发行时间视为无效。

为了解决这个问题，在 Zinc，我们省略了对**block stack . verifyauthresponse()**方法** **的发布验证，以支持允许时间差异的自定义方法(或由 [JWT 标准](https://medium.com/r/?url=https%3A%2F%2Ftools.ietf.org%2Fhtml%2Frfc7519%23section-4.1.4)提供的“余地”)。我们利用 Blockstack 组件函数来重新构建我们自己的**authResponse** 验证:

```
// blockstack.js methods await blockstack.isExpirationDateValid(authResponse) await blockstack.doSignaturesMatchPublicKeys(authResponse) await blockstack.doPublicKeysMatchIssuer(authResponse) await blockstack.doPublicKeysMatchUsername(authResponse, LOOKUP\_URL)

// custom issuance date validation method await isIssuanceDateValid(authResponse, {leeway: 30}) 
```

帖子 [Blockstack 认证服务器端— node.js](https://blog.blockstack.org/blockstack-authentication-server-side-node-js/) 最先出现在 [Blockstack 博客—block stack](https://blog.blockstack.org)上。