# 高级无服务器 AWS Lambda 依赖注入

> 原文：<https://dev.to/gary_woodfine/advanced-serverless-aws-lambda-dependency-injection-5el6>

我之前已经提供了一个简单的例子，说明如何将简单的依赖注入和配置引入到你的无服务器框架 AWS Lambda 中，在这篇文章中，我们将更深入一点，学习如何完全实现依赖注入以及如何布局你的 Lambda 项目。

使用框架和模板创建项目时的问题是，它经常引导开发人员认为实现解决方案的唯一方法是遵循项目模板提供的指导方针。

事实是，当谈到*无服务器框架*时，唯一真正重要的是 YAML 文件，它主要用于创建云形成脚本，以创建和管理 Lambda 和部署脚本使用的 AWS 资源。当涉及到你的 lambda 项目的实际代码和结构以及它包含的代码时，它真的不在乎。

当生成一个新的 *AWS Lambda* 项目时，*无服务器框架*可能只生成最简单的项目。一个几乎没有任何特色的项目。net 开发人员通常也被使用，实现他们需要的所有特性的任务就落在了他们身上。

在这篇文章中，我将带你了解如何在你的 AWS Lambda 项目中实现完整的依赖注入、配置，甚至利用 IHost 功能。在 AWS Lambda 项目中，您将面临的唯一限制是，在撰写本文时，您只能使用**。网络核心 2.1**

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

### 开始一个新的 AWS Lambda 项目

