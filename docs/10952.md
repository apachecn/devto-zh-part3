# 如何跨子域共享 Firebase 身份验证

> 原文：<https://dev.to/johncarroll/how-to-share-firebase-authentication-across-subdomains-1ka8>

这篇文章的目标读者是已经熟悉 Firebase、Firebase 认证以及正在 web 应用中使用 Firebase 的人。

如果您在 web 应用程序中使用 Firebase 身份验证，您可能会遇到 Firebase 只支持单个域身份验证的问题。这意味着，如果您的应用程序体验跨多个子域，您的用户必须分别登录到每个子域。更大的问题是，你的用户必须分别签出每个子域的*。*

如果您的应用程序跨子域共享品牌，这可能会带来安全风险。用户期望退出`app1.domain.com`也退出`app2.domain.com`可能是合理的。许多流行的应用程序共享跨子域的登录状态，例如 Google 本身。

花了很长时间，然后我打算得到跨子域的单点登录工作，我写这篇文章，以便下一个人希望有更容易。

## 在高层，这是我们的设置:

1.  我们在不同的领域有三个应用。
    *   `accounts.domain.com`
    *   `app1.domain.com`
    *   `app2.domain.com`
2.  我们有三个基本功能
    *   `...cloudfunctions.net/users-signin`
    *   `...cloudfunctions.net/users-checkAuthStatus`
    *   `...cloudfunctions.net/users-signout`

为了登录:

1.  有人导航到`accounts.domain.com`应用程序
2.  他们提供他们的认证信息
3.  该认证信息被发送到我们的`/users-signin`云函数，该函数验证该信息，如果有效，则设置一个包含用户 UID 的签名的`__session` cookie，并向客户端返回一个成功指示。
4.  成功后，客户端调用`/users-checkAuthStatus`云函数，该函数查找签名的`__session` cookie，提取用户 UID，并使用 UID 和`firebase-admin` SDK 创建一个定制的 auth 令牌，然后返回给客户端。
5.  当客户端收到这个定制的身份验证令牌时，它使用它来使用 firebase javascript SDK 进行登录。

当某人导航到某个其他应用程序时，比如说`app1.domain.com`，该应用程序首先检查这个人是否已经通过 Firebase Auth 登录。如果没有，它将调用`/users-checkAuthStatus`云函数，该函数将查找已签名的`__session` cookie，并在适当的时候向客户端返回一个定制的身份验证令牌。然后，客户端使用自定义身份验证令牌(如果存在)让用户登录。

如果`app1.domain.com`的用户没有登录并且想要登录，你将他们发送到`accounts.domain.com`，然后在登录完成后将他们重定向回`app1.domain.com`。

为了退出，客户端通过用`firebase-js-sdk`调用`signOut()`来清除本地身份验证状态，并且还调用`...cloudfunctions.net/users-signout`，这清除了`__session` cookie。此外，客户端需要通知任何其他连接的客户端用户已经注销，以便他们可以使用 firebase-js-sdk 调用`signOut()`。

## 实实在在地做事，有了保障。

这是一个高层次的概述，但是为了让它真正工作，我们需要处理一些东西，比如跨站点脚本、cookies、处理提供者身份验证等等。

### 签到

首先，您需要决定如何在服务器上验证身份验证。

一种可能是在`accounts.domain.com`客户端上对某人进行正常身份验证(使用 Firebase Auth)，然后将他们的 idToken 发送到服务器，在那里您使用 admin SDK 来验证 ID token，验证与 ID token 相关联的`issuedAtTime`(例如，确保它是在最近 5 分钟内创建的)，并验证与 ID token 相关联的提供者(例如，确保它不是使用定制的 auth token 创建的)。

另一种可能性是，如果有人通过脸书或 Twitter 等提供商进行身份验证，可以使用该提供商的 SDK 对他们进行身份验证，检索`authToken`，并将`authToken`发送到服务器，在服务器上按照提供商的说明验证令牌。

