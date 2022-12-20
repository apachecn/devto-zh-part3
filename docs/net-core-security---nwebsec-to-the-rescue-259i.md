# 网络核心安全- NWebSec 拯救世界！

> 原文：<https://dev.to/lssweatherhead/net-core-security---nwebsec-to-the-rescue-259i>

使用 [NWebSec](https://docs.nwebsec.com/en/latest/) 和 web.config 保护 Net Core webapp 的快速概述

首先，让我们通过包管理器从 nuget 安装 NWebSec 中间件

```
PM>  Install-Package  NWebsec.AspNetCore.Middleware 
```

Enter fullscreen mode Exit fullscreen mode

对于那些(像我一样)对安全最佳实践有点生疏的人来说，两个一般原则是:

1.  减少攻击面(使潜在攻击者尽可能难以收集关于你的应用的信息)
2.  限制访问(除非安全授权)

安全网络核心应用程序的要素广泛融入这些实践，包括以下(非详尽)列表:

*   [HSTS] HTTP 严格传输安全标头
*   x-XSS-保护接头
*   x-框架-选项标题
*   [CSP]内容-安全-策略标题
*   x-内容-类型-选项标题
*   referrer-策略 Http 标头
*   移除 X-Powered-By 标头以移除通过验证 app tech 传输的附加信息
*   [HPKP] HTTP 公钥固定标头

让我们一个一个来！

#### [HSTS] HTTP 严格传输安全标头

听起来是这样的——强迫所有通讯通过 HTTPS！使用下面指出的`.Preload()`从第一个请求开始强制执行。

```
app.UseHsts(options => options.MaxAge(365).IncludeSubdomains().Preload()); 
```

Enter fullscreen mode Exit fullscreen mode

#### x-XSS-保护接头

当检测到反映的跨站点描述时，此响应标头会阻止页面在现代浏览器中加载。如果一个站点实现了强大的`Content-Security-Policy`(剧透！)

```
app.UseXXssProtection(options => options.EnabledWithBlockMode()); 
```

Enter fullscreen mode Exit fullscreen mode

#### x-框架-选项标题

确保站点内容没有被嵌入到其他站点的 iframe 中——用于避免点击劫持攻击。

```
app.UseXfo(options => options.SameOrigin()); 
```

Enter fullscreen mode Exit fullscreen mode

#### [CSP]内容-安全-策略标题

内容安全策略本质上允许您在站点加载时将资源来源列入白名单。这些策略通常与服务器和脚本来源有关。

有很多不同的方法可以进行配置，它们很大程度上取决于您的需求以及您需要加载和加载的内容。你可以在方便的 Mozilla 文档中阅读更多关于你的选项

例如:

```
app.UseCsp(opts => opts
    .BlockAllMixedContent()
    .StyleSources(s => s.Self())
    .StyleSources(s => s.UnsafeInline())
    .FontSources(s => s.Self())
    .FormActions(s => s.Self())
    .FrameAncestors(s => s.Self())
    .ImageSources(s => s.Self())
    .ScriptSources(s => s.Self())
); 
```

Enter fullscreen mode Exit fullscreen mode

#### x-内容-类型-选项标题

阻止任何可能发生的内容嗅探，这些内容嗅探可能会将无害的 MIME 类型(例如，text/css)更改为可执行文件，从而造成一些真正的损害。

```
app.UseXContentTypeOptions(); 
```

Enter fullscreen mode Exit fullscreen mode

#### referrer-策略 Http 标头

这告诉站点在`Referer`标题字段中发送多少信息(拼写错误！).默认值为`no-referrer-when-downgrade`,即不要发送任何推荐数据，因为我们正在降级安全协议，并将 HTTPS 转到 HTTP 网站。

这一点取决于你的需求，Mozilla 的开发网站上的[详细列出了选项，以帮助你做出决定。如果你想超级安全，那么选择:](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy) 

```
app.UseReferrerPolicy(opts => opts.NoReferrer()); 
```

Enter fullscreen mode Exit fullscreen mode

#### 拆下 X-Powered-By 接头

现在，让我们确保我们不会泄露正在使用的技术(例如 ASP。网)。为此，我们将通过添加到 web.config
来删除 X-Powered-By 头

```
<system.web>
   <httpRuntime enableVersionHeader="false"/>
</system.web>
<system.webServer>
   ...
   <httpProtocol>
     <customHeaders>
        <remove name="X-Powered-By" />
     </customHeaders>
   </httpProtocol>
</system.webServer> 
```

Enter fullscreen mode Exit fullscreen mode

#### [HPKP] HTTP 公钥固定标头

这个很有趣，与白名单证书有关。有几个插件可以帮助你做到这一点，在@JoonasWestlin 博客[这里](https://joonasw.net/view/hpkp-in-aspnet-core)有全面的介绍

* * *

**进一步链接/阅读:**测试安全报头的一个好工具是使用[极客 Flare](https://tools.geekflare.com/header-security-test) 和大量易于一般人消化的信息。网络安全最佳实践可在 OWASP.org[获得](https://www.owasp.org/index.php/.NET_Security_Cheat_Sheet)

这只是 Net Core 站点(主要是基于标题的)安全性入门的一个快速参考点——缺少什么？其他推荐？