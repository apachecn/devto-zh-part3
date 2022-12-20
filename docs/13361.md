# 如何在带有自定义域的网络中启用 SSL

> 原文：<https://dev.to/hamatti/how-to-enable-ssl-in-netlify-with-custom-domain-4273>

你是否有一个静态网站需要放在互联网的某个地方，以便其他人可以访问它？你一定会喜欢上它的:[有几十种非常好的选择](https://www.slant.co/topics/2256/~best-static-website-hosting-provider)。我想试用 [Netlify](https://netlify.com) 来更新我的网站，因为每个人都对它赞不绝口，而我从未用过它。

我在 [Hover](https://hover.com) 上有一个现有的域名，并且曾经在一个自托管的 [Hetzner VPS](https://hetzner.de) 上运行我的网站。但是在过去的十年里，网站和我的服务器变得一团糟。我想开始使用静态站点生成器，并使部署更容易。于是我装了[十一](https://www.11ty.io/)，一点一点把我的旧站点改造成十一站点，运行`eleventy`。嘣，几秒钟后，我有了`_site`文件夹，我把它拖放到 Netlify 中，网站就上线了。

这是有史以来最棒的 UX 体验之一。

Netlify 提供了让我们为网站加密 SSL 证书，但这是我遇到的一些问题。我不是域名和域名系统的专家，无论我尝试什么，我总是面临一个问题。

> 网站通过证书证明他们的身份。Firefox Developer Edition 不信任此网站，因为它使用的证书对 hamatti.org 无效。证书仅对以下名称有效:*.netlify.com，netlify.com
> 错误代码:SSL_ERROR_BAD_CERT_DOMAIN

在大量阅读文档、谷歌搜索、咨询更聪明的同事并最终联系 Netlify 支持后，事情得到了解决。

他们关于用自定义域名设置 DNS 的文档非常好。当我使用 Hover 时，他们不提供名字或别名选项，所以我必须做一个记录和 CNAME。

```
A @ 104.198.14.52
CNAME www [appname].netlify.com 
```

Enter fullscreen mode Exit fullscreen mode

`104.198.14.52`是 Netlify 的负载平衡器的 IP 地址，而`[appname]`应该是您在 Netlify 中的应用名称。

所以我设置了这一切，但仍然遇到了问题。在联系支持人员后，发现有时(可能是我太急于改变设置)DNS-and-or-certificate 链中的某些东西会失败，并且它会挂在 Netlify 的一端。

如果这种情况发生在你身上，联系[他们的支持](https://www.netlify.com/support/)是正确的做法。他们可以手动清除问题，并重新触发获取以使其工作。