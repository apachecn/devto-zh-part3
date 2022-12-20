# 使用无服务器框架环境变量。净核心λ

> 原文：<https://dev.to/gary_woodfine/using-serverless-framework-environment-variables-net-core-lambda-5e9l>

在之前的帖子中，我们探索了[无服务器框架入门](https://garywoodfine.com/net-core-serverless-framework/)和[如何实现简单的依赖注入](https://dev.to/gary_woodfine/simple-dependency-injection-in-aws-lambda-5cgj-temp-slug-4239809)，然后我们探索了如何实现与 AWS Lambda 相关的更高级的[依赖注入概念。](https://dev.to/gary_woodfine/serverless-aws-lambda-dependency-injection-5e15-temp-slug-8854152)

在每篇文章中，我们探索了不同层次的交互性，以及[无服务器框架如何帮助您开发和部署 AWS Lambda 功能](https://serverless.com/framework/docs/providers/aws/guide/intro/)和。net core 和 C#。在这篇文章中，我们将再次深入探讨一个特定的领域，如何使用无服务器框架创建环境变量，并与。net 核心可执行文件。

这是一个值得学习的有用策略，因为它帮助你创建易于配置和维护的代码基础设施，并帮助你从你的代码库中提取那些[神奇的字符串](https://dev.to/gary_woodfine/book-review-java-by-comparison-2bnc-temp-slug-8485303)。

我们将利用`serverless.yml`来创建我们的环境变量，所以如果你不太熟悉它，那么我推荐你阅读高层次的 [serverless.yml 参考](https://serverless.com/framework/docs/providers/aws/guide/serverless.yml/)，但是我会解释我们将要使用的特性。

### 无服务器框架中的环境变量

无服务器框架 YAML 文件中的环境标签使您能够创建服务范围的环境变量，您可以在其他编程语言中轻松使用这些变量，例如在基于 nodejs 的 lambda 函数中，您可以简单地在您的`serverless.yml`中创建一个环境变量，如下所示

```
Environment:
    Variables:
      TABLE_NAME: SomeTableName 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以简单地在代码中引用并获取环境变量的值，如下所示

```
const tableName = process.env.TABLE_NAME; 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 JavaScript 的许多怪癖之一，这使得等量使用它既愉快又痛苦,还因为[进程对象是一个全局对象，它提供关于当前 Node.js 进程的信息并对其进行控制](https://nodejs.org/api/process.html#process_process)

英寸 net core 然而，有一点额外的工作需要，但在我看来，至少是值得的，当谈到管理变量等。

我们将在我在[中介绍的代码基础上构建如何实现简单的依赖注入](https://dev.to/gary_woodfine/simple-dependency-injection-in-aws-lambda-5cgj-temp-slug-4239809)我们将使用的分支是`EnvironmentVariables`

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

### 如何使用环境变量。网络核心

为了扩展我们的 AWS Lambda 应用程序以利用环境变量，我们需要向我们的应用程序添加一个额外的引用。

```
dotnet add package Microsoft.Extensions.Configuration.EnvironmentVariables –version 2.1.0 
```

Enter fullscreen mode Exit fullscreen mode

添加后，我们现在可以向我们的 serverless.yml 添加一个环境变量。我们将创建一个名为 SaySomething 的环境变量，并提供一些任意语句。

```
 service: Hello-Configuration

provider:
  name: aws
  runtime: dotnetcore2.1
  environment:
    SaySomething: "Eating coconuts is cool"

package:
  individually: true

functions:
  hello:
    handler: HelloConfiguration::Threenine.ConfigTest.Functions.Speak::Greet

    package:
      artifact: bin/release/netcoreapp2.1/hello.zip 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以将我们的`LambdaConfiguration.cs`编辑到下面的

```
using System.IO;
using Microsoft.Extensions.Configuration;

namespace Threenine.ConfigTest
{
    public class LambdaConfiguration : ILambdaConfiguration
    {
        public static IConfigurationRoot Configuration => new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
            .AddEnvironmentVariables()
            .Build();

        IConfigurationRoot ILambdaConfiguration.Configuration => Configuration;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以在代码中利用这一点，如下所示，我们将编辑我们的 SpeakService 以利用环境变量的。

```
using Microsoft.Extensions.Options;

namespace Threenine.ConfigTest.Services
{
    public class SpeakService : ISpeakService
    {
        private readonly Greeting _greeting;

        public SpeakService(IOptions<Greeting> hello)
        {
            _greeting = hello.Value;

        }

        public string Greeting => string.Concat( _greeting.Message, " ", System.Environment.GetEnvironmentVariable("SaySomething"));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们构建、部署和调用我们的 lambda，这将很好地工作。然而，我确信你们当中的[干净的程序员](https://dev.to/gary_woodfine/book-review--clean-code-40ki-temp-slug-6981555)会认为这是肮脏的，我们已经污染了代码库一点点。

### 给依赖注入添加环境变量

接下来，我们将创建一个类，我们将使用它来存储环境变量，我们将通过依赖注入使它对类可用。

在这种情况下，这是一个非常简单的类，它有一个我们稍后会用到的属性。

```
 namespace Threenine.ConfigTest.Config
{
    public class SpeakEnvironment
    {
        public string SaySomething { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

的。net core framework 使我们现在能够在运行时使用[环境变量配置提供程序](https://docs.microsoft.com/en-gb/aspnet/core/fundamentals/configuration/index?view=aspnetcore-2.2#environment-variables-configuration-provider)非常容易地配置和绑定这些变量，我们现在真正要做的就是在我们的 serverless.yaml 中重命名我们的环境变量，以包含我们的类名和一个双下划线

> 在环境变量中使用分层键时，冒号分隔符(:)可能并不适用于所有平台(例如 Bash)。所有平台都支持双下划线(__)，它由冒号代替。

```
service: Hello-Configuration

provider:
  name: aws
  runtime: dotnetcore2.1
  environment:
    SpeakEnvironment__SaySomething: "Eating coconuts is cool"

package:
  individually: true

functions:
  hello:
    handler: HelloConfiguration::Threenine.ConfigTest.Functions.Speak::Greet

    package:
      artifact: bin/release/netcoreapp2.1/hello.zip 
```

Enter fullscreen mode Exit fullscreen mode

修改 StartUp 类来配置环境变量，并将它们连接起来进行依赖注入。我们在第 20 行这样做，类似于我们之前所做的。

```
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Threenine.ConfigTest.Config;
using Threenine.ConfigTest.Services;

namespace Threenine.ConfigTest
{
    public class StartUp
    {
         public static IServiceCollection Container => ConfigureServices(LambdaConfiguration.Configuration); 

                private static IServiceCollection ConfigureServices(IConfigurationRoot root)
                {
                    var services = new ServiceCollection();
                    services.Configure<Greeting>(options =>
                        root.GetSection("greeting").Bind(options));

                    services.AddTransient<ISpeakService, SpeakService>();
                    services.Configure<SpeakEnvironment>(options => root.GetSection("SpeakEnvironment").Bind(options));
                   return services;

                }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 附加配置选项

上面的例子很好，但是你可能还发现了一个额外的问题。也许您认为这很好，但是如果您想为您的特定环境提供不同的配置选项，该怎么办呢？例如，如果您希望将在登台环境中创建的数据重定向到不同于您在生产环境中使用的表或队列，该怎么办？

幸运的是，无服务器框架提供了一个非常简单的选项，一个针对 serverless.yml 的配置。按照惯例，这通常是通过创建一个额外的 env.configs.yml 来实现的

通常，您将使用该文件来创建特定于环境的配置信息或敏感信息。例如，您可能希望存储您的 AWS 帐户 ID，甚至版本或特性信息。

该文件很可能不会存储在您的源代码存储库中，而可能会由您的 DevOps 进程管理。然而，在我的案例中，我将它包含在回购协议中，以提供一个样本。

我将创建一个简单的文件来提供一个大概的想法:

```
feature: <feature_name>
version: 1.0.0.0
region: <aws_region>
accountId: <aws_account_id>
something: "Coconuts are cool to eat" 
```

Enter fullscreen mode Exit fullscreen mode

在我们的例子中，我们创建了一个变量，我们给它命名并提供一个任意值。我们现在可以编辑我们的 serverless.yml 来使用这个文件

```
 service: Hello-Configuration

provider:
  name: aws
  runtime: dotnetcore2.1
  environment:
    SpeakEnvironment__SaySomething: ${file(env.configs.yml):something}

package:
  individually: true

functions:
  hello:
    handler: HelloConfiguration::Threenine.ConfigTest.Functions.Speak::Greet

    package:
      artifact: bin/release/netcoreapp2.1/hello.zip 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

我们现在可以轻松地配置 Lambda，并且能够在我们的 Serverless.yaml 中创建环境变量，并在我们的。净核心λ。