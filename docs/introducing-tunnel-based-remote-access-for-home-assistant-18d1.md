# 为家庭助理引入基于隧道的远程访问

> 原文：<https://dev.to/webhookrelay/introducing-tunnel-based-remote-access-for-home-assistant-18d1>

首先，什么是家庭助手？[家庭助手](https://www.home-assistant.io/)是一个自动化中枢，允许您轻松部署和互联大量不同的应用程序/服务/硬件小工具。它可以接受 webhooks，启动任务或运行更强大的工作流引擎，如它内部的 Node-RED(作为一个附加组件)。这是一个神奇的东西，安装在你的备用树莓派(我知道你有一个😏).

家庭助手 GitHub repo([https://github.com/home-assistant/home-assistant](https://github.com/home-assistant/home-assistant))拥有 2 万颗星星，这可能是其受欢迎程度的一个很好的证明:)

在这篇短文中，我将向您展示我的插件，它使用 [Webhook Relay](https://webhookrelay.com/) 云服务来提供对运行在 RPI 上的家庭助理的简单而安全的远程访问:

[![Remote access home assistant addon](img/16a9185fa53c7c2b09a3d1a3ac5b05b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ErtjSmVF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ep723h2fjfathq1xf54.png)

它可能看起来像许多不同的工具，但大多数是自动化的，不需要任何复杂的配置。

**如果您只是想尝试一下，请跳到“尝试”部分**

* * *

## 那么，为什么要远程访问插件呢？

由于家庭助理通常部署在您的本地网络中，从外部连接到它可能会有问题，除非您将路由器配置为进行端口转发。然而，有些情况下这是不可能的:

*   ISP 正在阻止入站连接-简单，没有端口转发将有所帮助。
*   由于 4G 网络的双重 NAT 同上。
*   无法访问/配置路由器
*   没有静态 IP
*   托管家庭助理的服务器正在更改位置/IP 地址
*   路由器不支持端口转发

该插件的工作原理是创建一个返回云服务的隧道，并允许任何外部流量(来自 4G 手机或办公室网络)返回到之前建立的隧道之上的家庭助理。这样，到服务器的任何传入连接都不需要端口转发或静态 IP。

## 附加组件

该插件是一个轻量级守护程序，它基于 [webhookrelayd Docker 映像](https://hub.docker.com/r/webhookrelay/webhookrelayd-aarch64/tags)，其大小仅为 5MB。该应用程序是用 Golang 编写的，这使得它快速高效。它几乎不使用任何内存或 CPU。该应用程序被定制为使用 Home Assistant 附加组件配置 JSON 文件，并将其视为一等公民，无需使用肮脏的 shell 脚本(像许多其他附加组件一样)来使用该配置。

## 安全&隐私

为了让这个插件成功，我必须为 Webhook Relay 开发的第一件事就是 TLS 直通隧道。Home Assistant 的几个主要开发者表示，用户设备上的 TLS 终止对于确保其安全性至关重要。甚至家庭助手云也没有为 webhooks 做这些，他们在他们的云服务上终止 TLS，然后将其传递给用户。所以，我不得不看第三方服务，将提供免费的子域+支持让我们加密。很高兴， [DuckDNS](https://www.duckdns.org/) 化险为夷:)

至于 TLS 传递和终止，Golang 有一个很好的标准库来处理这一点，它只是反过来(通常您会终止到您的服务器的传入连接，而不是终止从服务器传出的连接)。

## 把所有的东西放在一起

一旦附加组件启动，它将读取配置并确保 Webhook 中继中的隧道。这些隧道只是一个路由配置，告诉云服务将请求从`example.duckdns.org`定向到`client-xxx`。然后，它将 DuckDNS A 记录`example.duckdns.org`配置为属于 Webhook 中继的 IP 地址，并从[检索 HTTPS 证书，让我们使用 DNS 质询来加密](https://letsencrypt.org/)(因为我们还没有到客户端的 HTTP 流量)。有了这个证书，我们就可以在客户端设置 TLS 终止。在所有外部流量完全加密的情况下，仍然通过`http://localhost:8123`在 HTTP 端口上调用 Home Assistant。

## 试一试

在我们开始之前，本教程希望您具备:

*   **基本**计划订阅(4.5 美元/m)，支持 DuckDNS 域和 TLS 直通隧道。如果你不想承诺，给我们发邮件到 info@webhookrelay.com[的](mailto:info@webhookrelay.com)，我们会为你安排为期两周的试用期。
*   [DuckDNS](https://www.duckdns.org/) 账号获得自己的免费域名，通过[取回 TLS 证书让我们加密](https://letsencrypt.org/)。

配置 DuckDNS:

[![DuckDNS config](img/45f95bed9ce7a9ca9ff9ce5dac20c251.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4wTKeIr6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hbrj6h7a8zxmfug4tpo8.png)

这个附加组件的安装非常简单，与安装任何其他[has . io](https://hass.io)附加组件相比没有什么不同:

1.  将我们的 Hass.io 插件库 URL 添加到您的 Hass.io 实例:[https://github.com/webhookrelay/home-assistant](https://github.com/webhookrelay/home-assistant)
2.  安装“Webhook Relay”附件
3.  生成一个[令牌密钥&秘密对](https://my.webhookrelay.com/tokens)并将其添加到附加配置中
4.  获取 [DuckDNS](https://www.duckdns.org/) 令牌并创建您的域名。将这些详细信息添加到“隧道”配置部分和“duck_dns”部分。如果你接受[让我们加密 ToS](https://community.letsencrypt.org/tos) ，设置“accept_terms”为真
5.  启动“Webhook 中继”附件
6.  检查“Webhook Relay”附加组件的日志，看看是否一切顺利。它应该打印出你的公共网址

配置示例:

```
{  "key":  "[YOUR WEBHOOKRELAY KEY]",  "secret":  "[YOUR WEBHOOKRELAY SECRET]",  "forwarding":  [],  "tunnels":  [  {  "name":  "ha",  "destination":  "http://127.0.0.1:8123",  "protocol":  "tls",  "domain":  "[YOUR SUBDOMAIN].duckdns.org"  }  ],  "duck_dns":  {  "token":  "[YOUR DUCKDNS TOKEN]",  "accept_terms":  true  },  "tunnels_enabled":  true,  "forwarding_enabled":  false  } 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，你应该可以通过你配置的域名访问你的家庭助手，在我的例子中是[https://auto-ha.duckdns.org](https://auto-ha.duckdns.org)。我们拥有完整的端到端加密，无需配置您的路由器或获取静态 IP 地址:

[![HA](img/8491cfcbd4cafbdb3b7192c1bc162400.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kt65gb6F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8bcm6xtzinfy8rzt20up.png)

此外，除了使用 DuckDNS &让我们加密，您可以使用任何您想要的证书，或者不提供任何证书给附加组件，并在 Home Assistant 服务器上终止 TLS。为此，您只需在目的地:`"destination": "https://127.0.0.1:8123"`中指定 HTTPS。附加 TLS 终止纯粹只是为了让您的生活更轻松，当您可以自动完成时，为什么还要手动完成呢？

## 故障排除

**DNS 问题**

就像任何让你大费周章的系统一样，可能会有问题。我注意到在我的 Raspberry Pi 上运行 [pi-hole](https://pi-hole.net/) 经常会破坏 DNS，并且容器无法再访问互联网。重启会有所帮助，但是你可能想通过修改(【https://github.com/home-assistant/hassio/issues/497】)来寻找一个更永久的解决方案。

如果你使用的是 hassio 提供的镜像，那么在 Docker 中修复 DNS 的过程就有点复杂了，查看他们的调试[文档](https://developers.home-assistant.io/docs/en/hassio_debugging.html)

**多隧道& IP 地址**

在上面的例子中，您可以修改隧道配置以拥有多个隧道

```
"tunnels": [
        {
            "name": "ha",
            "destination": "http://127.0.0.1:8123",
            "protocol": "tls",          
            "domain": "[YOUR SUBDOMAIN].duckdns.org"            
        },
                {
            "name": "node-red",
            "destination": "http://127.0.0.1:1880",
            "protocol": "tls",          
            "domain": "[YOUR NODE RED SUBDOMAIN].duckdns.org"           
        }   
    ], 
```

Enter fullscreen mode Exit fullscreen mode

您可以将 IP 地址设置为其他本地网络地址，如[http://192 . 168 . 1 . 120:1880](http://192.168.1.120:1880)，如果这是您的 Node-RED 或其他服务正在运行的地方。

**自定义域或无域**

你可以一直使用`*.webrelay.io`子域，因为它们默认提供有隧道。只需将协议设置为“https”。
另一个选择是使用自己的域和证书，查看【配置示例([https://www . home-assistant . io/docs/ecosystem/certificates/TLS _ self _ signed _ certificate/](https://www.home-assistant.io/docs/ecosystem/certificates/tls_self_signed_certificate/))。一旦你在你的家庭助手上安装了 HTTPS，指定你的域，并将目的地改为 https: `"destination": "https://127.0.0.1:8123",`。不要忘记为你的 Webhook 中继隧道创建一个 CNAME 记录(应该出现在[隧道页面](https://my.webhookrelay.com/tunnels)

感谢阅读！