# OCSP 到底是什么？

> 原文：<https://dev.to/ecnepsnai/what-the-heck-is-ocsp-3li6>

在线证书状态协议，简称 OCSP，是 TLS 客户端(如您的 web 浏览器)检查证书是否已被吊销的一种方式。

# 证书撤销？

使用非对称加密，只要您的私钥保持私有，您的加密就不会有问题。[通常情况不是这样](https://github.com/search?l=Text&o=desc&q=-----BEGIN+RSA+PRIVATE+KEY-----&s=indexed&type=Code)，私钥最终会被暴露。

这只是证书可能被吊销的许多可能原因之一。证书颁发机构或证书持有者可以撤销他们的证书以将其标记为不可信。由于证书一旦创建就无法更改，所以使用状态检查来查看它是否被撤销。这就是 OCSP 发挥作用的地方。

# 一个基本例子

让我们来看看 dev.to 的证书，其中我们会看到 OCSP 服务器的 URL:

```
X509v3 extensions:
    Authority Information Access:
        CA Issuers - URI:http://secure.globalsign.com/cacert/cloudsslsha2g3.crt
        OCSP - URI:http://ocsp2.globalsign.com/cloudsslsha2g3 
```

Enter fullscreen mode Exit fullscreen mode

如果在您的 web 浏览器中启用了 OCSP(很可能没有，我们稍后会谈到)，当您浏览到 dev.to 时，您的浏览器使用证书的 SHA1 指纹向 ocsp2.globalsign.com/cloudsslsha2g3 发出了一个快速 HTTP 请求。Globalsign 是 dev.to 的证书的 CA，它将检查其数据库中的证书，以及它是否被撤销，并以签名的响应进行响应。

根据 OCSP 服务器的响应，浏览器可能会继续访问该站点，或者认为该证书已被吊销且不可信。

## HTTP？SHA-1？今年是哪一年？

是的，你没看错。OCSP 请求都是在老式的不安全的 HTTP 中，大多数客户端通常最终使用 SHA-1 指纹。

OCSP 在 HTTP 上运行的原因是，如果它在 HTTPS 上运行，那么我们必须检查用于 OCSP 服务器的证书的状态，然后是那个服务器，下一个服务器，等等。我们将永远陷入检查证书的循环中。OCSP 响应被签名以防止篡改。

OCSP 规范要求 SHA-1 必须是服务器和客户端所需的最低散列算法，这为使用更安全的散列算法提供了空间。然而，由于状态检查应该是快速的，TLS 客户端通常不希望陷入重试循环，试图与服务器协商算法。因此，大多数客户使用 SHA-1 的默认设置，并接受它。

# 有隐私顾虑吗？

你打赌！有了 OCSP，一个额外的第三方知道你浏览了一个通常不会知道的网站。

在上述示例中，GlobalSign 知道您在特定时间使用特定浏览器从特定 IP 访问了 dev.to。都是因为 OCSP。

幸运的是，有一个普遍实现和接受的解决方案:OCSP 装订。

使用 OCSP 装订，web 服务器本身代表您发出一个 OCSP 请求，并将它包含在连接信息中。这意味着第三方不知道当时谁连接到了那个站点，只有某人知道。

# 从 X.509 证书中获取 OCSP 网址

“证书颁发机构信息访问”扩展中证书的 OCSP URL。要获取响应者的 URL，需要获取信息访问扩展，然后遍历该扩展的每个可能的项，并定位 OCSP 对象。这是一个 IA4 ASN1 字符串值，因此您需要对其进行解码，以便将其作为常规字符串使用。

```
X509 * cert;
char * ocspURL;

AUTHORITY_INFO_ACCESS * info = X509_get_ext_d2i(cert, NID_info_access, NULL, NULL);
int len = sk_ACCESS_DESCRIPTION_num(info);
for (int i = 0; i < len; i++) {
    // Look for the OCSP entry
    ACCESS_DESCRIPTION * description = sk_ACCESS_DESCRIPTION_value(info, i);
    if (OBJ_obj2nid(description->method) == NID_ad_OCSP) {
        if (description->location->type == GEN_URI) {
            // Hold on to this URL for later use
            ocspURL = i2s_ASN1_IA5STRING(NULL, description->location->d.ia5);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 生成 OCSP 请求

OCSP 请求是一个或多个“密钥 id”，它是证书的指纹。大多数客户端通常每个请求只包含一个证书，并且对于服务器拒绝响应具有多个证书的请求没有限制。

```
X509 * certificate;
X509 * issuer;

// Passing NULL as the first parameter will use SHA-1
OCSP_CERTID * certID = OCSP_cert_to_id(NULL, certificate, issuer);
OCSP_REQUEST * request = OCSP_REQUEST_new();
OCSP_request_add0_id(request, certid);

// Hold on to request_data and len for later use.
unsigned char * request_data = NULL;
int len = i2d_OCSP_REQUEST(request, &request_data); 
```

Enter fullscreen mode Exit fullscreen mode

# 发出 OCSP 的请求

实际的请求只是一个简单的到响应者 URL 的 HTTP POST，主体是一个 ASN 编码的 OCSP 请求。

像所有 HTTP POST 请求一样，您需要提供`Content-Type`和`Accept`头。如果没有正确的头值，大多数 OCSP 服务器将拒绝响应，所以请确保您不要忘记这些:

```
Content-Type: application/ocsp-request
Accept: application/ocsp-response 
```

Enter fullscreen mode Exit fullscreen mode

*注意:这篇文章并不包括实际的 HTTP POST 请求。libCURL 很好地解决了这个问题。*

OCSP 响应包含一个外部响应对象和至少一个内部证书结果。(每个请求的证书一个结果)。

一旦从 OCSP 响应器得到响应，就将响应体解码为一个`OCSP_RESPONSE`对象。

```
const unsigned char * bytes; // Assuming this is the bytes of the HTTP response body
OCSP_RESPONSE * response = d2i_OCSP_RESPONSE(NULL, &bytes, data.length); 
```

Enter fullscreen mode Exit fullscreen mode

然后检查 OCSP 请求本身是否成功。这不*不*表示证书是否被吊销:

```
int requestResponse = OCSP_response_status(ocspResponse);
if (requestResponse != OCSP_RESPONSE_STATUS_SUCCESSFUL) {
    // Uh oh!
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦确定 OCSP 请求成功，就获取基本的响应对象，它包含所有的证书状态结果:

```
OCSP_BASICRESP * basicResp = OCSP_response_get1_basic(ocspResponse);
if (basicResp == NULL) {
    // Something bad happened!
} 
```

Enter fullscreen mode Exit fullscreen mode

最不重要的是，得到你的证书的结果！

```
int status; // If the certificate is revoked or not
int reason; // A CRL reason (if revoked)
ASN1_GENERALIZEDTIME * revocationTime; // When the certificate was revoked (if revoked)
ASN1_GENERALIZEDTIME * thisUP; // The last time this certificate was updated
ASN1_GENERALIZEDTIME * nextUP; // The time of the next update (how long you can cache until
if (!OCSP_resp_find_status(resp, certID, &status, &reason, &revocationTime, &thisUP, &nextUP)) {
    // There was a problem getting the status - probably the certificate wasnt found
} 
```

Enter fullscreen mode Exit fullscreen mode

太棒了，现在我们终于有了我们证书的结果！

# 验证证书状态

现在我们有了证书的状态，我们需要看看我们的证书是否被撤销。

对于 OCSP，证书有三种可能状态:`GOOD`、`REVOKED`或`UNKNOWN`。

*   好意味着根据这个 OCSP 响应者证书没有被撤销。不**不**意味着证书在其他情况下有效(即仍可能过期)。
*   吊销意味着证书被吊销。
*   未知意味着 OCSP 响应者不知道这个证书。您必须小心未知响应，因为许多应用程序认为未知响应是好的。

如果证书有一个`REVOKED`状态，那么您需要检查它被撤销的原因。OCSP 使用证书吊销列表(CRL)规范中定义的原因代码。原因是:

–未指定:可用于因特定代码以外的原因吊销证书。
–key compromise:用于撤销终端实体证书；它表示已知或怀疑主题的私钥或证书中验证的主题的其他方面已被破坏。
–cACompromise:用于撤销 CA 证书；它表示已知或怀疑主题的私钥或证书中验证的主题的其他方面已被破坏。
–affiliation changed:表示证书中主题的名称或其他信息已被修改，但没有理由怀疑私钥已被泄露。
–已被取代:表示证书已被取代，但没有理由怀疑私钥已被泄露。
–终止操作:表示证书不再需要用于其颁发目的，但没有理由怀疑私钥已经失密。
–privilege retracted:表示证书(公钥或属性证书)被撤销，因为该证书中包含的特权已被撤销。
–AA compromise:表示已知或怀疑属性证书中验证的 AA 的某些方面已经被破坏。

^ [来源](https://security.stackexchange.com/a/174331)

所有这些理由都很好，并且您的应用程序可能与其中任何一个都没有关系，但是有两个边缘情况使事情变得非常复杂:

*   certificateHold:表示证书颁发机构不再担保该证书，但以后可能会改变。
*   removeFromCrl:表示证书以前处于保留状态，但现在不再处于保留状态，并被视为未被吊销。

所以检查原因总是很重要的，因为即使状态被撤销，如果原因是`removeFromCrl`，那么它实际上并没有被撤销。

如果你正在寻找使用 iOS 移动设备在旅途中轻松验证你的证书，请查看我的一个项目: [TLS Inspector](https://tlsinspector.com) 。全球首个免费的 iOS 版& Libre X509 证书检查器( [GitHub](https://github.com/certificate-helper/tls-inspector) )。