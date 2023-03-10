# 从 HTTP/1 到 HTTP/2 的演变如何改变了网络

> 原文：<https://dev.to/lambdatest/how-evolution-of-http2-from-http1-changed-the-web-191g>

你有没有遇到过这样的情况，你真的需要快速打开一个网页，但由于网速慢，它总是花很长时间？可能是你的网络中有人正在下载一个堵塞带宽的洪流？

挺气人的吧？

在这个 5G 时代，缓慢的页面加载速度对于用户和开发者来说都是无缝用户体验的祸根。我们不能控制用户的网速，但是作为开发者，我们可以确保我们尝试了书中的每一个技巧，让用户的网络之旅尽可能的顺畅。

这就是 HTTP 协议的用武之地。

### 什么是 HTTP？

HTTP(超文本传输协议)帮助我们获得流畅的上网体验。它于 1996 年首次推出。它是协作、分布式和超媒体信息系统的应用层协议，是万维网数据通信的基础。HTTP 是为 web 浏览器和 web 服务器之间的通信而设计的，它充当客户端和服务器之间的桥梁。

基本上，它用于传输超媒体文档，如 HTML。HTTP 由 URL 和超文本构成。它是客户机-服务器计算系统中的请求-响应协议。HTTP/1.1 的第一个定义，即通常使用的 HTTP 版本，出现在 1997 年。

### HTTP/1.1 中的限制

HTTP/1.1 是对 HTTP 1 的更新，早在 1999 年就被标准化了。HTTP/1.1 帮助人们在线开展业务，但是 HTTP/1.1 中有一些限制，这些限制后来在 v2 中得到更新。

*   加载网页的延迟
*   行首阻塞问题
*   在单个 TCP 连接上多路复用多个请求
*   每个资源文件的不同资源请求
*   HTTP 头很长，导致网页加载延迟

那时的网络非常不同，网页和互联网连接速度都很慢。但是这些年来，我们使用网络的方式发生了巨大的变化，现在的网站比过去复杂得多。

HTTP 的主要更新于 2015 年作为 HTTP/2 发布，解决了 HTTP/1.1 中面临的问题。HTTP/2 源自早期的实验性 SPDY 协议，由 Google 开发。它使 web 应用程序更快、更简单、更健壮。

