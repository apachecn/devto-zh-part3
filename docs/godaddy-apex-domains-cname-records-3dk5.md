# GoDaddy，Surge，Apex Domains & CNAME 唱片公司

> 原文：<https://dev.to/rubengmurray/godaddy-apex-domains-cname-records-3dk5>

* * *

# 问题

### 总结

您需要正确解析您在 GoDaddy 购买的 apex 域名([https://yoursite.com](https://yoursite.com))。

### 解释

如果你被要求这样做，你面临的问题是——至少对于基本域名购买——go daddy 不允许你为 apex 域名添加`CNAME`记录。

> 一个顶点域指的是[https://yoursite.com](https://yoursite.com)而不是【https://www.yoursite.com】T2。

# 解

### 解释

1.  GoDaddy 的建议是，在添加任何其他`CNAME`或`A`记录之前，必须先删除 GoDaddy 上的所有`A` & `CNAME`记录。
2.  您必须使用您的主机提供商的 IP 将`CNAME` `@`记录替换为`A` `@`记录。

我的主机是 surge.sh，他们在[这个文档](https://surge.sh/help/adding-a-custom-domain)中提供了他们的 IP。

因此，为了在部署到 surge 时识别我的域，我需要删除所有现有的`A` & `CNAME`记录，并以`Type` : `Name` : `Value`格式添加以下两条记录:

`CNAME` : `www` : `na-west1.surge.sh`

`A` : `@` : `45.55.110.124`

一旦完成，我的域名在 5 分钟内解决。

如果你很难找到你的提供商的 IP 地址，试着在 Twitter 上发消息或者搜索 StackOverflow，但是一旦你有了它，它应该就可以了。

原文来源: [GoDaddy 论坛](https://www.godaddy.com/community/Managing-Domains/domain-DNS-with-surge-sh/td-p/24075)