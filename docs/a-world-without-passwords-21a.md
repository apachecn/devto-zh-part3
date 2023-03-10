# 没有密码的世界

> 原文：<https://dev.to/yashints/a-world-without-passwords-21a>

让我们面对现实吧，这年头没有密码我们是活不下去的。事实上，我们的整个网络生活都依赖于它们。但是有了密码，不仅给用户，也给我们开发者带来了一大堆问题。

# 惨痛的事实

大多数人都有一个弱密码(很可能是相同的)，用于他们所属的许多网站。社交媒体、电子邮件、生产力工具，凡是你能想到的，它们都有不同的密码政策，大多数用户都会找到一种方法，使用弱密码或写在便利贴上的强密码来绕过密码🤷🏽‍♂️.

事实上，81%的数据泄露都是由黑客攻击引起的，[利用被盗或弱密码](https://www.verizonenterprise.com/resources/reports/2017_dbir_en_xg.pdf)。

# 有更好的办法

但是多亏了 W3C(T2)和 T4()的巨大努力，我们不必在任何事情上都使用密码。Web 认证 API，又名`WebAuthn`是一个相当[的新规范](https://w3c.github.io/webauthn/)，它引入了一种新的无需密码的认证方式。

谷歌、微软、Yubico 和其他行业巨头都是这个规范得以广泛传播的贡献者。这个 API 允许服务器使用公钥加密代替密码来注册和验证用户。

Windows Hello，苹果的触控 ID，Yubikey 设备，都是不需要密码的不同类型的认证机制的例子。他们使用生物识别或硬件设备来实现这一点。

# 工作原理

从 1000 英尺开始，您将使用为网站生成的[密钥对(公钥和私钥)](https://en.wikipedia.org/wiki/Public-key_cryptography)，而不是使用密码。

私钥安全地存储在用户的设备上，而公钥与随机生成的凭证 ID 一起被发送到 web 服务器进行存储，并在以后用于认证该特定用户。

总的来说，WebAuthn 依赖于三个主要方面:

*   认证者(进行认证和验证的设备)
*   依赖方(web 应用程序所有者)
*   浏览器的 Web 身份验证 API

流程相当简单，第一步是注册，然后是认证。

## 注册一个凭证

与密码验证流程相同，其中 web 服务器通常为用户提供一个表单来输入他们的密码，然后将密码发送回服务器进行验证。

对于 WebAuthn，情况相当类似，但是依赖方要求公钥，而不是要求密码。

| ![Registration using WebAuthn](img/4e18443fe847094d49c857279a548d96.png) |
| --- |
| *图片来自 WebAuthn.io* |

当提示用户输入凭证时，使用`navigator.credentials.create()`:

```
const credential = await navigator.credentials.create(
  {
    publicKey: publicKeyCredentialCreationOptions,
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

如果您想知道`publicKeyCredentialCreationOptions`看起来像什么，它包含了一堆强制和可选字段:

```
const publicKeyCredentialCreationOptions = {
  challenge: Uint8Array.from(
    randomStringFromServer,
    c => c.charCodeAt(0)
  ),
  rp: {
    name: 'Google',
    id: 'accounts.google.com',
  },
  user: {
    id: Uint8Array.from('5T9AFCUZSL8', c =>
      c.charCodeAt(0)
    ),
    name: 'me@yashints.dev',
    displayName: 'Yaser',
  },
  pubKeyCredParams: [
    { alg: -7, type: 'public-key' },
  ],
  authenticatorSelection: {
    authenticatorAttachment: 'cross-platform',
  },
  timeout: 60000,
  attestation: 'direct',
} 
```

Enter fullscreen mode Exit fullscreen mode

选项中最重要的部分是挑战。它是服务器上随机生成的字节，用于防止回复攻击。

`rp`:表示依赖方，是用户试图注册的 web 服务器。

`user`:这是注册服务的最终用户。`id`用于将公钥与该用户相关联，但建议不要作为个人识别信息。

`pubKeyCredPrams`:指定服务器可接受的公钥类型。

`authenticatorSelection`:可选字段，帮助依赖方进一步限制允许注册的授权码。可能的值(`platform`像 Windows Hello，或者`cross-platform`像 Yubikey)可以在规格上找到[。](https://w3c.github.io/webauthn/#dom-publickeycredentialcreationoptions-authenticatorselection)

`timeout`:用户必须响应注册提示的时间，以毫秒为单位。

`attestation`:从认证器返回，包含可用于跟踪用户的信息。它表明该证明对该注册事件有多重要。比如用了`none`，表示服务器不关心。`indirect`表示服务器将允许匿名证明数据。`direct`表示服务器需要来自认证者的数据。一般来说，这是关于注册时用户的隐私。

从`create()`调用返回的对象包含一个公钥和其他用于验证用户的属性:

```
console.log(credential);

PublicKeyCredential {
    id: 'ADSUllKQmbqdGtpu4sjseh4cg2TxSvrbcHDTBsv4NSSX9...',
    rawId: ArrayBuffer(59),
    response: AuthenticatorAttestationResponse {
        clientDataJSON: ArrayBuffer(121),
        attestationObject: ArrayBuffer(306),
    },
    type: 'public-key'
} 
```

Enter fullscreen mode Exit fullscreen mode

## 依赖方验证注册数据

一旦公钥连同注册数据的其他属性一起被接收到服务器，[就有一个过程](https://w3c.github.io/webauthn/#registering-a-new-credential)来验证该数据。

当然，实现取决于您使用的语言，但是，如果您想知道如何实现这些步骤，这里有一些例子。Duo Labs 在 [Go](https://github.com/duo-labs/webauthn) 和 [Python](https://github.com/duo-labs/py_webauthn) 中提供了完整的实现。

## 使用 WebAuthn 进行认证

完成注册后，用户就可以通过网站的认证了。在注册过程中，用户获得了一个声明，表明他们拥有私钥。这包含使用上述私钥的签名。Web 服务器在注册过程中使用公钥来验证这一点。

身份验证流程如下所示:

| ![Authentication process](img/734135e736f728f245779357dcc879b6.png) |
| --- |
| *图片来自 WebAuthn.io* |

调用`navigator.credentials.get()`方法来生成一个断言，以证明用户拥有私钥。这将检索注册期间生成的包含签名的凭据。

```
const credential = await navigator.credentials.get(
  {
    publicKey: publicKeyCredentialRequestOptions,
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

`publicKeyCredentialRequestOptions`对象包含许多由服务器指定的强制和可选属性:

```
const publicKeyCredentialRequestOptions = {
  challenge: Uint8Array.from(
    randomStringFromServer,
    c => c.charCodeAt(0)
  ),
  allowCredentials: [
    {
      id: Uint8Array.from(credentialId, c =>
        c.charCodeAt(0)
      ),
      type: 'public-key',
      transports: ['usb', 'ble', 'nfc'],
    },
  ],
  timeout: 60000,
} 
```

Enter fullscreen mode Exit fullscreen mode

这个对象中最有趣的部分是传输属性。服务器可以选择指示它喜欢的传输方式，如 USB、NFC 和蓝牙。

返回的对象是一个`PublicKeyCredential`对象，但与之前注册的对象略有不同。

```
console.log(assertion);

PublicKeyCredential {
    id: 'SSX9lKQmbqdGtbcHDTBsvpu4sjseh4cg2TxSvr4ADSUlN...',
    rawId: ArrayBuffer(59),
    response: AuthenticatorAssertionResponse {
        authenticatorData: ArrayBuffer(191),
        clientDataJSON: ArrayBuffer(118),
        signature: ArrayBuffer(70),
        userHandle: ArrayBuffer(10),
    },
    type: 'public-key'
} 
```

Enter fullscreen mode Exit fullscreen mode

关于此对象[的更多信息，请参考规格](https://w3c.github.io/webauthn/#dom-authenticatorassertionresponse-userhandle)。

### 验证认证数据

获取断言后，它被发送到服务器进行验证。验证之后，用存储的公钥验证签名。

```
const storedCredential = await getCredentialFromDatabase(
  userHandle,
  credentialId
)

const signedData =
  authenticatorDataBytes + hashedClientDataJSON

const signatureIsValid = storedCredential.publicKey.verify(
  signature,
  signedData
)

if (signatureIsValid) {
  return 'Hooray! User is authenticated! 🎉'
} else {
  return 'Verification failed. 😭'
} 
```

Enter fullscreen mode Exit fullscreen mode

# 进一步阅读和资源

以下资源是任何进一步阅读的良好起点:

*   [Webauthn.io](https://webauthn.io) : Due Labs 创建了这个有用的资源，其中包含所有源代码和带有漂亮插图的示例用例。
*   WebAuthn 和 FIDO2 框架的发展:一篇内容丰富的博客文章，介绍了一些进展，并展示了每个主要浏览器和贡献者对该领域的兼容性。
*   [源代码](https://github.com/duo-labs/webauthn.io):这个 GitHub repo 包含了 Due Labs 为展示其能力而创建的所有源代码。
*   互联网的大日子: [W3C 标准化 WebAuthn](https://www.yubico.com/2019/03/w3c-standardizes-webauthn/)
*   Yubico 的演示站点:从最终用户的角度演示该流程

# 总结

我们看到了实现这个惊人的功能是多么简单和直接，我强烈建议如果你正在做一个绿色领域的项目，一定要考虑实现这个功能，给用户无密码生活的能力🔥👊🏻。对于目前的产品，这可能是一个转折点，通过简化流程中最重要的部分来获得更多的用户😊。