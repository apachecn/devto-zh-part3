# mywebsite.com 和 www.mywebsite.com 有什么不同？

> 原文：<https://dev.to/demetrakopetros/what-is-the-difference-between-mywebsitecom-and-wwwmywebsitecom-nel>

所以有一天一个朋友问我“mywebsite.com 和 www.mywebsite.com 的域名有什么不同”？

差异主要是技术性的，但也很重要。

简单来说，这两个基本区别与 cookies 和在 DNS 级别更改域“映射”到的“目的地”的能力有关。

### 所以关于饼干...

cookie 的核心区别在于，如果一个“裸”域中的站点使用它们的资源(或者只是向它们请求数据)，那么这个“裸”域(比如 mywebsite.com)会将属于它们的 cookie 发送到它们拥有的子域。您可能会理解，这可能会造成带宽的浪费，因为大多数子域(如果不是全部)没有理由知道主站点的 cookies。相反，子域不会向其他子域发送 Cookie(除非在 HTTP 响应头的 Set-Cookie 头中明确声明)

### 关于 DNS 和 CNAME 的记录

裸域只能与“A”DNS 记录相关联。a 记录只允许域与特定的 IP 地址相关联。然而，子域(如[www.mywebsite.com](http://www.mywebsite.com))支持“CNAME”记录，允许将一个子域与另一个域或子域(如 server2.mywebsite.com)相关联。这在您需要管理流量并将流量从一台服务器转移到另一台服务器的情况下非常有用(您只需在 CNAME 记录中更改子域“映射”到的域/子域即可)。

希望对你有启发和帮助！

如果您有任何意见，请告诉我！