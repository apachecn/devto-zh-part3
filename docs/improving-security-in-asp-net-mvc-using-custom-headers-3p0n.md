# 使用自定义头文件提高 ASP.NET MVC 的安全性

> 原文：<https://dev.to/thomasardal/improving-security-in-asp-net-mvc-using-custom-headers-3p0n>

我最近发现了由高产的 Scott Helme 制作的 [securityheaders.io](https://securityheaders.io/) 。securityheaders.io 会扫描您的网站，并建议添加哪些 HTTP 响应标头以提高安全性。

[![securityheaders.io](img/9ad42b3ba3a231836ec445a50e4cf2fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WCOgaQya--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.elmah.io/conteimg/old/securityheaders.png)

这篇文章不仅总结了最近添加的 [elmah.io](https://elmah.io/?utm_source=devto&utm_medium=social&utm_campaign=devtoposts) 头文件，还总结了如何让黑客更难入侵你的 ASP.NET(MVC、Web API、Core)网站。在下面的段落中，我列出了许多易于配置的 HTTP 头，每个人都应该实现它们。

## X-帧-选项

标题确保了黑客不会内嵌你的网站，来欺骗你点击你从未想过的链接。如果您使用的是 ASP.NET MVC 5 或更新版本，这个头会自动添加。使用`web.config` :
可以很容易地在以前的版本或其他 web 框架中添加标题

```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="X-Frame-Options" value="DENY" />
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

在这个例子中，我否认任何篡改网站的可能性。如果在同一个域中使用 iframes，可以将值改为`SAMEORIGIN`。

## X-Xss-保护

`X-Xss-Protection`是在大多数现代浏览器中实现的功能，当检测到跨站点脚本攻击时，它将停止加载页面。添加标题也通过`web.config`发生:

```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="X-Xss-Protection" value="1; mode=block" />
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

值 if `1`只是将保护标记为启用。

## X-内容-类型-选项

为了避免 MIME 类型嗅探，您可以添加`X-Content-Type-Options`头。这使得黑客更难通过检查内容来猜测正确的 mime 类型。添加标题很容易通过`web.config` :
完成

```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="X-Content-Type-Options" value="nosniff" />
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

## 推荐人-策略

当用户点击你网站上的链接时，浏览器会自动添加`Referer`标题。这意味着一个链接的网站将能够看到用户来自哪里。虽然这是一个很好的分析功能，但你的 URL 中可能有敏感信息，你不想转发到其他域。要完全删除推荐人，请将以下标题添加到`web.config` :

```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="Referrer-Policy" value="no-referrer" />
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

在现实生活中，您可能希望`Referrer-Policy`有另一个值。完全删除推荐人会让你看不到网站的内部流量。查看[mozilla.org](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy)的推荐人政策，获取可能值的列表。

## X-允许跨域-策略

要限制 Flash 组件进行跨源请求，您应该完全禁用它(当然，除非您正在使用 Flash)。为此，将`X-Permitted-Cross-Domain-Policies`添加到`web.config` :

```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="X-Permitted-Cross-Domain-Policies" value="none" />
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

## 严格-运输-安全

如果你还没有在你的网站上实现 HTTPS，你应该这样做。这样做之后，您可以使用`Strict-Transport-Security`头:
来阻止任何通过 HTTP 的通信

```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="Strict-Transport-Security" value="max-age=31536000; includeSubDomains" />
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

`max-age`值告诉浏览器在指定的秒数内使用该设置。在这种情况下是一年。如果你在子域名(比如博客)上托管非 HTTPS 网站，可以不包含`includeSubDomains`部分。

## X-power-By

`X-Powered-By`标题由 ASP.NET 自动添加。为了让你的网站使用的技术不那么明显，你应该通过`web.config` :
删除这个标题

```
<system.webServer>
  <httpProtocol>
    <customHeaders>
        <remove name="X-Powered-By" />
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

## X-aspnet MVC-版本

很像`X-Powered-By`，`X-AspNetMvc-Version`是框架自动添加的头。为了避免告诉黑客你用的是 MVC，哪个版本，你应该把它去掉。`X-AspNetMvc-Version`不能通过`web.config`移除，但是可以从代码中禁用。将以下内容添加到您的`Startup.cs`或`Global.asax.cs` :

```
MvcHandler.DisableMvcResponseHeader = true; 
```

## 服务器

ASP.NET 还揭示了托管应用程序的服务器。如果黑客知道您正在使用 IIS，这就缩小了他/她需要尝试的弱点的数量。要删除`Server`头，在过滤器中或者通过`Global.asax.cs` :
将其从代码中删除

```
protected void Application_PreSendRequestHeaders()
{
    if (HttpContext.Current != null)
    {
        HttpContext.Current.Response.Headers.Remove("Server");
    }
} 
```

## 功能-策略

`Feature-Policy`报头是安全相关报头系列中的新成员。当指定标题时，你告诉浏览器你的站点使用或不使用哪些特性。这是一个很棒的功能，尤其是如果你嵌入了其他网站。要添加标题，在`web.config`中进行以下更改:

```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="Feature-Policy" value="accelerometer 'none'; camera 'none'; geolocation 'none'; gyroscope 'none'; magnetometer 'none'; microphone 'none'; payment 'none'; usb 'none'" />
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

在这里，我们告诉浏览器，我们的网站不应该允许使用加速度计、摄像头等等。你需要添加哪些功能，完全取决于你的网站。欲了解更多信息，请查看[规范](https://wicg.github.io/feature-policy/)。

## 内容-安全-策略

使用`Content-Security-Policy`头需要更多的解释。查看本系列的下一篇文章了解详情:[ASP.NET MVC 的内容-安全-政策](https://blog.elmah.io/content-security-policy-in-asp-net-mvc/)。

## 你的用户会喜欢更少的错误吗？

elmah.io 是一个简单的错误记录和正常运行时间监控服务。通过对所有的支持，重新控制你的错误。NET web 和日志框架。

➡️ [错误监控。NET 网络应用](https://elmah.io/?utm_source=devto&utm_medium=social&utm_campaign=devtoposts) ⬅️

本文首次出现在 elmah.io 博客上，网址为[https://blog . elmah . io/improving-security-in-ASP-net-MVC-using-custom-headers/](https://blog.elmah.io/improving-security-in-asp-net-mvc-using-custom-headers/)