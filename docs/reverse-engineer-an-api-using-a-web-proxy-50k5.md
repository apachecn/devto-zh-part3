# 使用 web 代理对 API 进行反向工程

> 原文：<https://dev.to/loopdelicious/reverse-engineer-an-api-using-a-web-proxy-50k5>

当你想到对 API 进行逆向工程时，也许会想到商业间谍或者一些法律或道德上有问题的东西。黑客会入侵。然而，对 API 进行逆向工程也有合理的理由。

## 为什么要对 API 进行逆向工程

API 并不总是有文档记录的。在调试 API 时，如果能够可视化所有发送和接收的数据，就可以更快地诊断和解决问题。这样做可以让你对 API 有更深的理解。

> [逆向工程](https://en.wikipedia.org/wiki/Reverse_engineering)，也称为逆向工程，是对人造物体进行解构以揭示其设计、架构或从物体中提取知识的过程。

了解一个 API 的实际行为使你能够识别缺陷和安全漏洞[，比如意外的数据泄露](https://medium.freecodecamp.org/reverse-engineering-apis-coffee-meets-bagel-2eda71295613)。这也是对您的 API 的性能测试，因此您可以隔离那些可以从额外的缓存和压缩中获益的瓶颈。

当然，对 API 进行逆向工程也有自私的原因。

## 对一个 API 进行逆向工程的自私原因

*   以编程方式访问不提供公共 API 的服务
*   创建一个符合你需求的界面，因为[他们的界面不能满足你的需求](https://www.toptal.com/back-end/reverse-engineering-the-private-api-hacking-your-couch)
*   促成一个特点，就是工程团队慢🐌释放
*   心怀希望，你会让工程团队眼花缭乱，他们会给你一份工作
*   [当应用程序限制你吃次优量的培根时，在你的披萨上点额外的培根](https://news.ycombinator.com/item?id=14758500)
*   在游戏中作弊——在[和其他](https://www.reddit.com/r/ReverseEngineering/)中，有[一整块子区域专门用于这个](https://www.reddit.com/r/REGames/)
*   通过在流行的约会应用程序上释放聊天机器人来扩大你的钓鱼副业

当你对一个不属于你的私有 API 进行逆向工程时，一定要检查服务条款。一些开发人员把他们的停止令戴在身上，就像荣誉徽章一样，但是你可能会被起诉或者被禁止使用他们的服务。

## 什么是 HTTP/S 代理？

web 代理服务器就像是位于客户端应用程序和服务器之间的中间人。web 代理是一种替代服务器，可以拦截 web 浏览器和网站真实服务器之间的 HTTP 流量。

> 都在那了！黑白分明，清澈如水晶！
> —威利·旺卡，巧克力工厂

### 使用网络代理的理由

*   通过记录和显示客户端和服务器之间的所有流量，监控和窃听 HTTP 网络流量
*   浏览网站和访问被阻止的内容时隐藏您的公共 IP 地址
*   过滤或重定向请求以限制对内容的访问或优化系统性能

[![Postman is a proxy that captures the HTTP/S request](img/9ae1d487bab6cdbe0b654ce762dcddcf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0PfdttQF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AW5CE9hV6_G_ivcU9UuYlHw.png)

## 免费网络代理工具

*   **[Postman](https://www.getpostman.com/)** 是一个免费的工具，带有[一堆不同的代理](http://blog.getpostman.com/2017/09/29/client-proxy-settings-in-postman/)，包括[一个内置代理](https://learning.getpostman.com/docs/postman/sending_api_requests/capturing_http_requests)来捕获 Mac、Windows 或 Linux 的本地应用程序中的 HTTP 请求
*   **[Mitproxy](https://mitmproxy.org/)** 是一个开源代理，具有命令行界面、web 界面和 Python API
*   **[Fiddler](https://www.telerik.com/fiddler)** 是一个免费的 web 调试代理，支持多种浏览器、系统和平台
*   **[打嗝](https://portswigger.net/burp/documentation/desktop/tools/proxy)** 有一个免费的网络代理服务器社区版，可以让你查看和修改请求和响应

本文的剩余部分将关注如何使用 Postman 来拦截客户机和服务器之间的流量。

* * *

## 一个逆向工程 API 的邮差配方

是时候弄脏我们的手，尽情地嗅嗅和检查了。首先，我们将一个请求导入到 Postman 应用程序中。然后，我们将使用 Postman 作为代理来捕获来自各种客户端(如桌面 web 浏览器、移动设备和电子应用程序)的 HTTP/S 请求流。

[https://www.youtube.com/embed/2VKVPB5USgU](https://www.youtube.com/embed/2VKVPB5USgU)

要在 Postman 中跟随，请单击 Postman 应用程序左上角的橙色**+新**按钮。在[模板](https://www.getpostman.com/docs/v6/postman/launching_postman/newbutton#templates)标签下，搜索**逆向工程一个 API** ，将样本集合导入到你的 Postman app 实例中。

[![Import this collection and follow along with these examples](img/7bbd38643025cec4f328ec5def3ac2f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uloa-Ku---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2As10zVLqch6xLcdwmtqZdjw.png)

详细阅读 Postman 应用程序中的描述，或者查看 [web 文档](https://documenter.getpostman.com/view/1559645/Rzn9uMQk)中的分步说明和截图。

### 导入单个请求

如果你想在 Postman 中检查一个请求，这里有一个超级简单的方法从你的浏览器中把它作为 [cURL](https://curl.haxx.se/docs/manpage.html) 导入。在这个例子中，我们将使用 [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/) 来检查和选择一个请求。使用其他 web 浏览器时，您可以遵循类似的步骤。

[![Copy the cURL request from Chrome DevTools](img/eb76f4ee1fde1d0b2a85d6c98a1a8f80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lf3-_aqU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AcXBMjFq2g5N7-2SyElYXGw.png)

[![Paste the cURL request as raw text in Postman](img/41ef1a53de4130c1bd528fd99055468d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KCsel5bU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A_2ENwXUm9rONc8X3yNBAqg.png)

### 检查请求流

如果您想要检查来自客户端的请求流，您可以使用 Postman 内置代理来捕获这些请求。邮递员[有一堆不同的代理](http://blog.getpostman.com/2017/09/29/client-proxy-settings-in-postman/)。在这个场景中，我们将依赖于 Mac、Windows 或 Linux 的本地应用中的内置代理[。您可以捕获从客户端发送的请求，如桌面 web 浏览器、移动设备或电子应用程序。](https://learning.getpostman.com/docs/postman/sending_api_requests/capturing_http_requests)

您可以将这些请求通过管道传输到您的[邮递员历史](https://learning.getpostman.com/docs/postman/sending_api_requests/history)中，并将它们保存到[邮递员集合](https://learning.getpostman.com/docs/postman/collections/intro_to_collections)中。然后你可以在以后重新审视这些检查请求，或者[与队友](https://learning.getpostman.com/docs/postman/collections/sharing_collections)分享这些请求。

目前，本机应用程序中的 Postman 内置代理仅捕获 HTTP 请求流量。幸运的是，大多数网站都启用了 HTTP 严格传输安全(HSTS ),以增加一层安全性。不幸的是，这意味着如果网站启用了 HSTS，内置的邮差代理就不能捕捉通过 HTTPS 发送的请求。

> 注意:截至本文发布时，Postman 内置代理捕获 HTTP 流量，但不捕获来自启用了 HSTS 的 HTTPS 网站的流量。[拦截器集成](https://trello.com/c/Ewv9DGhh/79-interceptor-integration)和 [HTTPS 代理](https://trello.com/c/S4E7egzO/78-https-proxy)将在 [Postman 开发者路线图](https://trello.com/b/4N7PnHAz/postman-roadmap-for-developers)中进行开发。

* * *

## 关于 API 逆向工程的最后一点思考

这份食谱只是你如何嗅探和检查 HTTP 流量的冰山一角，以开始了解在引擎盖下发生了什么。对于更难解决的问题，您可能需要钻研 [SSL 证书锁定](https://en.wikipedia.org/wiki/Transport_Layer_Security#Certificate_pinning)，伪造特定的客户端属性，或者学习如何对更复杂的请求进行签名和认证。

虽然这些工具和方法是强大的，可以用于自私的原因，但它们可以而且应该用于好的方面。

> 对彼此都很优秀。
> — [比尔·普莱斯顿先生](https://www.imdb.com/title/tt0096928/)

像 Postman 这样的工具可以增强客户端请求的可见性，使它们更容易复制和调整，因此您可以更快地诊断和解决问题。最终，这将帮助您更深入地理解公共或私有 API，尤其是对于那些没有很好记录的 API。

**本文原载于[邮差工程博客](https://medium.com/postman-engineering/reverse-engineering-an-api-403fae885303)T3**