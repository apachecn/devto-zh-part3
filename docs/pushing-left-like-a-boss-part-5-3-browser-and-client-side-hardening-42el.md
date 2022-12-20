# 像老板一样向左推—第 5.3 部分—浏览器和客户端加固

> 原文：<https://dev.to/azure/pushing-left-like-a-boss-part-5-3-browser-and-client-side-hardening-42el>

### [这个系列，还有我的博客，都感动了！](https://wehackpurple.com/pushing-left-like-a-boss%e2%80%8a-%e2%80%8apart-5-3%e2%80%8a-%e2%80%8abrowser-and-client-side-hardening/)！

浏览器和客户端加固侧重于启用和使用用户浏览器中的安全功能。与大多数应用程序安全建议一样，以下设置从用户端(客户端/浏览器)而不是服务器端(应用程序本身)保护 web 应用程序的用户。

### 禁用“记住我”功能

虽然我认为所有的应用程序都应该允许用户将值“粘贴”到密码字段中(以允许使用[密码管理器](https://medium.com/@shehackspurple/why-i-love-password-managers-14dad739ea79))，但我不认为应用程序应该允许浏览器使用“记住我”功能来存储密码。在某些时候，我可能会被迫收回自己的话，但在浏览器的安全性提高之前，我认为所有的密码都应该存储在密码管理器中。

### 不允许缓存敏感数据

浏览器和客户端加固的另一项是禁用敏感页面的缓存。在过去，我会建议，如果一个页面是通过 HTTPS 传送的，那么它一定包含敏感信息，因此不应该被缓存。但是现在事情已经发生了变化，坦白地说，特洛伊·亨特让我大开眼界，我相信每个网络应用程序的所有页面都应该通过 HTTPS 传送，也就是说不应该缓存任何东西。有了这个警告，我想建议如果你的应用程序的页面包含任何敏感的东西，你不应该允许它被缓存。不包含敏感信息的页面可能会被缓存以便更快地检索。

### 到处都是 HTTPS

应该从应用程序内部在服务器*和*上强制 HTTPS。应该始终支持 TLS(传输层安全性)的最新版本，在撰写本文时是 TLS 1.2。应该禁用存在已知漏洞的旧版本加密，除非您有明确的业务原因，并且已经针对支持向后兼容性的风险进行了评估。在撰写本文时，所有服务器上都应该禁用 SSL(安全套接字层)和 TLS 1.0 的所有版本。

### [在我的新博客上阅读这篇文章的其余部分！](https://wehackpurple.com/pushing-left-like-a-boss%E2%80%8A-%E2%80%8Apart-5-3%E2%80%8A-%E2%80%8Abrowser-and-client-side-hardening/)！

[![I feel strongly about client-side hardening. Image — http://sector.ca/](img/7f86ce23faeab0199d03bd87a5914997.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5RVWdRLY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yfeqzvh5vfe80h8mal90.jpeg)