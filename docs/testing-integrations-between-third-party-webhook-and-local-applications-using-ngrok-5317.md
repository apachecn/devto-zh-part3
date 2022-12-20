# 使用 Ngrok 测试第三方 webhook 和本地应用程序之间的集成

> 原文：<https://dev.to/renoirtech/testing-integrations-between-third-party-webhook-and-local-applications-using-ngrok-5317>

# 简介

这听起来可能很傻，但自从我开始开发 web 应用程序以来，我一直发现检查和调试我的应用程序和第三方 webhooks 之间的集成是一件痛苦的事情——除了复制事件的麻烦之外，我从来没有依赖于测试“假设的”场景——我甚至开始了一个试图解决这个问题的开源项目， 它的名字叫做 [Hookathon](https://github.com/pequitech/hookathon) ，目标是通过安装在项目中的客户端/包检查请求并将其重定向到本地环境，最终复制存储在所需项目的 bucket 中的请求，但是我们并不致力于维护项目的良好运行。

# 工具

好消息是，这不是必须的，有一个解决方案可以以一种高效、免费的方式解决所有这些问题，这就是[艾伦·什里夫](https://inconshreveable.com)创造的产品[Ngrok](https://www.ngrok.com)——它已经在微软和 Twilio 这样的巨头身上工作过。艾伦写了关于 Ngrok 的创作，并在他的博客上发表了一篇[文章，他将该产品描述为:](https://inconshreveable.com/09-25-2013/ngrok-tunnels-better-faster-stronger/)

> “Ngrok 是一种隧道反向代理，它建立从公共端点到本地运行的网络服务的安全隧道，同时捕获所有流量以供检查和重放。它是 GitHub 上的一个开源项目。”

# 我们开始吧:

现在，您已经对该工具以及我为什么找到它有了一些了解，让我们来演示如何公开一个本地环境，以允许第三方服务通过 Webhook 向本地环境提交请求非常简单，我将要介绍的两个解决方案已经过测试，并且适用于以下场景:

*   场景 1:使用 PHP 7.2.6 开发服务器的 PHP 项目
*   场景 2:使用 Laravel 开发服务器(php artisan serve)的 Laravel (PHP)项目
*   场景 3:使用 Laravel Homestead 的 Laravel (PHP)项目

为此，只需遵循以下步骤:

1.  访问 [Ngrok 网站](https://ngrok.com)
2.  注册或登录
3.  根据您的操作系统下载客户端
4.  按照下载页面上显示的说明对下载的客户端进行鉴定。

对于前两种情况，只需运行以下命令

`ngrok http <host>:<port>`

例如:ngrok http 127.0.0.0.1:666

对于第三种情况(如果您使用一个域来访问 homestead Ex: homestead.test ),您必须重写 host-header。但是不要担心，要做到这一点，只需将上面的命令增加到类似于)

`ngrok http <homestead_host_ip>:<port> -host-header=<homestead_domain>`

*示例:ngrok http 192 . 168 . 10 . 10:80-host-header = homestead . test*

瞧，现在只需在 Webhook 中指向您想要测试的 Ngrok 生成的 URL，就可以享受这个美妙的解决方案了。

对 Ngrok 有什么疑问，它是如何工作的，还有什么推荐的吗？查看[官网](https://ngrok.com/product)的“工作原理”页面。如果你是个书呆子，喜欢在开始使用之前阅读文档，请点击[官方文档](https://ngrok.com/docs)的链接。

# 学分

本文使用以下资源作为基础:

*   诺克斯回复@ Laracasts 讨论论坛:[https://Lara casts . com/discuse/channels/servers/homestead-ngrok # reply = 156596](https://laracasts.com/discuss/channels/servers/homestead-ngrok#reply=156596)
*   姆贝克的博客-使用缩略图[https://blog . mbeck . com . br/como-fazer-tunelemento-de-localhost-de-forma-se gura-ee 696 fcd 1180](https://blog.mbeck.com.br/como-fazer-tunelamento-de-localhost-de-forma-segura-ee696fcd1180)