[![crossbrowsertesting](img/5e17db035dfb1c5d8b4fa3bf8825925e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e2IHmC9R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/03/Comparison-1.jpg)

### 如何切换到 HTTP/2

HTTP/2 向后兼容 HTTP/1.1。但是，要切换到 HTTP/2，您需要做以下事情:

*   **采用 HTTPS 并获得 TLS 证书**
    你需要做的第一件事是将你的网站从不安全的 http 迁移到安全的 https。

*   使用图像精灵代替多个文件
    将你所有的小资源文件转换成一个大精灵，帮助 HTTP/2 解决资源排队问题。

*   **内联添加图片**
    HTTP/1.1 中多 HTTP 请求问题需要做的另一项工作是使用数据 URIs 在 CSS 中内联图片。

*   **连接 JavaScript 和 CSS 文件**
    将您的多个 JavaScript 和 CSS 文件合并并优化为一个简单的优化 JSS 和 CSS 文件

*   **域分片**
    将你的资源文件分割到不同的子域，因为 HTTP2 不需要域分片。

### HTTP/2 如何解决 HTTP/1.1 的局限性

HTTP/2 侧重于 HTTP/1.1 中关注的具体目标和问题:

*   创建允许客户端和服务器选择使用 HTTP 1.1 或 2.0 的算法
*   保持方法、状态代码、URI 和标题字段与 HTTP/1.1 的高度兼容性
*   减少延迟以提高网页加载速度
*   支持常见的现有 HTTP 用例，如桌面 web 浏览器、移动 web 浏览器、web APIs、各种规模的 web 服务器、代理服务器、反向代理服务器、防火墙和内容交付网络。

### 哪些浏览器支持 HTTP/2？

现在大多数浏览器都支持 HTTP/2。浏览器列表包括最新版本的 [Chrome](https://www.lambdatest.com/test-on-chrome-browsers) 、 [Firefox](https://www.lambdatest.com/test-on-firefox-browsers) 、 [Safari](https://www.lambdatest.com/test-on-safari-browsers) 、 [Internet Explorer](https://www.lambdatest.com/test-on-internet-explorer-browsers) 、 [Opera](https://www.lambdatest.com/test-on-opera-browsers) 等。你可以使用 Akamai 的工具来检查你的浏览器是否支持 HTTP/2。

### HTTP/2 如何帮助提高网页加载速度

**报头压缩**
报头字段在请求或响应行之后传输。HTTP/1.1 请求和响应没有被压缩并以纯文本形式传输，这导致下载了大量不必要的字节。

但是在 HTTP/2 中，报头字段被压缩并以二进制代码传输，使得用户计算机加载更加紧凑和有效。

标头压缩减少了客户端请求的开销，从而降低了带宽并减少了页面加载时间。

**服务器推送**
在 HTTP/1.1 中，当你的浏览器连接到一个请求网页的 web 服务器时，首先发送 HTML 文件，然后你的浏览器请求其他资源文件，如 [CSS、javascript](https://www.lambdatest.com/blog/top-programming-languages-helpful-for-testers/) 等。但是在 HTTP2 服务器推送中，除了对原始请求的响应之外，服务器可以对单个客户机请求发送多个响应，而客户机不必显式地请求每个响应。

[![crossbrowsertesting](img/3a070f2619893bb308658d3b90c0b1ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7S53qEJP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/03/Karim-1.jpg)

如果你曾经通过数据 URI 添加过内联 CSS、javascript 或任何其他源文件，那么你就已经体验过服务器推送。通过手动将源文件添加到 URI，我们将该源文件推送到客户端，而无需等待客户端请求。我们可以用 HTTP/2 实现同样的结果，并获得额外的性能优势。

**在单个 TCP 连接上复用多个请求**

HTTP/1.1 的局限性之一是它只能在一个连接上一个接一个地请求文件——这意味着请求文件，等待响应，下载文件，然后请求下一个文件。这造成了行首阻塞和底层 TCP 连接使用效率低下的问题。
[![](img/d8345d308b279e34d07eae4430b6ed9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fn46GgSL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/03/RahimJi-1.jpg)
HTTP/2 中新的二进制成帧层消除了这些限制，并通过允许客户机和服务器将 HTTP 消息分解成独立的帧，交错它们，然后在另一端重新组合它们，实现了完全的请求和响应多路复用。

**流优先级**
一旦一条 HTTP 消息可以被分割成许多单独的帧，HTTP/2 就允许多路复用来自多个流的帧。客户端和服务器交错和交付帧的方式成为关键的性能问题。为了执行这一点，HTTP/2 标准将权重和依赖性与每个流相关联。

*   每个流可以被分配 1 到 256 之间的整数权重。
*   每个流可以被给予对另一个流的显式依赖。

[![](img/50c98e9d144953af77c2ba1d04c00f92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_OqKF4Ew--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/03/RamJi-1.jpg)

流依赖性和权重的组合允许客户端构建和传达“优先化树”,该树表示它将如何偏好接收响应。反过来，服务器可以使用此信息，通过控制 CPU、内存和其他资源的分配来确定流处理的优先级，并且一旦响应数据可用，就分配带宽，以确保向客户端最佳地传送高优先级响应

### HTTP/2 的优势

切换到 HTTP/2 的好处是立竿见影的。大多数用户都在使用可以利用该协议的浏览器，这个数字每天都在上升。

*   **更安全:**默认情况下，HTTP/2 总是开启加密，这导致跨站点的高安全性。
*   **移动友好:**所有移动站点都允许大量请求，以防止来自报头的数据字节被下载，这是报头压缩特性的结果。
*   更快的页面加载时间:在消除了协议的许多障碍后，HTTP/2 有望比当前的标准更快。
*   **兼容 HTTP/1.1:** HTTP/2 兼容 HTTP/1.1。为了确保尽可能平稳地切换到新协议，方法、状态代码、报头字段和 URIs 都保持与 HTTP/1.1 相同。
*   **减少对替代方案的依赖:**多路复用功能解决了减少服务器请求数量的高耗时方法的问题——如域分片、图像精灵或内嵌 Javascript 和 CSS——将不再是必不可少的。

### HTTP/2 的缺点

在转向新协议之前，企业还应该考虑一些限制。

*   照顾你的观众:虽然大多数用户使用的浏览器可以利用该协议，但也有一些人继续使用旧的浏览器。除此之外，并不是所有的 web 服务器都支持 HTTP/2。因此，当前的页面加载速度优化过程仍然是必要的。
*   **HTTPS 要求:**对于不使用 HTTPS 的站点，从 HTTP/1.1 迁移会更加困难，因为 HTTP/2 的前提条件是站点已经在 HTTPS 上。因此，将网站切换到 HTTPS 将需要所有的 HTTP 网址转换到 HTTPS。由于搬到 HTTPS 对搜索引擎来说是一个积极的因素，因此，升级你的网站会很棒。
*   **页面加载性能可能很低:**从 HTTP/1.1 切换到 HTTP/2 可能会花费大量的时间和资源，因此确保这对网站速度的影响是值得的，这一点很重要。如果一个网站已经针对页面加载速度进行了优化，那么性能提升可能会比预期的要小。
*   HTTP/2 未知事实: HTTP 仍然是新的，因此必须进行大量的研究，以确定最佳实践和可能的缺点。

原文来源:[lambdatest.com](https://goo.gl/4C3epq)2019 年 3 月 21 日。

相关文章:

1.  [SaaS 公司的 20 个设计灵感](https://www.lambdatest.com/blog/20-design-inspirations-for-saas-companies/)
2.  [自动化测试的机器学习](https://www.lambdatest.com/blog/machine-learning-for-automation-testing/)
3.  什么是无代码自动化测试，为什么它是未来！
4.  [对测试人员有帮助的顶级编程语言](https://www.lambdatest.com/blog/top-programming-languages-helpful-for-testers/)