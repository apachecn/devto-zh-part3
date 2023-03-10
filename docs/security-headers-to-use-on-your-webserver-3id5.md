# 在您的 web 服务器上使用的安全标头

> 原文：<https://dev.to/shostarsson/security-headers-to-use-on-your-webserver-3id5>

如果你想有很多关于信息安全的信息，一定要关注我的 [Youtube 频道](https://www.youtube.com/channel/UCloKU_jdC9dVS6xh-lJ1Jig)。

* * *

### 简介

由于**错误配置**或**缺乏保护**，数十个网站被黑。

您确实可以在 OWASP(开放 web 应用程序安全项目)的 web 服务器和服务上发现的 10 大漏洞中看到，安全错误配置是 Web 服务上最常见的漏洞原因。
这通常是由不安全的默认配置、不完整或临时配置、开放式云存储、错误配置的 HTTP 头以及包含敏感信息的冗长错误消息造成的。
不仅所有的操作系统、框架、库和应用程序都必须**安全** **配置**，而且必须及时**修补** / **升级**。

让我们深入探讨一下 it 的**弱点**和**影响**。

| 威胁代理/攻击媒介 | 安全弱点 | 影响 |
| --- | --- | --- |
| 特定应用/可利用性:3 | 流行率:3/检出率:3 | 技术:2/商务？ |
| 攻击者经常试图利用未修补的漏洞或访问默认帐户、未使用的页面、未受保护的文件和目录等，以获得对系统的未授权访问或了解。 | 安全错误配置可能发生在应用程序堆栈的任何级别，包括网络服务、平台、web 服务器、应用程序服务器、数据库、框架、自定义代码和预安装的虚拟机、容器或存储。自动扫描程序对于检测错误配置、使用默认帐户或配置、不必要的服务、遗留选项等非常有用。 | 这些缺陷经常让攻击者未经授权访问一些系统数据或功能。有时，这种缺陷会导致整个系统受损。业务影响取决于应用程序和数据的保护需求。 |

在这篇文章中，我们将讨论你应该在你的项目中使用的**头**和**配置**来保护你的服务器。
首先，我们认为**所有的请求和响应**都是通过 **https** 传输的。
和**所有来自服务器配置的日志和信息都被隐藏**。

**开始之前:**

*   在对您的配置进行任何更改之前，不要忘记备份您当前的配置。
*   另外有些标题在浏览器上可能不兼容。我鼓励您查看该页面上的[兼容性列表](#browser-support)中的浏览器兼容性。
*   要实现这些头，必须在 Apache 中启用 Mod-Headers。确保该行在`httpd.conf`文件中未被注释。

##### 让我们回顾一下我们将要涉及的 HTTP 标题列表:

1.  [X-XSS-保护](#x-xss-protection)
2.  [X 帧选项](#x-frame-option)
3.  [X 内容类型选项](#x-content-type-options)
4.  [内容-安全-政策](#content-security-policy)
5.  [推荐人-政策](#referrer-policy)
6.  [HTTP 严格的运输安全](#hsts)
7.  [复合物](#samesite)
8.  [http only](#http-only)
9.  [安全](#secure)
10.  [结论](#conclusion)
11.  [附件](#annexes)

**注:如果你想有总结以及 Apache 和 NGinx 的配置，可以去“结论”。**

* * *

### 1。x-XSS-保护

X-XSS 保护报头可以防止某种程度的 XSS(跨站点脚本)攻击。XSS 攻击使得攻击者能够将客户端脚本注入到其他用户查看的网页中。

#### 浏览器支持

| 微软公司出品的 web 浏览器 | 边缘 | 火狐浏览器 | 铬 | 旅行队 | 歌剧 | 机器人 |
| --- | --- | --- | --- | --- | --- | --- |
| eight | 。 | 纳秒 | 4+ | 。 | 。 | 。 |

#### 有 4 种方式可以配置该标题。

| 参数值 | 意义 |
| --- | --- |
| Zero | XSS 滤波器已禁用 |
| one | 如果检测到攻击，启用 xss 过滤器并杀毒页面 |
| **1；模式=阻塞** k | 如果检测到攻击，启用并阻止呈现页面的 XSS 过滤器 |
| 1;报道=[http://example.com/report_URI](http://example.com/report_URI) | 如果检测到攻击，启用 XSS 过滤器并报告违规 |

#### 我们建议实施什么:`1;mode=block`

| 服务器类型 | 如何 |
| --- | --- |
| Apache HTTP 服务器 | 在 Apache web 服务器的 httpd.conf 中添加以下条目。`Header set X-XSS-Protection "1; mode=block"`。重新启动 apache 进行验证 |
| NGinx | 在 http block 下的 nginx.conf 中添加以下内容。`add_header X-XSS-Protection "1; mode=block";`。需要重启 Nginx 才能在你的网页响应头上反映出来。 |

* * *

### 2。X-Frame-Options

X-Frame-Options 标头可防止网站出现点击劫持漏洞。通过实现这个头，你指示浏览器不要在 frame/iframe 中嵌入你的网页。

#### 浏览器支持

| 微软公司出品的 web 浏览器 | 边缘 | 火狐浏览器 | 铬 | 旅行队 | 歌剧 | 机器人 |
| --- | --- | --- | --- | --- | --- | --- |
| eight | Thirteen | Forty-seven | forty-nine | Nine point one | Thirty-nine | Four point four |

#### 有 3 种可能的方式可以配置该标题。

| 参数值 | 意义 |
| --- | --- |
| SAMEORIGIN(萨姆原点) | 内容的框架/iFrame 只允许来自同一网站源。 |
| **否认** | 防止任何域使用 frame/iframe 嵌入您的内容。 |
| 允许-从 | 仅允许在特定的 URI 上构建内容。 |

#### 我们建议实施什么:`DENY`

| 服务器类型 | 如何 |
| --- | --- |
| Apache HTTP 服务器 | 在 Apache web 服务器的 httpd.conf 中添加以下条目。`Header always append X-Frame-Options DENY`。重新启动 apache 进行验证 |
| NGinx | 在 http block 下的 nginx.conf 中添加以下内容。`add_header X-Frame-Options “DENY”;`。需要重启 Nginx 才能在你的网页响应头上反映出来。 |

* * *

### 3。x-内容-类型-选项

X-Content-Type-Options 标头通过将此标头添加到网页的 HTTP 响应中来防止 MIME 类型安全风险。让这个标题指示浏览器考虑已定义的文件类型并禁止内容嗅探。

#### 浏览器支持

| 微软公司出品的 web 浏览器 | 边缘 | 火狐浏览器 | 铬 | 旅行队 | 歌剧 | 机器人 |
| --- | --- | --- | --- | --- | --- | --- |
| eight | 。 | Fifty-one | One | 纳秒 | Thirteen | 。 |

#### 有 1 种可能方式可以配置报头。

| 参数值 | 意义 |
| --- | --- |
| **nosniff** | 考虑已定义的文件类型，不允许内容嗅探。 |

#### 我们建议实施什么:`nosniff`

| 服务器类型 | 如何 |
| --- | --- |
| Apache HTTP 服务器 | 在 Apache web 服务器的 httpd.conf 中添加以下条目。`Header set X-Content-Type-Options nosniff`。重启 apache 以激活配置，然后进行验证。 |
| NGinx | 在服务器块下的 nginx.conf 中添加以下内容。`add_header X-Content-Type-Options nosniff;`。需要重启 Nginx 才能在你的网页响应头上反映出来。 |

* * *

### 4。内容安全政策

内容安全策略通过在您的网页 HTTP 响应中实现内容安全策略(CSP)报头来防止 **XSS** 、**点击劫持**、**代码注入攻击**。
CSP 指示浏览器加载允许在网站上加载的内容。
然而，如果你实现了 CSRF，在某些框架中(如 [AngularJS](https://angular.io/guide/security#xsrf) )，浏览器会检索 CSRF cookie，并在响应中添加一个定制的头 XSRF-HEADER，以便实现一个 CSRF 预防方法。所以你必须非常小心你是如何实现这个头的。你可以在 [OWASP 网站](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.md)上找到很多预防 CSRF 的好方法。

#### 浏览器支持

| 微软公司出品的 web 浏览器 | 边缘 | 火狐浏览器 | 铬 | 旅行队 | 歌剧 | 机器人 |
| --- | --- | --- | --- | --- | --- | --- |
| Eleven | Thirteen | Forty-seven | forty-nine | Nine point one | Thirty-nine | Four point four |

#### 有 23 种可能的方式可以配置该标题。

| 参数值 | 意义 |
| --- | --- |
| base-uri | 为相对 uri 定义基本 uri。 |
| **默认-src** | 在未定义资源类型专用指令的情况下，为所有资源类型定义加载策略(后备)。 |
| 脚本-src | 定义受保护资源可以执行的脚本。 |
| 对象-src | 定义受保护的资源可以从哪里加载插件。 |
| 风格-src | 定义用户将哪些样式(CSS)应用于受保护的资源。 |
| img-src | 定义受保护资源可以从何处加载图像。 |
| 媒体-src | 定义受保护资源可以从何处加载视频和音频。 |
| 框架-src | 已弃用，由 child-src 替换。 |
| 儿童-src | 定义受保护资源可以嵌入帧的位置。 |
| 框架-祖先 | 定义受保护资源可以嵌入框架的位置。 |
| 字体-src | 定义受保护资源可以从何处加载字体。 |
| 连接-src | 使用脚本接口定义受保护资源可以加载哪个 URIs。 |
| 清单-src | 定义受保护资源可以从何处加载清单。 |
| 形式-动作 | 定义哪个 URIs 可以用作 HTML 表单元素的动作。 |
| 沙箱 | 指定用户代理应用于受保护资源的 HTML 沙箱策略。 |
| 脚本随机数 | 通过要求脚本元素上存在指定的 nonce 来定义脚本执行。 |
| 插件类型 | 通过限制可以嵌入的资源类型，定义受保护资源可以调用的插件集。 |
| 反射-xss | 指示用户代理激活或停用任何用于过滤或阻止反射跨站脚本攻击的试探法，相当于非标准 X-XSS 保护标头的效果。 |
| 阻止所有混合内容 | 防止用户代理加载混合内容。 |
| 升级-不安全-请求 | 指示用户代理使用 HTTPS 下载不安全的资源。 |
| 推荐人 | 定义用户代理必须在引用标头中发送的信息。 |
| 报告 uri(已弃用) | 指定用户代理向其发送策略违规报告的 URI。 |
| **报告人** | 指定用户代理向其发送策略违规报告的组(在报告目标标题中定义)。 |

#### 我们建议实施什么:`default-src on self with reporting enabled`

| 服务器类型 | 如何 |
| --- | --- |
| Apache HTTP 服务器 | 在 Apache web 服务器的 httpd.conf 中添加以下条目。`Header set Content-Security-Policy: default-src 'self'; report-uri http://reportcollector.example.com/collector.cgi`。重启 apache 以激活配置，然后进行验证。 |
| NGinx | 在服务器块下的 nginx.conf 中添加以下内容。`add_header Content-Security-Policy "default-src 'self';", "report-uri http://reportcollector.example.com/collector.cgi;""`。需要重启 Nginx 才能在你的网页响应头上反映出来。 |

* * *

### 5。推荐人政策

Referer-Policy HTTP 标头控制在 Referer 标头中发送的哪些 Referer 信息应该包含在所做的请求中。

#### 浏览器支持

| 微软公司出品的 web 浏览器 | 边缘 | 火狐浏览器 | 铬 | 旅行队 | 歌剧 | 机器人 |
| --- | --- | --- | --- | --- | --- | --- |
| 纳秒 | 纳秒 | Fifty | fifty-six | 纳秒 | Forty-three | 。 |

#### 有 8 种方式可以配置该标题。

| 参数值 | 意义 |
| --- | --- |
| **无推荐人** | Referer 标头将被完全忽略。 |
| 降级时无引用 | 如果没有指定策略，这是用户代理的默认行为。 |
| 起源 | 在所有情况下，只发送文档的来源作为推荐人。 |
| 起点-何时-交叉起点 | 在执行同源请求时发送完整的 URL，但在其他情况下只发送文档的来源。 |
| 同源 | 将为同一站点来源发送一个推荐人，但跨来源请求将不包含推荐人信息。 |
| 严格起源的 | 只将文档的来源作为 referrer 发送到一个先验的更安全的目的地(HTTPS->HTTPS)，但不要将其发送到一个不太安全的目的地(HTTPS->HTTP)。 |
| 交叉原点时严格原点 | 在执行同源请求时发送完整的 URL，仅将文档的源发送到先验的安全性更高的目的地(HTTPS->HTTPS)，不向安全性较低的目的地(HTTPS->HTTP)发送报头。 |
| 不安全的 url | 当执行同源或跨源请求时，发送完整的 URL(去掉参数)。 |

#### 我们建议实施什么:`no-referrer`

| 服务器类型 | 如何 |
| --- | --- |
| Apache HTTP 服务器 | 在 Apache web 服务器的 httpd.conf 中添加以下条目。`Header set Referrer-Policy "no-referrer"`。重启 apache 以激活配置，然后进行验证。 |
| NGinx | 在服务器块下的 nginx.conf 中添加以下内容。`add_header Referrer-Policy no-referrer;`。需要重启 Nginx 才能在你的网页响应头上反映出来。 |

* * *

### 6。HTTP 严格的运输安全

HTTP 严格传输安全(HSTS)是一种 web 安全策略机制，有助于保护网站免受协议降级攻击和 cookie 劫持。它允许 web 服务器声明 web 浏览器(或其他兼容的用户代理)应该只使用安全的 HTTPS 连接与它交互，而绝不通过不安全的 HTTP 协议。

#### 浏览器支持

| 微软公司出品的 web 浏览器 | 边缘 | 火狐浏览器 | 铬 | 旅行队 | 歌剧 | 机器人 |
| --- | --- | --- | --- | --- | --- | --- |
| Eleven | Thirteen | Forty-seven | forty-nine | Nine point one | Thirty-nine | Four point four |

#### 有两种方法可以配置该标题。

| 参数值 | 意义 |
| --- | --- |
| **最大年龄=秒** | 浏览器应该记住只能使用 HTTPS 访问该站点的时间(秒)。 |
| **包含子域** | 如果指定了这个可选参数，这个规则也适用于站点的所有子域。 |

#### 我们建议实施什么:`max-age=31536000; includeSubDomains`

| 服务器类型 | 如何 |
| --- | --- |
| Apache HTTP 服务器 | 在 Apache web 服务器的 httpd.conf 中添加以下条目。`Header set Strict-Transport-Security "max-age=31536000; includeSubDomains"`。重启 apache 以激活配置，然后进行验证。 |
| NGinx | 在服务器块下的 nginx.conf 中添加以下内容。`add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';`。需要重启 Nginx 才能在你的网页响应头上反映出来。 |

* * *

### 7。饼干

当接收到一个 HTTP 请求时，服务器可以发送一个 Set-Cookie 头作为响应。cookie 通常由浏览器存储，然后 cookie 在 Cookie HTTP 头中与对同一服务器的请求一起发送。可以指定过期日期或持续时间，过期后不再发送 cookie。此外，可以设置对特定域和路径的限制，从而限制 cookie 的发送位置。

#### 设置 Cookie 和 Cookie 头

Set-Cookie HTTP 响应头将 Cookie 从服务器发送到用户代理。
一个简单的 cookie 是这样设置的`Set-Cookie: <cookie-name>=<cookie-value>`。

#### 会话 cookies

上面创建的 cookie 是一个会话 cookie:它在客户机关闭时被删除，因为它没有指定 Expires 或 Max-Age 指令。但是，web 浏览器可能使用会话恢复，这使得大多数会话 cookies 是永久的，就像浏览器从未关闭一样。

#### 永久饼干

永久 cookies 不是在客户端关闭时过期，而是在特定日期(过期)或特定时间长度(最大年龄)后过期。永久 cookies 是这样设置的`Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2020 07:28:00 GMT;`。

#### 浏览器支持

| 微软公司出品的 web 浏览器 | 边缘 | 火狐浏览器 | 铬 | 旅行队 | 歌剧 | 机器人 |
| --- | --- | --- | --- | --- | --- | --- |
| 。 | 。 | 。 | 。 | 。 | 。 | 。 |

#### 您可以添加许多可能的 cookies 来提高产品的安全性。

##### 【相同地点】饼干

SameSite cookies 让服务器要求 cookie 不能与跨站点请求一起发送，这在某种程度上防止了跨站点请求伪造攻击(CSRF)。SameSite cookies 仍然是试验性的，并不是所有的浏览器都支持。

同站点属性可以有两个值中的一个:
|值|含义|
|-|-|
|`strict`|如果同站点 cookie 有此属性，浏览器将只发送来自设置 cookie 的网站的请求。如果请求来自不同于当前位置 URL 的其他 URL，则不会包含任何标记有 strict 属性的 cookies。|
|`lax`|如果该属性设置为 lax，则在跨域的子请求(如加载图像或帧的调用)中会保留同站点 cookies，但当用户从外部站点导航到 URL(例如，通过链接)时会发送。|

如果未设置该标志，或者浏览器不支持该标志，则默认行为是将 cookies 包含在任何请求中，包括跨来源请求。

##### 【安全】饼干

安全 cookie 仅通过 HTTPS 协议与加密请求一起发送到服务器。即使是安全的，敏感信息也不应该**存储在 cookies 中，因为它们本质上是不安全的，这个标志不能提供真正的保护。从 Chrome 52 和 Firefox 52 开始，不安全的站点(http:)不能用 secure 指令设置 cookies。**

##### “http only”Cookie

为防止跨站脚本(XSS)攻击，JavaScript 的 Document.cookie API 无法访问 HttpOnly cookies 它们只被发送到服务器。例如，持久化服务器端会话的 cookies 不需要对 JavaScript 可用，并且应该设置`HttpOnly`标志。

#### 我们建议实施什么:`Secure=True; HttpOnly=True, SameSite=strict`

##### 阿帕奇

| 服务器类型 | 如何 |
| --- | --- |
| Apache HTTP 服务器 | 在 Apache web 服务器的 httpd.conf 中添加以下条目。`Header edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure;SameSite=Strict`。重启 apache 以激活配置，然后进行验证。 |
| 低于 Aache 2.2.4 的 Apache HTTP 服务器 | 在 Apache web 服务器的 httpd.conf 中添加以下条目。`Header set Set-Cookie HttpOnly;Secure;SameSite=Strict`。重启 apache 以激活配置，然后进行验证。 |

##### PHP

```
setcookie ( string $key [, string $value = "" [, int $expires = 0 [, string $path = "" [, string $domain = "" [, bool $secure = FALSE [, bool $httponly = FALSE ]]]]]] ) : bool 
```

Enter fullscreen mode Exit fullscreen mode

##### 节点。射流研究…

```
response.setHeader('Set-Cookie', 'key=value; secure; HttpOnly; SameSite=Strict'); 
```

Enter fullscreen mode Exit fullscreen mode

##### 巨蟒

```
self.set_secure_cookie('key', value, secure=True, httponly=True) 
```

Enter fullscreen mode Exit fullscreen mode

##### 红宝石轨道

```
cookies["key"] = { :value => "value", :secure => true, :http_only => true, :same_site =>  } 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以测试你的网站 http 响应头与那个网站的响应头。

* * *

### 结论

我们来总结一下可以使用的服务器配置。

#### 阿帕奇

| 页眉 | 履行 |
| --- | --- |
| x-XSS-保护 | `Header set X-XSS-Protection "1; mode=block"` |
| x-框架-选项 | `Header set X-Frame-Options "DENY"` |
| x-内容-类型-选项 | `Header set X-Content-Type-Options "nosniff"` |
| 内容-安全-策略 | `Header set Content-Security-Policy: default-src 'self'; report-uri http://reportcollector.example.com/collector.cgi` |
| 推荐人-策略 | `Header set Referrer-Policy "no-referrer"` |
| HTTP 严格的传输安全性 | `Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains"` |

#### Nginx

| 页眉 | 履行 |
| --- | --- |
| x-XSS-保护 | `add_header X-XSS-Protection "1;mode=block";` |
| x-框架-选项 | `add_header X-Frame-Options "DENY";` |
| x-内容-类型-选项 | `add_header X-Content-Type-Options "nosniff";` |
| 内容-安全-策略 | `add_header Content-Security-Policy "default-src 'self';", "report-uri http://reportcollector.example.com/collector.cgi;"` |
| 推荐人-策略 | `add_header Referrer-Policy no-referrer;` |
| HTTP 严格的传输安全性 | `add_header Strict-Transport-Security "max-age=63072000; includeSubdomains";` |

#### 饼干

| 服务器类型 | 如何 |
| --- | --- |
| Apache HTTP 服务器 | 在 Apache web 服务器的 httpd.conf 中添加以下条目。`Header edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure;SameSite=Strict`。重启 apache 以激活配置，然后进行验证。 |
| 低于 Aache 2.2.4 的 Apache HTTP 服务器 | 在 Apache web 服务器的 httpd.conf 中添加以下条目。`Header set Set-Cookie HttpOnly;Secure;SameSite=Strict`。重启 apache 以激活配置，然后进行验证。 |

* * *

### 浏览器支持

| 微软公司出品的 web 浏览器 | 边缘 | 火狐浏览器 | 铬 | 旅行队 | 歌剧 | 机器人 |
| --- | --- | --- | --- | --- | --- | --- |
| HTTP 严格的传输安全(HSTS) | Eleven | Thirteen | Forty-seven | forty-nine | Nine point one | Thirty-nine |
| HTTP 的公钥锁定扩展(HPKP) | 纳秒 | 纳秒 | Forty-seven | forty-nine | 纳秒 | Thirty-nine |
| x-框架-选项 | eight | Thirteen | Forty-seven | forty-nine | Nine point one | Thirty-nine |
| x-XSS-保护 | eight | 。 | 纳秒 | 4+ | 。 | 。 |
| x-内容-类型-选项 | eight | 。 | Fifty-one | One | 纳秒 | Thirteen |
| 内容-安全-策略 | Eleven | Thirteen | Forty-seven | forty-nine | Nine point one | Thirty-nine |
| x-允许的跨域策略 | 。 | 。 | 。 | 。 | 。 | 。 |
| 推荐人-策略 | 纳秒 | 纳秒 | Fifty | fifty-six | 纳秒 | Forty-three |
| Expect-CT | 。 | 。 | 。 | Sixty-one | 。 | Forty-eight |
| 特征策略 | 。 | 。 | 。 | 。 | 。 | 。 |

* * *

#### 现实生活中如何考试

这是测试安全头的最简单的方法。只要打开一个控制台和火卷曲；Curl 将在几秒钟内为您抓取标题。我们需要使用带有参数 I 和 L 的 Curl。I 开关将告诉 Curl 抓取头部，L 参数将帮助跟踪重定向，如果我们的目标有任何重定向的话。

```
curl -I -L --url <target domain or IP> 
```

Enter fullscreen mode Exit fullscreen mode

```
➜ curl -I -L --url example.fr/espace-client
HTTP/1.1 301 Moved Permanently
Location: https://www.example.fr/
Content-Length: 0
Connection: close
Date: Thu, 04 Jul 2019 13:18:30 GMT

HTTP/2 200
content-type: text/html; charset=UTF-8
x-frame-options: SAMEORIGIN
strict-transport-security: max-age=15724800; includeSubDomains
cache-control: max-age=3600, public
link: <http://www.example.fr/>; rel="shortlink", <http://www.example.fr/>; rel="canonical"
link: </node/1>; rel="revision"
x-ua-compatible: IE=edge
content-language: fr
x-content-type-options: nosniff
x-frame-options: DENY
expires: Sun, 19 Nov 1978 05:00:00 GMT
last-modified: Thu, 04 Jul 2019 12:29:39 GMT
etag: W/"1562243379"
x-drupal-cache: HIT
x-xss-protection: 1; mode=block
access-control-allow-origin: *
content-security-policy: upgrade-insecure-requests
access-control-allow-methods: POST, GET, DELETE, PUT
access-control-max-age: 1000
access-control-allow-headers: Content-Type, origin, accept
referrer-policy: no-referrer
date: Thu, 04 Jul 2019 13:18:30 GMT
vary: cookie 
```

Enter fullscreen mode Exit fullscreen mode

Nmap 可以用来非常容易地测试和验证安全报头。我们可以利用名为“http-security-headers”的 nmap 脚本。从这个链接下载:[https://svn.nmap.org/nmap/scripts/http-security-headers.nse](https://svn.nmap.org/nmap/scripts/http-security-headers.nse)

```
nmap -p 443,80 --script http-security-headers <target IP or Domain> 
```

Enter fullscreen mode Exit fullscreen mode

参考下面的结果:它很好地概述了已实现的头值。

```
➜ nmap -p 443,80 --script http-security-headers example.fr
Starting Nmap 7.70 ( https://nmap.org ) at 2019-07-04 15:19 CEST
Nmap scan report for example.fr (92.223.124.199)
Host is up (0.012s latency).

PORT    STATE SERVICE
80/tcp  open  http
| http-security-headers:
|   Strict_Transport_Security:
|     Header: Strict-Transport-Security: max-age=15724800; includeSubDomains
|   X_Frame_Options:
|     Header: X-Frame-Options: SAMEORIGIN, DENY
|     Description: The browser must not display this content in any frame.
|   X_XSS_Protection:
|     Header: X-XSS-Protection: 1; mode=block
|     Description: The browser will prevent the rendering of the page when XSS is detected.
|   X_Content_Type_Options:
|     Header: X-Content-Type-Options: nosniff
|     Description: Will prevent the browser from MIME-sniffing a response away from the declared content-type.
|   Content_Security_Policy:
|     Header: Content-Security-Policy: upgrade-insecure-requests
|     Description: Instructs user agent to download insecure resources using HTTPS.
|   Cache_Control:
|     Header: Cache-Control: max-age=3600, public
|   Expires:
|_    Header: Expires: Sun, 19 Nov 1978 05:00:00 GMT
443/tcp open  https
| http-security-headers:
|   Strict_Transport_Security:
|     Header: Strict-Transport-Security: max-age=15724800; includeSubDomains
|   X_Frame_Options:
|     Header: X-Frame-Options: SAMEORIGIN, DENY
|     Description: The browser must not display this content in any frame.
|   X_XSS_Protection:
|     Header: X-XSS-Protection: 1; mode=block
|     Description: The browser will prevent the rendering of the page when XSS is detected.
|   X_Content_Type_Options:
|     Header: X-Content-Type-Options: nosniff
|     Description: Will prevent the browser from MIME-sniffing a response away from the declared content-type.
|   Content_Security_Policy:
|     Header: Content-Security-Policy: upgrade-insecure-requests
|     Description: Instructs user agent to download insecure resources using HTTPS.
|   Cache_Control:
|     Header: Cache-Control: max-age=3600, public
|   Expires:
|_    Header: Expires: Sun, 19 Nov 1978 05:00:00 GMT

Nmap done: 1 IP address (1 host up) scanned in 0.88 seconds 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 附件

[OWASP 安全头项目- OWASP](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project#tab=Main)
[HTTP Cookies 安全- Mozilla 开发者网络](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Security)

* * *

视频由 [Wild & Secure](https://wildnsecure.com) 制作，这是您的安全和房地产咨询公司。
如果你想每周收到高质量的安全内容，请在我们的网站上订阅我们的时事通讯[。](https://wildnsecure.com/contact)