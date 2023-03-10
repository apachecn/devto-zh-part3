# Net 核心重要功能实现

> 原文：<https://dev.to/jai00271/net-core-important-features-implementation-51f8>

作为。Net core(这里使用的版本是 2.1)日益流行，这里是我创建的一些项目，可能会派上用场。(为了更好地理解，在代码中添加了注释)。无论您想测试哪个功能，只需将这些 web API 项目设置为启动项目，就可以开始了。

知识库:[https://github.com/jai00271/AspCoreGuide](https://github.com/jai00271/AspCoreGuide)

# 1。扩展服务和 cors 实施:

在这个项目中，我们将所有的服务注入逻辑转移到一个不同的类中。我们应该避免将所有服务注入逻辑放在 ConfigureServices 方法中。同样当我们使用。Net 核心的客户端项目，如 Angular 或 react，我们也应该注意 cors 的问题。

项目名称:BasicProjectSetup

* * *

# 2。日志实现:

此项目包含默认情况下由提供的日志记录功能的实现。网芯。

项目名称:日志实现

* * *

# 3。实体框架实现:

这个项目包含两件事:

a.如何用 SQL server 实现框架
b .通过存储库设计模式实现

使用的 Nugets:微软。微软。EntityFrameworkCore.Tools(项目:EFImplementation。储存库)

文件夹名称:EFImplementation

请参考[本](https://www.learnentityframeworkcore.com/walkthroughs/aspnetcore-application)了解更多关于 EF 实施的信息。网络核心

* * *

# 4。错误处理:

这个项目包含 3 件事:

a.通过扩展 NLog 实现自定义日志。扩展.日志记录
b .内置。Net 核心中间件实现
c .定制中间件实现

文件夹名称:错误处理

努格特美元:NLog。日志记录(项目:LoggerService)

* * *

# 5。在 Web API 中实现 Swagger

这个项目包含 swagger 实现。

项目名称:SwaggerImplementation

Nuget used: Swashbuckle。AspNetCore(项目:SwaggerImplementation)

* * *

# 6。使用 XUnit 测试框架

这个项目有助于理解 XUnit 框架是如何工作的。

Folder Name: XUnitTest

Nuget used:微软。AspNetCore.App(项目:学习。单元测试)

* * *

知识库:[https://github.com/jai00271/AspCoreGuide](https://github.com/jai00271/AspCoreGuide)

*   参考文章

代号:[https://code-maze.com](https://code-maze.com)
封面图片:图片[来源](https://ardalis.com/wp-content/uplo%20ads/2017/05/aspnetcore-logo-591x360.png)

请分享您的宝贵反馈，因为它将帮助我写得更好:)