如果你没有使用过或者不熟悉无服务器框架，那么我建议你阅读[入门。NET 核心和无服务器框架。](https://dev.to/gary_woodfine/getting-started-with-net-core-and-the-serverless-framework-1kge-temp-slug-5669462)它将指导您完成安装，提供对无服务器框架的基本理解，并生成一个典型的项目。在本教程的剩余部分，我假设您已经掌握了这些知识。

> 信息
> 
> 我们将继续开发 AWS Lambda 中[简单依赖
> 注入中的代码](https://dev.to/gary_woodfine/simple-dependency-<br>%0Ainjection-in-aws-lambda-5cgj-temp-slug-4239809)

让我们生成一个新的无服务器项目

```
sls create -t  aws-csharp -p HelloConfiguration -n Threenine.ConfigTest 
```

Enter fullscreen mode Exit fullscreen mode

事实是我们可以删除。net 项目创建并重新开始，因为在生成的项目中唯一对我们有用的东西是`serverless.yml`，`build.sh`但是我们将使用我们已经得到的东西！

我们将在这里进行一次重构，整理生成的项目，以更好地适应我们的约定等。在以后的文章中，我将详细介绍如何使用*无服务器框架*创建您自己的定制项目模板，这样您就不必为您开始的每个项目做这种样板式的繁重工作。

首先，我们需要重命名我们的程序集，使其更符合我们的目的。当你从事微服务开发和开发许多 lambda 时，你并不真的想要 100 个 lambda 都被命名为`aws-csharp`和一个默认的名称空间`AwsDotnetCsharp`，它实际上并没有描述你正在开发的组件，并且违背了软件设计的[哲学](https://garywoodfine.com/philosophy-of-software-design)

在您使用的任何 IDE 或文本编辑器中，重命名程序集和设置默认命名空间的过程都是正常的。我主要使用[Jet Brains Rider——一种快速&的强大跨平台。NET IDE](https://www.jetbrains.com/rider/) ，所以我的过程会稍微不同于大多数。然而，有一些重要的编辑需要对您的`serverless.yml`进行，以确保您不会实现突破性的更改。

```
 service: Hello-Configuration

provider:
  name: aws
  runtime: dotnetcore2.1

package:
  individually: true

functions:
  hello:
    handler: HelloConfiguration::Threenine.ConfigTest.Handler::Hello

    package:
      artifact: bin/release/netcoreapp2.1/hello.zip 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到我简单地编辑了第 12 行来表示我已经将我的程序集重命名为`HelloConfiguration`并且我的项目的名称空间现在是`Threenine.ConfigTest.`对于这个演示代码来说，这就足够了，我只是想强调你可以编辑这些项目文件来更好地满足你的需要。

### 添加一个启动类

在 AWS Lambda 中的简单依赖注入的示例代码中，我们遵循了一些非常糟糕的编码实践，创建了一个做所有事情的类，这并不可靠，可能会导致各种各样的维护噩梦，因此我们对该类进行了一些重构，并将核心功能组件拆分到它们自己的类中。在开发时，我总是牢记自适应代码的！

所以让我们添加一个新的类，按照惯例我们称之为`StartUp.cs`

我们将稍微重构我们的`ILambdaConfiguration`和`LambdaConfiguration`，以利用代表图标配置层次结构
的根的`IConfigurationRoot`

```
public interface  ILambdaConfiguration
    {
        IConfigurationRoot Configuration { get; }
    } 
```

Enter fullscreen mode Exit fullscreen mode

```
public class LambdaConfiguration : ILambdaConfiguration
    {
        public static IConfigurationRoot Configuration  => new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
            .Build();

        IConfigurationRoot ILambdaConfiguration.Configuration => Configuration;
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们新的启动类将是我们连接应用程序将要使用的依赖项和配置数据的地方。

因此，在我们真正这样做之前，我们必须创建我们的服务，并对我们的应用程序进行更多的重构。在我的例子中，我删除了`Handler.cs`文件，创建了一个名为*函数*的新目录，并在该目录中创建了一个名为`Speak.cs`的新类

为了演示，我创建了一个名为 Services 的附加目录，并创建了一个接口`ISpeakService`和一个名为`SpeakService`
的类

```
public interface ISpeakService
    {
        string Greeting { get; }
    } 
```

Enter fullscreen mode Exit fullscreen mode

```
 public class SpeakService : ISpeakService
    {
        private readonly Greeting _greeting;
        public SpeakService(IOptions<Greeting> hello)
        {
            _greeting = hello.Value;
        }

        public string Greeting => _greeting.Message;
    } 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到使用了[选项模式](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-2.1)的`SpeakService`提供了一种验证配置数据的机制。我们似乎还使用了一个名为 Greeting 的类，让我们继续创建这个类

```
[JsonObject("greeting")]
    public class Greeting
    {
        [JsonProperty("message")]
        public string Message { get; set; }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以在启动类
中连接我们的依赖关系

```
public class StartUp
    {
        public static IServiceCollection Container => ConfigureServices(LambdaConfiguration.Configuration);

        private static IServiceCollection ConfigureServices(IConfigurationRoot root)
        {

            var services = new ServiceCollection();

            //Wire up all your dependencies here
            services.Configure<Greeting>(options =>
                root.GetSection("greeting").Bind(options));

            services.AddTransient<ISpeakService, SpeakService>();

            return services;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在 Speak.cs 类中，我们现在将利用所有的依赖项。您会注意到，我们将创建 2 个类构造函数，其中一个将接受 IServiceProvider，另一个将通过初始化启动类来实例化该类。

我们的问候方法将从 IOC 容器中获取一个服务并使用它。我们利用了[服务提供者。GetService](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovider.getservice?view=aspnetcore-2.1) 方法获取指定类型的服务对象。

```
 public class Speak
    {
        private readonly IServiceProvider _serviceProvider;

        public Speak(IServiceProvider serviceProvider)
        {
            _serviceProvider = serviceProvider;
        }

        public Speak() : this(StartUp.Container.BuildServiceProvider())
        {
        }

        [LambdaSerializer(typeof(Amazon.Lambda.Serialization.Json.JsonSerializer))]
        public string Greet(ILambdaContext context)
        {
            var talk = _serviceProvider.GetService<ISpeakService>();

            return talk.Greeting;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们需要更新我们的无服务器 yaml 来考虑我们的重构

```
service: Hello-Configuration

provider:
  name: aws
  runtime: dotnetcore2.1

package:
  individually: true

functions:
  hello:
    handler: HelloConfiguration::Threenine.ConfigTest.Functions.Speak::Greet

    package:
      artifact: bin/release/netcoreapp2.1/hello.zip 
```

Enter fullscreen mode Exit fullscreen mode

我们还将稍微修改我们的 AppSettings 文件来创建一个问候部分

```
{
    "greeting" : {
        "message": "This is from the configuration"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 构建、部署和调用

我们现在可以构建我们的 lambda

```
 sudo ./build.sh 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以部署我们的 Lambda

```
 sls deploy 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以调用它来查看一切是否如预期的那样工作

```
 sls invoke -f hello 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

在这个例子中，我们已经使我们的 lambda 能够利用一些很好的。net 核心特性的依赖注入和配置。这将使开发人员能够整理他们的 lambda 函数，帮助组织他们的代码，还可以使用额外的库。