然而，您完成了向服务器传递凭证，如果服务器确定认证有效，您需要设置一个`__session` cookie，它等于与用户相关联的 Firebase 认证 UID，并设置一个跨站点脚本 cookie，我们将使用它来防止[跨站点脚本攻击](https://en.wikipedia.org/wiki/Cross-site_scripting)。

`__session` cookie 应该是`signed`、`secure`(只表示 HTTPS)和`httponly`(表示 javascript 无法访问)。跨站点脚本 cookie，我们称之为`csst`，应该是`secure`，而不是`signed`或`httponly`。相反，应该使用 [`jsonwebtoken`](https://github.com/auth0/node-jsonwebtoken) 库创建`csst` cookie，该库将对令牌进行签名并记录令牌的主题(即 auth UID)。与这两个 cookies 相关联的域应该是您的应用程序的根域(即`domain.com`)。这确保了 cookies 在子域之间共享。

浏览器不允许您为另一个域设置 cookies。这是一个问题，因为默认情况下，你的 Firebase 函数和你的应用程序在不同的域。你可以按照[这些指令](https://stackoverflow.com/a/51461847/5490505)来使用 Firebase 托管&一个自定义域来实现你的功能。还要注意，对您的函数使用 Firebase 托管意味着您被限制为[只能在服务器端](https://firebase.google.com/docs/hosting/functions#using_cookies)读取`__session` cookie(这不会影响我们对`csst`令牌的消费)。即使您设置了 firebase 函数来使用您的自定义域，您在开发过程中可能仍然会遇到麻烦:在我最初的设置中，我在本地托管我的应用程序，但是将 Firebase 函数部署到一个特殊的开发 Firebase 项目中。这意味着与函数相关联的域不是 localhost(意味着函数不能设置客户机可以看到的 cookie)。

我想到了两种解决这种情况的方法。

1.  在开发期间禁用跨站点脚本检查，并从`__session` cookie 中删除`domain`规范。这是可行的，因为无论如何`__session` cookie 只被 Firebase 函数读取，所以如果`__session` cookie 没有跨子域共享也没关系(在这种情况下，与`__session` cookie 相关联的域将是您的 Firebase 函数域)。
2.  在开发过程中在本地为您的功能服务。Firebase 在文档中有使用本地仿真器的说明。
    *   本地函数模拟器现在可以与 node 8+ ~~一起工作，本地模拟器的一个问题是它只适用于 nodejs6(仅供参考，我发现当前版本的 expressjs 不能在 nodejs 6 中工作)。~~
    *   这不再是必要的，因为本地函数仿真器现在可以与 node 8+ ~~一起工作。另一个选择是在开发期间构建自己的 express 应用程序来托管您的函数。这是路线...~~

为了设置 cookies，你需要使用一个 [`onRequest` Firebase 函数](https://firebase.google.com/docs/functions/http-events)而不是一个`onCall` Firebase 函数。你还需要处理好 CORS 和所有这些事情。我还会指出，谷歌 Chrome 有一个非常意想不到的怪癖，如果`set-cookie`头是针对不同的域，它会从响应中去掉`set-cookie`头。我*讨厌*这种怪癖。我花了几个小时以为饼干没有被设定，而事实上，它被设定了。更多信息，请参见[该 S.O .问题](https://stackoverflow.com/a/55010828/5490505)。另一个参考消息是，在执行 CORS 请求时，您需要指定该请求是通过“凭据”发出的，以便发送 cookies。服务器还需要指定凭据允许 CORS 请求接收凭据。

### 检查授权状态

总之，在客户端设置了`__session` cookie 和`csst`cookie 之后。客户端现在可以调用`/users-checkAuthStatus`端点。这样做时，**客户机**需要找到`csst` cookie，提取它的令牌，并在请求的`Authorization: Bearer ${token}`头中设置令牌。当收到请求时，`checkAuthStatus`端点提取授权头中包含的`csst`令牌，验证令牌上的签名，并确保令牌的主题与签名的`__session` cookie 中包含的 auth UID 匹配。假设一切都有效，您使用`firebase-admin` SDK 创建一个[自定义身份验证令牌](https://firebase.google.com/docs/auth/web/custom-auth)并发送给客户端。如果东西无效，清除客户端上任何旧的`__session`/`csst`cookie。

最后，当客户端接收到这些自定义身份验证令牌之一时，确保客户端使用 [`SESSION`身份验证持久性](https://firebase.google.com/docs/auth/web/auth-state-persistence)登录。这意味着 auth 状态将在页面刷新过程中保持，但是当与域相关联的每个选项卡关闭时，auth 状态将被清除。每当初始化应用程序时，您都需要:

1.  检查此人是否已经登录。
2.  如果没有，调用`/users-checkAuthStatus`端点，如果收到定制的身份验证令牌作为响应，使用它让用户登录。
    *   如果您没有收到任何消息，您就知道该用户没有登录。

### 签退

当有人退出时，客户端需要调用`/users-signout`端点，这将清除客户端上的任何`__session`/`csst`cookie，并调用 firebase-js-sdk 的`signOut()`方法。此外，你需要以某种方式 ping 任何其他打开的应用程序，让它们知道注销，这时它们应该调用 firebase-js-sdk 的`signOut()`方法来注销自己。提醒一下，如果一个应用被关闭，firebase-js-sdk 的 auth 状态已经被清除。

为了 ping 其他打开的应用程序，告诉它们退出 firebase-js-sdk，我发现最简单的方法是监控`csst` cookie 的存在。如果`csst` cookie 消失了，你就知道这个人已经退出了，你的应用程序应该调用 firebase sdk 的`signOut()`方法。

## 包装完毕

无论如何，这结束了我的概述。让 Firebase 认证跨子域工作并不简单，但是不需要做太多工作也是可行的。不幸的是，您需要熟悉许多概念，比如 CORS、cookies、jwt、Firebase 身份验证本身等等。

祝你好运！

## 编辑(5/14/21)

1.  一些人要求提供工作设置的示例(即代码)。很明显，我能理解为什么这真的很有帮助，但是我没有任何这样做的计划(也就是说，我不愿意花时间)。如果有人读了这篇文章，把一个回购的例子放在一起，并在评论中对我进行 pings，我会更新这篇文章，提供一个你的回购的链接(并表扬你)，这样其他人也可以受益。

2.  另一个开发人员提出了这种方法的变体(他们认为这是一种改进)，你可以在这里阅读，[跨域 Firebase 认证:一种简单的方法](https://dev.to/brianburton/cross-domain-firebase-authentication-a-simple-approach-337k)。我根本没有测试过这种方法，所以我分享它，但不认可它(但是有选择总是好的，对吗？).

3.  与此无关，我最近发现，您可以(相当容易地)为 Firebase Firestore 实现一个简单的查询缓存，从而提高性能并降低成本。你可以在这里看到一个概述:

    *   [https://dev . to/John Carroll/surprise-simple-method-to-incremently-increase-firestore-performance-reduce-cost-2g CG](https://dev.to/johncarroll/surprisingly-simple-method-to-increase-firestore-performance-reduce-cost-2gcg)