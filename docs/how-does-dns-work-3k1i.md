# DNS 是如何工作的

> 原文：<https://dev.to/limxingzhi/how-does-dns-work-3k1i>

我试图向一个只有很少网络背景的朋友解释域名服务器的概念。

# 网页是如何工作的

当你打开一个像[google.com](//google.com)这样的网站时，你的浏览器会下载网页并呈现内容供你查看和互动。

# IP 地址从何而来

当你键入一个域名(如[google.com](//google.com))，你的电脑不知道在哪里可以找到它。它依赖于电话簿，一种被称为域名服务器的服务。像一本电话簿一样，它将 google.com 的 T2 翻译成一个实际的 IP 地址，比如 172.217.194.113 的 T4，你的浏览器将在显示给你之前进入并下载网页。你的浏览器不知道[google.com](//google.com)在哪里，但是它知道在哪里可以找到[172.217.194.113](//172.217.194.113)。

# 这个域名服务器在哪里

所有这一切都发生在后台，您的计算机与网络上的其他元素进行交互并使其发生，这一切对您(用户)都是透明的。通常，您的 ISP(互联网服务提供商)提供的路由器会指向您的 ISP 托管的 DNS。在某些情况下，连接速度可能比公共 DNS 慢。在其他情况下，由于盗版、安全或 DNS 主机(DNS 的管理机构)设置的审查问题，您试图访问的 DNS 可能没有您想要的域。

# 为什么使用公共 DNS 而不是默认的

使用 ISP 提供的默认 DNS 通常没有坏处。如前所述，它可能会慢一些，但它有你想访问的大多数网站。在世界的某些地方，某些网站可能会因审查或安全问题而被监管机构审查。有些是在 DNS 级别，有些是在防火墙级别。

在其他用例中，默认 DNS 服务器可能不如公共 DNS 高效，例如来自 [Cloudfare](https://1.1.1.1/) 或 [Google](https://developers.google.com/speed/public-dns/) 的公共 DNS，因此提供结果的速度较慢。

DNS 可以用来记录你的网络活动。通过映射用户和所提供的 DNS 结果，DNS 提供商可以记录您的所有网络活动(您的 ISP 可以通过其他方式做到这一点，DNS 只是其中一种方式)。谨慎选择和使用公共 DNS 可以降低这方面的风险。

# 我们应该马上冲出去更换域名吗？

在大多数国家，仅仅改变你的域名系统可能不足以规避审查法律。对于大多数家庭用户来说，改变 DNS 可能不会提高你的网速，因为网络在其他地方会遇到瓶颈(比如 6 年前的 WiFi 接入点)。

总之，如果你真的需要公共 DNS 提供商提供的稍微快一点的速度或者这些提供商承诺的安全性，那就去改变你的 DNS 吧。就像所有与网络相关的配置一样，请确保您完全了解该技术，并谨慎行事。

这篇文章的范围并不作为改变你的 DNS 的指南。

# 学分

什么是 DNS？——[https://www.cloudflare.com/learning/dns/what-is-dns/](https://www.cloudflare.com/learning/dns/what-is-dns/)

什么是防火墙？-[https://www . Cisco . com/c/en/us/products/security/firewalls/what-is-a-firewall . html](https://www.cisco.com/c/en/us/products/security/firewalls/what-is-a-firewall.html)

为什么用 1.1.1.1？——[https://www.cloudflare.com/learning/dns/what-is-1.1.1.1/](https://www.cloudflare.com/learning/dns/what-is-1.1.1.1/)

https://1.1.1.1/

谷歌公共域名系统-[https://developers.google.com/speed/public-dns/](https://developers.google.com/speed/public-dns/)

为 LOLs-[https://lmgtfy.com/](https://lmgtfy.com/)