# Go 语言的服务器端应用程序框架

> 原文：<https://dev.to/geosoft1/server-side-application-skeleton-for-go-language-did>

如果你关心登录过程，包括登录、退出、密码恢复和最小的用户管理(如更改名称、密码和删除帐户)，那么在 Go 中编写 web 应用程序可能会非常困难。

该解决方案可以是一个高层次的框架，以帮助所有开发人员专注于应用程序内容。

该项目正在进行中，托管在 Github 上，并附有代码示例和一些图片。

[https://github.com/geosoft1/ssas](https://github.com/geosoft1/ssas)

事情是如何运作的？假设您对 Go 语言和 Mysql 数据库有一些经验，从存储库下载项目并编译。

您必须在您的服务器上创建一个数据库，并将`database.sql`文件导入到(用您的数据库名调整之前的文件)。在`model`文件夹中，您将找到一个 Mysql Workbench 应用程序的模型。您可以使用此模型来同步现有的数据库。

接下来，您必须完成配置文件`conf.json`。基本上，您必须设置数据库连接信息，如 IP/域、用户、密码和模式名。

此外，您可能需要将邮件程序设置为密码恢复程序。这没什么大不了的，使用一个为此目的创建的邮箱。

如果你第一次运行这个框架，你会被要求添加一个新用户(或者注册)。

[![signup](img/c02169ec8536e2b3cff6f2b8f020324b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--daUVSek5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/geosoft1/ssas/raw/master/statimg/signup.png)

请随意这样做，然后登录

[![signin](img/974dff8073d45a136555ca59252ade36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Gwzskd5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/geosoft1/ssas/raw/master/statimg/signin.png)

之后，你必须看到主页

[![home](img/47c55ae4ecbeb7e0253267148bd133b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Z9hJyAv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/geosoft1/ssas/raw/master/statimg/home.png)

您可以通过点击右上角的选项来管理本地用户帐户、注销和恢复忘记的密码。

[![user](img/2ae38c4dfa661ee4dfac56c4285a5e5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---UiwYh-p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/geosoft1/ssas/raw/master/statimg/user.png)

一个很好的部分是，你有一个网络故障的后备离线页面。

[![home](img/dc3e1e64ea14459b80bd86348f6be133.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qnxGIIy1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/geosoft1/ssas/raw/master/statimg/cache.png)

现在您已经准备好编写您的应用程序，请看一下文档中的[如何添加简单的应用程序](https://github.com/geosoft1/ssas#adding-a-simple-application)部分，看看如何用新功能扩展框架。一个虚拟例子解释了这一点。

请注意，您将需要一个 HTML5 浏览器来查看应用程序，但您应该不会有现代浏览器的问题。你也可以使用 Chrome [应用](https://developers.google.com/web/fundamentals/app-install-banners/)模式，获得更接近原生应用的体验。

享受吧。