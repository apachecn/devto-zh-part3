# AWS Lambda 中的简单依赖注入。网络核心

> 原文：<https://dev.to/gary_woodfine/simple-dependency-injection-in-aws-lambda-net-core-n0g>

我之前讨论过如何[使用无服务器框架](https://garywoodfine.com/net-core-serverless-framework/)开发. net 核心 AWS Lambda，以提供底层操作系统和执行运行时的抽象，这样您就可以使用云计算范例更容易地实现规模经济。

在这篇文章中，我将详述这些概念，并介绍如何将依赖注入和配置等概念引入 lambda 函数。

无服务器框架使您能够轻松地创建和管理 Lambda 组件使用的资源，并创建一个简单的存根函数，使您能够专注于开发您的函数。

这样做的一个问题是，开发人员可能认为他们不能实现任何其他开发项目中所有常见的好东西，从我不得不在野外处理的一些代码来看，这肯定是真的！

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[hello configuration](https://github.com/garywoodfine/HelloConfiguration)

### AWS Lambda 和。净核心依赖注入

<article class="markdown-body entry-content container-lg" itemprop="text">

# Hello 配置

源代码支持一系列博客文章，详细介绍如何在 AWS Dotnet core lambdas 中使用 Dotnet Core 配置和依赖注入

*   [AWS Lambda 中的简单依赖注入](https://garywoodfine.com/dependency-injection-in-aws-lambda/)
*   [无服务器 AWS Lambda 依赖注入](https://garywoodfine.com/serverless-aws-lambda-dependency-injection/)
*   [使用无服务器框架环境变量。净核心λ](https://garywoodfine.com/using-serverless-framework-environment-variables-net-core-lambda/)

## 捐赠

Hello Configuration 是一个由 Threenine.co.uk 开发和支持的免费教程

如果你愿意捐款，让开发商备有咖啡和披萨，我们将不胜感激。

[![Donate via PayPal](img/5e585a0543a740733050b31bfa2bfad9.png)](https://www.paypal.me/geekiam/)

[![threenine logo](img/25c489cba46459755b2ff6eaaf9d122a.png)](https://threenine.co.uk/)

</article>

[View on GitHub](https://github.com/garywoodfine/HelloConfiguration)

## AWSλ和。网络核心

AWS Lambda 支持大量的[编程语言和运行时](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html)，以及[定制运行时](https://docs.aws.amazon.com/lambda/latest/dg/runtimes-custom.html)，支持使用任何语言和执行环境。标准运行时列表中包括微软[。NET Core](https://en.wikipedia.org/wiki/.NET_Core) ，一个开源的跨平台运行时，你可以使用 [C#编程语言](https://en.wikipedia.org/wiki/C_Sharp_(programming_language))在上面构建应用。

当使用无服务器框架模板生成项目时，它缺少设置配置和依赖注入的常用钩子。您需要自己添加必要的代码。我将带您完成实现所有需要的样板代码的过程。

为了生成一个新的无服务器 lambda 项目，我们将使用下面的无服务器命令:

```
sls create -t  aws-csharp -p HelloConfiguration -n Threenine.ConfigTest 
```

Enter fullscreen mode Exit fullscreen mode

信息

要了解如何安装无服务器框架以及这些命令是做什么的，我强烈建议你阅读[入门。NET 核心和无服务器框架](https://dev.to/gary_woodfine/getting-started-with-net-core-and-the-serverless-framework-1kge-temp-slug-5669462)

项目生成后，我们可以开始清除`Handler.cs`并删除那里的大部分代码，这样我们只剩下下面的

```
using Amazon.Lambda.Core;
using System;

[assembly:LambdaSerializer(typeof(Amazon.Lambda.Serialization.Json.JsonSerializer))]

namespace AwsDotnetCsharp
{
    public class Handler
    {

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 添加。对 lambda 的净核心依赖注入

任何时候你直接创建一个对象，你都是在把自己耦合到一个特定的实现上。因此，很明显，你会想要应用相同的[控制反转](https://en.wikipedia.org/wiki/Inversion_of_control)模式和[。NET Core Dependency Injection](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.2)good to your AWS Lambda function 就像你在标准的【ASP.NET T4】核心 Web API 项目中一样。

在 AWS Lambda 项目中使用 IOC 和依赖注入代码的额外好处是，您最终可以利用 [AWS Lambda 层](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html)，这使您能够配置您的 Lambda 函数，以层的形式获取额外的代码和内容。

层是包含库、自定义运行时或其他依赖项的 ZIP 存档。使用层，您可以在函数中使用库，而无需将它们包含在部署包中。

首先，我们需要连接依赖注入，因此为了做到这一点，我们需要在项目中添加以下引用

```
dotnet add package Microsoft.Extensions.Hosting –version 2.1.0
dotnet add package Microsoft.Extensions.Configuration –version 2.1.0
dotnet add package Microsoft.Extensions.Configuration.Json –version 2.1.0 
```

Enter fullscreen mode Exit fullscreen mode

请注意版本号，这是因为 AWS lambda 目前仅支持。网芯 2.1.0。

在下一步中，让我们添加一个新的接口，我们将其命名为`ILambdaConfiguration.cs`

```
using Microsoft.Extensions.Configuration;

namespace AwsDotnetCsharp
{
    public interface  ILambdaConfiguration
    {
        IConfiguration Configuration { get; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 实现 Lambda 配置

我们现在可以添加一个类来实现这个接口，我们可以把它想象成`LambdaConfiguration.cs`

最初，我们将使我们的 Lambda 能够从一个`appsettings.json`文件中读取配置设置，所以在我们添加代码之前，先将该文件添加到您的项目中。按照惯例，只需添加一个空的`json`文件。net 开发人员将这个文件命名为`appsettings.json`,但是你可以随意命名。

您的 aws-csharp.csproj 现在应该看起来类似于这个

```
 <Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <GenerateRuntimeConfigurationFiles>true</GenerateRuntimeConfigurationFiles>
    <AssemblyName>CsharpHandlers</AssemblyName>
    <PackageId>aws-csharp</PackageId>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Amazon.Lambda.Core" Version="1.0.0" />
    <PackageReference Include="Amazon.Lambda.Serialization.Json" Version="1.3.0" />
    <PackageReference Include="Microsoft.Extensions.Hosting" Version="2.1.0" />
       <PackageReference Include="Microsoft.Extensions.Configuration" Version="2.1.0" />
    <PackageReference Include="Microsoft.Extensions.Configuration.Json" Version="2.1.0" />
  </ItemGroup>

  <ItemGroup>
    <DotNetCliToolReference Include="Amazon.Lambda.Tools" Version="2.2.0" />
  </ItemGroup>

  <ItemGroup>
    <None Remove="appsettings.json" />
    <Content Include="appsettings.json">
      <CopyToOutputDirectory>Always</CopyToOutputDirectory>
    </Content>
  </ItemGroup>

</Project> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以编辑 lambda 配置，从我们的应用程序设置
中获取数据

```
using System.IO;
using Microsoft.Extensions.Configuration;

namespace AwsDotnetCsharp
{
     public class LambdaConfiguration : ILambdaConfiguration
    {
        public IConfiguration Configuration  => new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
                .Build();

    }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以从简单的 lambda 中完成代码。因此，编辑 Handler.cs 以包含以下代码。

```
using Amazon.Lambda.Core;
using Microsoft.Extensions.DependencyInjection;

[assembly:LambdaSerializer(typeof(Amazon.Lambda.Serialization.Json.JsonSerializer))]

namespace AwsDotnetCsharp
{
    public class Handler
    {
        private ILambdaConfiguration Configuration { get; }

        public Handler()
        {
            var serviceCollection = new ServiceCollection();
            ConfigureServices(serviceCollection);
            var serviceProvider = serviceCollection.BuildServiceProvider();
            Configuration = serviceProvider.GetService<ILambdaConfiguration>();
        }

        public string Hello( ILambdaContext context)
        {
            return LambdaConfiguration.Configuration["hello"] ?? "None";
        }

        private void ConfigureServices(IServiceCollection serviceCollection)
        {
            serviceCollection.AddTransient<ILambdaConfiguration, LambdaConfiguration>();
        }

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这些代码所做的基本上就是连接依赖注入和配置应用程序。我们已经创建了一个简单的 Hello lambda 方法，它唯一能做的就是从应用程序设置文件中读取一个值，一个我们称之为 Hello 的键。

我们的应用程序设置文件相当简单，看起来像

```
{
    "hello": "This is from the configuration"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 构建并部署 Lambda

我们现在可以使用无服务器框架来构建和部署我们的 lambda。我通常在我的 Linux 机器上工作，所以我将利用`build.sh.`构建应用程序。我保持 yaml 文件非常简单，它包含下面的

```
service: Hello-Configuration # NOTE: update this with your service name

provider:
  name: aws
  runtime: dotnetcore2.1

package:
  individually: true

functions:
  hello:
    handler: CsharpHandlers::AwsDotnetCsharp.Handler::Hello

    package:
      artifact: bin/release/netcoreapp2.1/hello.zip 
```

Enter fullscreen mode Exit fullscreen mode

使用构建命令

```
sudo ./build.sh 
```

Enter fullscreen mode Exit fullscreen mode

然后，一旦构建完成，我们可以使用
简单地将我们的应用程序部署到 AWS

```
 sls deploy 
```

Enter fullscreen mode Exit fullscreen mode

部署完成后，我们可以简单地通过如下调用来测试我们的功能

```
 sls invoke -f hello 
```

Enter fullscreen mode Exit fullscreen mode

然后，无论您在 appsettings 文件中放置了什么值，我们都应该看到输出的响应

## 总结

在这个简单的教程中，我们发现了如何实现依赖注入的基本实现，我们还提供了一个示例，说明如何利用应用程序配置来利用 appsettings 来存储 AWS Lambda 的附加配置信息，并在运行时读取这些信息。

如果您想学习更高级的技术，了解如何在 AWS Lambda 中用。net core 检出[无服务器 AWS Lambda 依赖注入](https://garywoodfine.com/serverless-aws-lambda-dependency-injection)