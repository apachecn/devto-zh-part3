# 使用 ASP.NET 核心和 Ocelot 实现简单的微服务基础设施

> 原文：<https://dev.to/mfaghfoory/implementing-a-simple-micro-service-infrastructure-using-asp-net-core-and-ocelot-55gh>

今天，我将使用 ASP.NET 核心和 Ocelot 创建一个微服务基础架构。
你可能知道，微服务架构为我们带来了很多好处，因为我们可以拥有独立的服务，例如产品服务、销售服务、管理服务等，我们可以为他们建立独立的团队，甚至我们可以使用不同的技术和工具，例如，我们可能希望设计和实现使用 PHP 的电子邮件服务，而我们应用程序的核心部分是在。Net 平台。
通过使用微服务架构，我们可以完全隔离它们，并且我们获得的另一个好处是扩展服务。假设我们有一个销售服务，它会遇到很多请求。托管它的服务器在一段时间后无法响应最终用户。因此，如果我们架构和基础设施是微服务，我们可以在不同的服务器上运行和调用服务，而不是增加主服务器的内存或 CPU。
你可以在这里阅读单体和微服务架构。
[https://articles . micro services . com/monolithic-vs-micro services-architecture-5c 4848858 f59](https://articles.microservices.com/monolithic-vs-microservices-architecture-5c4848858f59)

这是我将要创建的基础设施的抽象图。真的不完全是这样，但你可以从中获得灵感来创造这样的东西。
[![Micro services infrastructure](img/b931ea1766d802ca0765d7cff1772f3f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--tQtaDzsq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h2q9hzcg0k0li2rdl4g0.png)

实际上，我将创建两个名为“ProductService”和“EmailService”的服务。你可以通过下面的链接阅读所有相关的文档，我将尝试使用这个示例项目的最少选项来进行更多的说明。https://ocelot.readthedocs.io/en/latest
。
这是我的项目结构
[![Micro services infrastructure](img/936ff894638d34bf16df2f7f529ad839.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wmgCuVvR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/muv3u02l27hj6v5qx708.JPG)

好，我们这里有三个项目。首先，“MainWebApp”充当我们的 API 网关，它将请求重定向到其他服务。因此，我们的应用程序的切入点将是这个项目，最终用户将他们的请求发送到这个应用程序，而不是直接通过电子邮件服务或产品服务。可能你出于很多原因想阻止用户直接与子服务连接。假设您有来自每个服务的几个实例，它们运行在具有不同 URL 和端口的不同机器上，并且您希望在它们前面有一个负载平衡器，因此您应该通过应用网络规则来防止用户看到子服务的路径。然后，您将拥有一个入口点(称为 API gateway ),所有最终用户都将与这个入口点进行通信。我已经把这个项目上传到了 Github 上，你可以通过下面的链接看到。
[https://github.com/mfaghfoory/SimpleMicroservices](https://github.com/mfaghfoory/SimpleMicroservices)
如果你决定看看那个，试一试，首先你要运行服务项目，然后运行 MainWebApp。您将通过这些 URL 访问服务:
localhost:4110/product service/product，
localhost:4110/email service/email
基于 MainWebApp 项目中 ocelot.json 文件中的路由定义。
最后，我很高兴看到关于我在 dev.io 上的首次尝试的任何评论和问题