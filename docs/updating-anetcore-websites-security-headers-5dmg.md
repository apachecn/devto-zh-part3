# 更新. netcore 网站的安全标题

> 原文：<https://dev.to/kritner/updating-anetcore-websites-security-headers-5dmg>

### 更新. netcore 网站的安全标题

GaProgMan 最近在推特上发布了他对 securityHeaders.com 的 A+评级。检查我的网站，我可以看到有改进的空间！

原推文:

> ![](img/f6d4012c9a1a901a4ec0792e50eb6ba8.png)![](img/1c336ed90a5be0209d65d175a9ce465b.png)杰米@ dotnetcoreblog![](img/4d9c44713c216584b3d48ff3455cbb68.png)我非常高兴能在[@ security headers](https://twitter.com/securityheaders)
> [# secure web](https://twitter.com/hashtag/secureTheWeb)2018 年 11 月 27 日上午 09:08[上成为 50 万个评级为 A+的网站之一](https://twitter.com/intent/tweet?in_reply_to=1067344351947309057)

我检查了 kritner.com，结果不太好。让我们看看如何改变这一点！

网站安全——我们在 ssllabs 上取得了 A+的成绩:

[在 ssllabs.com 从“A”到“A+”](https://dev.to/kritner/going-from-an-a-to-an-a-on-ssllabscom-3pda)

但是从标题的角度来看，一个安全的站点怎么样呢？我们走吧！

#### 什么是安全头，它们解决什么问题？

Owasp 是帮助保护您的应用程序安全的绝佳资源:

OWASP 安全标题项目- OWASP

OWASP 站点详细介绍了主要的安全漏洞，如何预防这些漏洞，以及如何通过各种不同的潜在攻击手段来保护您的站点。现在，我们集中在安全标题上。

利用安全头的 TLDR 是在客户机和服务器之间建立一个协议，规定当它们相互连接时什么是允许的，什么是不允许的。确保我们通过 HTTPS 进行通信，不允许在 iframe 中加载站点，以及防止跨站点脚本，这些都是适当的安全头可以帮助解决的问题的例子。

#### kritner.com 基线

只是为了了解一下我将在 kritner.com 处理的事情——从[securityheaders.com](https://www.securityheaders.com):

[![](img/f5aed921b88ae1a8ab707407025d45ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XfpvmEln--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoGfUiEhBFyOLlPCXxdAZ-w.png)

[![](img/a38adeba36c55915494b6ccda18601f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kyGf0mPt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApmRv7BUE1VFMlTKs0j9dxA.png)

上面给出了一个很好的评级细分，以及关于缺失信息的信息。

#### 我们能做些什么来改善它

幸运的是，GaProgman(这篇文章的作者)已经整理了一个 OWasp 安全头 NuGet 包，我认为它会让我的网站大部分时间都在那里。

[OwaspHeaders.Core 3.3.2](https://www.nuget.org/packages/OwaspHeaders.Core/)

Jamie 建议我先阅读文档，所以我想我应该这样做:)

> ![](img/1c336ed90a5be0209d65d175a9ce465b.png)杰米@ dotnetcoreblog![](img/4d9c44713c216584b3d48ff3455cbb68.png)[@ RLHammett](https://twitter.com/RLHammett)[@ security headers](https://twitter.com/securityheaders)务必先看文档
> [github.com/GaProgMan/Owas…](https://t.co/F7t5xjdHwP)2018 年 11 月 27 日 20:55[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1067522245558525955)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1067522245558525955)0

好吧，我读了，你呢？

让我们安装这个坏男孩！

```
add package OwaspHeaders.Core 
```

[![](img/a7c68fa97996baefa5147dd3a2d8ab0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i5Tr9BTp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVsi95AwFOMjA_-5wA2SPrQ.png)

我将使用从[自述文件](https://github.com/GaProgMan/OwaspHeaders.Core/blob/master/README.md)中指定的默认构建器，看看我能得到什么。

在`Startup.Configure()`内

```
app.UseSecureHeadersMiddleware(SecureHeadersMiddlewareExtensions.BuildDefaultConfiguration()); 
```

现在剩下要做的就是构建这个东西并测试它！注意，我使用 docker、kestrel 和 nginx 来构建和服务我的网站，所以我不太确定这是否不需要调整就能工作；谢天谢地，它做到了！

我只是需要:

*   重建我的 docker 映像(自我提醒—这个 docker 文件太大了…我需要重构它)
*   推入映像到坞站集线器
*   SSH 进入服务器，拉映像，重启服务
*   期待最好的结果

做完所有这些，并再次测试我的站点后，我看到了:

[![](img/a689a94fdc1143ac7bfff2eb7bfe5e95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S5gBlyeF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AnnqnoL9ViTuktP4f1LleiA.png)

我不知道还能说什么。让你的网站进入一个更安全的状态非常简单，而且几乎是现成的。现在，我们已经通过其 SSL/TLS 和安全标头确保了 kritner.com 的安全！

#### 相关

*   [安全标题](https://www.securityheaders.com) —测试您网站的安全标题！
*   [OWasp Headers Core](https://www.nuget.org/packages/OwaspHeaders.Core/)—nu get package by[gap progman](https://dotnetcore.gaprogman.com/)
*   [OWasp 头核心](https://github.com/GaProgMan/OwaspHeaders.Core) — GitHub repo
*   [在 SSLLabs.com 从 A 到 A+](https://medium.com/@kritner/going-from-an-a-to-an-a-on-ssllabs-com-570d2e245100?source=friends_link&sk=54451e8847a0162f28349b43595e4efc)