# 安全和 HTTP 标题

> 原文：<https://dev.to/andercodes/security--htpp-headers-30c6>

> ### “您知道大多数安全漏洞可以通过在响应报头中实现必要的报头来修复吗？”
> 
> -钱丹·库马尔

* * *

## 简介

> HTTP 头允许客户端和服务器在请求或响应中传递附加信息。HTTP 头由不区分大小写的名称、冒号“:”和值(没有换行符)组成。忽略值前的前导空格。
> - [MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)

这些头是 HTTP 通信不可分割的一部分，它们携带关于客户端浏览器、cookies、语言等的信息。

标题可以根据其上下文进行分组:

*   **通用报头**:请求和响应，但与主体中最终传输的数据无关。

*   **Request header** :关于要获取的资源或客户端本身的信息。

*   **响应头**:关于响应的信息，比如它的位置或者关于服务器本身。

*   **实体头**:关于实体主体的信息，如内容长度或 MIME 类型。

## 安全报头

这些头文件的使用是我们开发应用程序时要遵循的最佳实践的一部分。“安全标题”旨在限制现代浏览器利用漏洞。像点击劫持，XSS，MITM 等。

### HTTP 严格的运输安全(HSTS)

`Strict-Transport-Security`
是一个 HTTP 响应头，要求代理只能与 HTTPS 连接进行交互，而不能通过 HTTP 协议。

#### 保护从:

*   协议降级攻击
*   Cookie 劫持

#### 值:

| 价值 | 描述 |
| --- | --- |
| `max-age=SECONDS` | 代理应该记住该站点只能使用 HTTPS 进行交互的秒数 |
| `includeSubDomains` | 将标题应用于站点的所有子域。 |

#### 举例:

`Strict-Transport-Security: max-age=31536000 ; includeSubDomains`

#### 引用:

*   [https://en . Wikipedia . org/wiki/HTTP _ Strict _ Transport _ Security](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security)
*   [https://developer . Mozilla . org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security)
*   [https://www . owasp . org/index . PHP/HTTP _ Strict _ Transport _ Security](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security)

### 帧选项

`X-Frame-Options`
此响应头设置代理中的策略，允许或拒绝在第三方网站的 iframes 中呈现您的内容。当浏览器加载 iframes 时，将检查标题的值。

#### 保护从:

*   点击劫持

#### 值:

| 价值 | 描述 |
| --- | --- |
| `deny` | 框架内无渲染 |
| `isameorigin` | 如果原点不匹配，则不渲染 |
| `allow-from: DOMAIN` | 如果从域中加载帧，则允许渲染 |

#### 举例:

`X-Frame-Options: deny`

#### 参考文献:

*   [https://www.owasp.org/index.php/Clickjacking](https://www.owasp.org/index.php/Clickjacking)
*   [https://developer . Mozilla . org/en-US/docs/Web/HTTP/X-Frame-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/X-Frame-Options)
*   [https://blogs . msdn . Microsoft . com/ie internals/2010/03/30/fighting-click jacking-with-x-frame-options/](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/)

* * *

### XSS 保护

`X-XSS-Protection`
该保护头可以设置值来禁用保护或阻挡反射性 XSS 攻击。这是一个 XSS 过滤器。

#### 保护从:

*   XSS 袭击

#### 值:

| 价值 | 描述 |
| --- | --- |
| `0` | 过滤器禁用。 |
| `1` | 启用过滤器，浏览器将净化页面 |
| `1; mode=block` | 启用过滤器后，浏览器将阻止呈现页面 |

#### 举例:

`X-XSS-Protection: 1; mode=block`

#### 参考文献:

*   [https://www . owasp . org/index . PHP/Cross-site _ Scripting _(XSS)](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))
*   [https://developer . Mozilla . org/es/docs/Web/HTTP/Headers/X-XSS-Protection](https://developer.mozilla.org/es/docs/Web/HTTP/Headers/X-XSS-Protection)

### 内容类型

`X-Content-Type-Options`
这个头防止浏览器嗅探 MIME 类型。客户端浏览器将拒绝 MIME 类型不正确的响应，最大限度地降低上传内容被视为动态 HTML 文件的风险。

#### 保护从:

*   基于 MIME 类型混淆的攻击
*   “执行”任意 HTML

#### 值:

| 价值 | 描述 |
| --- | --- |
| `nosniff` | 防止浏览器嗅探 MIME 类型。浏览器会相信服务器所说的，如果是错误的，就会阻止资源。 |

#### 举例:

`X-Content-Type-Options: nosniff`

#### 参考文献:

*   [https://en.wikipedia.org/wiki/Media_type](https://en.wikipedia.org/wiki/Media_type)
*   [https://blog . fox-it . com/2012/05/08/mime-snughing-feature-or-vulnerability/](https://blog.fox-it.com/2012/05/08/mime-sniffing-feature-or-vulnerability/)
*   [https://developers . Google . com/web/updates/2018/07/site-isolation](https://developers.google.com/web/updates/2018/07/site-isolation)

* * *

### 内容安全策略

`Content-Security-Policy`
此标题有助于防止 JavaScript、CSS、插件等恶意注入。没有一个特定的攻击，这个头可以阻止，但它可以防止有人把任何恶意的你的应用程序可以运行。

#### 保护从:

*   XSS
*   点击劫持
*   交叉部位注射

#### 值:

CSP 可以采用的值有很多。请参考 [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)了解详细信息。

#### 举例:

`Content-Security-Policy: script-src 'self'`

#### 参考文献:

*   [https://content-security-policy.com/](https://content-security-policy.com/)
*   [https://developer.mozilla.org/en-US/docs/Web/Security/CSP](https://developer.mozilla.org/en-US/docs/Web/Security/CSP)
*   [https://www.owasp.org/index.php/Content_Security_Policy](https://www.owasp.org/index.php/Content_Security_Policy)

#### 特性策略

`Feature-Policy`
该标题可以启用或禁用各种浏览器功能。如照相机、全屏、麦克风等。

#### 保护从:

*   使用浏览器功能的第三方脚本。

#### 值:

| 价值 | 描述 |
| --- | --- |
| `accelerometer` | 禁用浏览器加速计 |
| `autoplay` | 禁用播放器中的自动播放 |
| `camera` | 禁止使用相机 |
| `microphone` | 禁用麦克风 |
| `usb` | 禁止访问 usb 设备 |
| `vibrate` | 禁用振动功能 |

#### 举例:

`Feature-Policy: vibrate 'none'`

#### 参考文献:

*   [https://developer . Mozilla . org/en-US/docs/Web/HTTP/Headers/Feature-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Feature-Policy)
*   [https://Scott helme . co . uk/a-new-security-header-feature-policy/](https://scotthelme.co.uk/a-new-security-header-feature-policy/)

* * *

## 结论

HTTP 头不会使你的应用程序免受攻击。但是它们可以提供一个我们绝对不能忽视的重要安全层。您现在就可以实现它，无需任何成本和最少的努力。这里列出了一些流行的库，可以帮助你在应用中设置安全的头文件。

*   【Ruby 的安全头
*   [PHP 安全头](https://github.com/aidantwoods/SecureHeaders)
*   [node . js 头盔](https://github.com/helmetjs/helmet)
*   [Go 的 secureheader】](https://github.com/kr/secureheader)

*摄影:薇薇安·梅耶*