# Hugo 和 GitLab 博客(3):自定义域名

> 原文：<https://dev.to/loadbalancing/building-a-blog-website-3-register-a-custom-domain-from-namesilo-26lh>

给你的博客网站一个自定义域名，让它更专业和/或更有趣。

## 顶级域名

一个[顶级域名(TLD)](https://en.wikipedia.org/wiki/Top-level_domain) 是一个域名的最后一个标签，比如“w ww.example.com”的“com”。对于同一个示例，“示例”是二级域。您的博客网站创建并托管在 GitLab 中，与二级域名“gitlab.io”相关联，可通过*https://username . git lab . io*访问。

如今有各种各样的顶级域名。除了最著名的如。com”和”。net”，你可以为你的风格选择其他新的有趣的领域。lol“，”。猫“和”。纽约市”。有些域名对我来说很奇怪，例如。失败“和”。wtf”，我想知道如何使用它们(也就是说，有一个我非常喜欢的好例子， [Algorithms.wtf](http://algorithms.wtf) ，是 UIUC 的一位教授写的)。

顶级域名可以免费吗？[是的](http://www.dot.tk)，但是你只有五个选项，你将无法拥有它(虽然你可以使用它)。域名可以负担得起，只要它不是一个溢价/流行的名称。通常情况下，一年大约十美元就可以租到一辆。它甚至可能会有折扣，第一年结束时还不到一美元。首先，你需要选择一个域名注册商。这里推荐[名思洛](https://www.namesilo.com/?rid=e010b53fi)。

### 为什么是 NameSilo？

通过四处寻找，我发现 NameSilo 是一家信誉非常好的域名注册商(虽然它的网站看起来很老式)。它的主要优势是[价格](https://www.namesilo.com/pricing.php?rid=e010b53fi)——不仅便宜，而且透明。列出的价格是你支付的价格(对于其他域名注册商来说可能并不总是如此)。对于新注册，有优惠券`NamesiloOneDollar`可以获得 1 美元的折扣。请注意，NameSilo [不](https://www.namesilo.com/Support/Why-we-do-not-offer-SSL%2FTLS-certificates/?rid=e010b53fi)提供 SSL/TLS 证书(这意味着您的网站只能在 *http://* 访问，而不能在 *https://* 访问)。幸运的是，有免费提供安全 SSL/TLS 的选项。

## 在域名注册一个域名

要注册域:

1.  进入[姓名库](https://www.namesilo.com/register.php?rid=e010b53fi)并搜索您想要的姓名。
2.  选择顶级域名(例如。com)并点击“注册选中的域名”。
3.  按如下方式设置配置选项:
    *   “服务链接”:无(默认)
    *   “名称服务器”:(离开它)
    *   “自动续订”:否/是
    *   “隐私设置”:WHOIS 隐私
    *   “将所有年份设置为”:1/2/3/.../10 请注意，对于第一年价格促销的域名，注册限制为 1 年。注册后，您将能够以正常价格将其延长至 10 年。
4.  输入优惠券`NamesiloOneDollar`并点击“提交”进行申请。
5.  点击“继续->”并选择付款方式。

## 链接域名和你的网站

链接域名和网站的基本原理是依靠[域名系统(DNS)](https://en.wikipedia.org/wiki/Domain_Name_System) 将域名映射到网站服务的 IP 地址。两端都需要配置:

1.  将自定义域添加到 GitLab。
2.  将 DNS 记录添加到域名注册机构(如 NameSilo)并将其发送到 GitLab。

### 向 GitLab 添加自定义域

1.  禁用强制 HTTPS，因为 NameSilo 不提供 SSL/TLS 证书。
    【设置】>【页面】。
    取消选择“强制 HTTPS(需要有效证书)”。
    点击【保存】。

2.  添加根域(例如 loadbalancing.xyz)。
    【设置】>【页面】>点击“新建域名”。
    “域”:loadbalancing.xyz
    “证书(PEM)”:(留空)
    “密钥(PEM)”:(留空)
    点击“新建域”。
    GitLab 然后会生成一个 DNS TXT 记录内容(后面会用到)比如:
    _ git lab-pages-verification-code . load balancing . XYZ TXT git lab-pages-verification-code =...

3.  添加子域(例如，w ww.loadbalancing.xyz)。
    【设置】>【页面】>点击“新建域名”。
    “域”:w ww.loadbalancing.xyz
    “证书(PEM)”:(留空)
    “密钥(PEM)”:(留空)
    点击“新建域”。
    GitLab 然后会生成一个 DNS TXT 记录内容(后面会用到)像:
    _ git lab-pages-verification-code . www . load balancing . XYZ TXT git lab-pages-verification-code =...

### 将 DNS 记录添加到名称库

1.  打开 NameSilo 的控制面板。
    点击“域管理器”。
    单击要配置的域。
    点击“DNS 记录:更新”。

2.  删除所有现有的 DNS A 和 CNAME 记录。

3.  对于根域(例如 loadbalancing.xyz):
    创建一个新的 A 类型记录。
    【主机名】:(留空)
    【类型】:A
    【地址/值】:35.185.44.232(git lab 的 IP 地址)
    【距离/PRIO】:NA
    【TTL】:3603
    点击“提交”。
    创建一个新的 TXT/SPF 类型的记录。
    【主机名】:_ git lab-pages-验证码
    【类型】:CNAME
    【地址/值】:git lab-pages-验证码=...
    “距离/PRIO”:NA
    “TTL”:3603
    点击“提交”。

4.  对于子域(如 w ww.loadbalancing.xyz):
    创建新的 CNAME 类型记录:
    “主机名”:w ww
    “类型】:CNAME
    “地址/值”:username.gitlab.io
    “距离/PRIO”:NA
    “TTL”:3603
    点击“提交”。
    新建一条 TXT/SPF 类型的记录:
    “主机名”:_ git lab-pages-验证码. w ww
    “类型”:CNAME
    “地址/值”:git lab-pages-验证码=...
    “距离/PRIO”:NA
    “TTL”:3603
    点击“提交”。

这里我们对根域和子域使用不同的 DNS 记录(分别是 DNS A 记录和 DNS CNAME 记录)。GitLab 需要 DNS TXT 记录来验证域名的所有权。

几分钟后，您将能够从新的域名访问您的网站。