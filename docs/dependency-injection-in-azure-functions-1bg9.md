# Azure 函数中的依赖注入

> 原文：<https://dev.to/cesarcodes/dependency-injection-in-azure-functions-1bg9>

对于我们这些在. net 核心领域工作的人来说，依赖注入是必须的。在 ASP.NET 核心中，我们在`ConfigureServices`的`Startup.cs`中配置我们的服务容器。

在 Azure 函数中，我们有一个类似的方法来做到这一点。你要做的第一件事是从 NuGet 那里拿到`Microsoft.Azure.Functions.Extensions`包。接下来您要做的是在您的项目中创建一个`Startup.cs`类。最后，注册程序集并从`FunctionsStartup`类(从`Microsoft.Azure.Functions.Extensions.DependencyInjection`)继承，覆盖它的 Configure 方法，如下图所示。