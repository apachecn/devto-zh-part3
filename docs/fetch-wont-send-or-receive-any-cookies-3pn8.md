# Fetch 不会发送或接收任何 Cookies

> 原文：<https://dev.to/solrevdev/fetch-wont-send-or-receive-any-cookies-3pn8>

## Fetch 不会发送或接收任何 cookies🍪

今天我想我已经修复了一个困扰我很久的错误。

一个严重的案例[‘在我的机器上工作’](https://blog.codinghorror.com/the-works-on-my-machine-certification-program/)

我有一些使用外部 API 的代码，该 API 一旦通过身份验证，就会在稍后进行另一个 API 调用来获取终端用户最近的图像。

这非常有效…

除了一些用户报告他们一旦登录就看不到他们的图像。

然而，在很长一段时间里，我无法重现这一幕。它可以在我的机器上运行，可以在 macOS、Windows 和 Linux 上运行，可以在 safari、chrome 和 firefox 上运行。它在 iPhone 6s 上运行良好。

所以，我添加了[日志](https://github.com/serilog/serilog-aspnetcore)，然后更多日志。我甚至注册了一个试用账户 [Browserstack](https://www.browserstack.com/) ，尝试尽可能多的不同浏览器和设备，但仍然无法重现这个问题。

最后，在测试其他东西的时候，我用苹果的 iOS 模拟器和运行 iOS 11.2 的 iPhone 6S 重现了这个错误。

当谈到 bug 修复代码时，能够重现 bug 确实是成功的一半。

因此，有了一些断点和对正在发生的事情的更清晰的想法，我最终能够修复错误并推出一个工作应用程序。

窃听器？

嗯，我不是 100%确定如何解释它，但是前端有一些 JavaScript 代码使用[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 来调用 [ApiController](https://docs.microsoft.com/en-us/aspnet/core/web-api/?view=aspnetcore-2.2) 来检查会话变量，并基于该值将数据返回到前端客户端。

对于大多数浏览器和我的开发环境来说，下面的代码足以进行调用并获得正确的数据:

```
fetch(this.apiUrl) 
```

但是，对于某些浏览器，我需要修改 fetch API 调用来指定 cookies 和凭证也必须随请求一起发送。

这是实现这一点的代码，也是我提交的修复代码。

```
fetch(this.apiUrl, {
        method: 'GET',
        credentials: 'include'
      }) 
```

fetch 的文档确实在一定程度上指出了这个问题

> 默认情况下， **fetch 不会从服务器发送或接收任何 cookie**，如果站点依赖于维护用户会话，则会导致未经验证的请求(要发送 cookie，必须设置 credentials init 选项)。
> 
> 自 2017 年 8 月 25 日。规范将默认凭据策略更改为同源。火狐从 61.0b13 开始改变。
> 
> –[使用获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)

为什么只有某些浏览器需要这样做，我不确定，但是我的解决方案似乎有效。

成功🎉