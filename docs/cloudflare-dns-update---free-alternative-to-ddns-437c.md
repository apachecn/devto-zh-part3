# cloud flare DNS-ddns 的免费更新替代方案？

> 原文：<https://dev.to/gac/cloudflare-dns-update---free-alternative-to-ddns-437c>

所以最近我在家里给自己安装了一个小小的网络服务器，这样我就可以摆弄它，并且有一个地方可以存储这些项目，以后可以给朋友和/或客户看。但是我的小想法面临着一个问题: ***我家里有一个动态 IP***。所以我开始四处寻找解决方案，我想起了[动态域名系统](https://dyn.com/dns/)，他们**有**他们的动态域名系统计划的免费版本(遗憾的是，现在不再是这样了)。于是我寻找一个替代品，找到了 [NoIP](https://noip.com) 。他们有一个免费的计划，让你可以从他们的免费域名中选择你的主机名。

这是一个伟大的发现，但对我来说不幸的是，在他们的免费计划中有一个条款，这意味着我必须点击我的电子邮件中的链接，并通过验证过程，以确认我仍然使用我的主机名，它不应该被删除。我无法自动化这个过程，因为它有几个步骤，谷歌 recaptcha 确保我没有使用脚本或机器人为我做这件事。

因此，我开始寻找其他选项，同时保持 NoIP 主机名有效，以允许我从外部世界访问服务器。我发现的另一个替代方法是使用我朋友的服务器和域，通过 autossh 建立一个 ssh 隧道，并设置一个端口转发，这样我就可以访问它。这也很好，但它也有一些缺点。一个是我必须使用我朋友的域名和端口，他会允许我使用，这样他们就不会与他正在使用的端口冲突。

所以我们又一次设置好了它，我用了一段时间来看看事情会如何发展，一切都很好(为了安全起见，我仍然使用它的一些部分)，但在大多数情况下，我找到了一个替代方案。我记得我自己的域名正通过 [CloudFlare](https://cloudflare.com) 进行路由，我还看到了一些 API 文档的链接。于是我登录自己的账号，找到文档，开始阅读。快速找到 dns 区域的文档，以及如何获得它们的列表和如何更新特定区域。

现在有趣的部分来了

我在我的服务器上打开了 nano(不要评价我:)，并开始编写和搜索如何在 shell/bash 中做事情(在此之前没有太多经验)。我必须查找的第一件事是如何获得我的公共 IP 并将其存储到一个变量中。

一个 [nixCraft](https://www.cyberciti.biz/) 网站帮了大忙，感谢 [@nixCraft](https://twitter.com/nixcraft) 写了这么棒又简单易懂的教程《你是 RMVP:)

在那之后，我开始寻找从 CloudFlare 获取信息的方法，并使用 [Postman](https://www.getpostman.com/) 及其出色的功能根据您的请求生成代码。我将调用的输出存储到一个变量中，然后使用 [jq](https://stedolan.github.io/jq/) 将 JSON 输出解析成另外两个变量。一个用于存储成功响应的结果，另一个用于存储错误消息。

之后，我查找了如何用 shell 发送电子邮件，并查看了 [mail](https://www.cyberciti.biz/faq/linux-unix-bash-ksh-csh-sendingfiles-mail-attachments/) 命令。这很好，但为了将它发送到现实世界，我需要其他东西(或者至少我不知道如何配置它来做到这一点)。所以我与 [sSMTP](https://www.cyberciti.biz/tips/linux-use-gmail-as-a-smarthost.html) 达成了和解，它允许我在更新 DNS 记录出现问题时给自己发电子邮件。

您可以在我的 github 上查看完整的源代码:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [吉吉里](https://github.com/gigili)/[cloud flare-DNS-更新](https://github.com/gigili/Cloudflare-dns-update)

### 允许我更新我的 CloudFlare dns 记录，以便我可以使用动态 IP 访问我的服务器

<article class="markdown-body entry-content container-lg" itemprop="text">

# Cloudflare dns 更新

[![All Contributors](img/978c1092a51de6094a8f8a354af886db.png)](https://github.com/gigili/Cloudflare-dns-update#contributors-)

允许我更新我的 Cloudflare dns 记录，以便我可以使用动态 IP 访问我的服务器

因为我在家里有一个动态的 IP 地址，但是我自己安装了一个小的网络服务器，所以我需要一个好的域名来访问它，而不用担心 IP 地址的变化。有许多很好的选择，如 ddns 或 noip，但是如果你可以尝试和学习一些新的东西并创建自己的脚本来模拟这些客户端的行为，为什么要付费或麻烦地确认你的帐户呢

还有一个好处是，您可以将它用于通过 Cloudflare 托管的任何域/子域，而不必从这些免费域中选择一个。

***要求:***

*   对…的基本知识

</article>

[View on GitHub](https://github.com/gigili/Cloudflare-dns-update)