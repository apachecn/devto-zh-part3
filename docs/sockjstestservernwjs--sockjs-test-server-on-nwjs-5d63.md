# sockjs _ test _ server _ nwjs–NW . js 上的 sock js 测试服务器

> 原文：<https://dev.to/jalbam/sockjstestservernwjs--sockjs-test-server-on-nwjs-5d63>

“ [sockjs_test_server_nwjs](https://github.com/jalbam/sockjs_test_server_nwjs) 只是一个简单的 [SockJS](https://github.com/sockjs) 服务器，运行在 [Node.js](https://nodejs.org/) 上，但是使用 [NW.js(以前的 node-webkit)](https://nwjs.io/) 来提供一个简单的可视化界面，用于测试目的。它是在 2016 年 7 月 29 日(大约)制定的。

<figure>[![](img/a2f13e4f8030df4179b3c27cb446228b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r8TcePpJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://joanalbamaldonado.com/blog/wp-content/uploads/2018/12/sockjs_screenshot.gif) 

<figcaption>截图</figcaption>

</figure>

默认监听端口设置为 *9999* ，但可以轻松修改。

它附带了一个用于 Windows 32 位的自解压单文件二进制文件(使用 [7zip](https://www.7-zip.org/links.html) 的 SFX ),可以在最低版本为 SP2 (Service Pack 2)的 Windows XP 上运行，也可以在更新的 Windows 版本如 Windows 10(使用 [Web2Executable](https://github.com/jyapayne/Web2Executable) 生成)上运行，但也包括源代码，因此它可以很容易地移植到 [NW.js](https://nwjs.io/) 支持的其他平台上。

可以在 GitHub 上找到:[https://github.com/jalbam/sockjs_test_server_nwjs](https://github.com/jalbam/sockjs_test_server_nwjs)