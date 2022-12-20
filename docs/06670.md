# 在控制台应用程序中使用 HttpClientFactory 进行 API 调用

> 原文：<https://dev.to/gary_woodfine/making-api-calls-with-httpclientfactory-in-console-applications-541d>

。Net 核心控制台应用程序是大多数开发人员为了实现某种基于自动化的任务而经常开发的最流行的应用程序。

我之前已经讨论过如何在中使用 IHost。net 核心控制台应用程序开发者可以创建异步 Linux 守护进程和[如何使用配置 API。net core 控制台应用](https://garywoodfine.com/configuration-api-net-core-console-application)。

在这篇文章中，我将详述这两个例子，并提供一个如何使用的例子。net 核心控制台应用程序和 **HttpClient** 来开发一个 Linux 守护进程，与基于 Web 的 API 进行交互，以提取和处理数据。

### 什么是 HttpClient

英寸 net core[HTTP client 类](https://docs.microsoft.com/en-us/previous-versions/visualstudio/hh193681(v=vs.118))提供了一个基类，用于发送 HTTP 请求和从 URI 标识的资源接收 HTTP 响应。

在以前的版本中，on.net 框架开发人员误用了 **HttpClient** ，通常将它包装在 using 块中，主要是因为 HttpClient 实现了 Dispose 方法。

`using`语句是 C#语言处理可处置对象的一个特性。一旦`using`块完成，则一次性物体。事实上，许多重构工具，如 [Jetbrains Resharper](https://www.jetbrains.com/resharper/) ，包括我个人最喜欢的跨平台工具。如果你不这么做，net IDE [Jetbrains Rider](https://www.jetbrains.com/rider/) 会明确警告你。然而，在这种情况下，处理掉`HttpClient`是错误的。

不幸的是，`HttpClient`实现了鼓励错误行为的`IDisposable`。

调用`dispose`方法，并清理所有正在使用的资源。这是中的典型模式。NET，用于从数据库连接到流编写器的所有事情。任何具有外部资源的对象必须使用`IDisposable`接口清理。

毫无疑问，您会在几乎所有代码库中找到类似的代码，其中 HttpClient 用于下载资源或连接到 API。

```
 using (var client = new HttpClient())
    {
        var content = await client.GetStringAsync(Url);
        return JsonConvert.DeserializeObject<List<Model>>(content);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这段代码的潜在问题是，虽然这个 HttpClient 类是可处置的，但将它与`using`语句一起使用并不是最佳选择，因为即使当您处置`HttpClient`对象时，底层套接字也不会立即被释放，这可能会导致一个名为[“套接字耗尽”的严重问题。](https://making.pusher.com/ephemeral-port-exhaustion-and-how-to-avoid-it/)

`HttpClient`旨在实例化一次，并在应用程序的整个生命周期中重用。为每个请求实例化一个`HttpClient`类将耗尽重载下可用的套接字数量。

解决这个问题的可能方法是基于将`HttpClient`对象创建为单例或静态的。然而，HttpClient 并没有被设计成一个单独的或静态的对象，因为它不考虑 DNS 的变化。

英寸 net core 2.1 引入了一个新的`HttpClientFactory`，用于通过集成 [Polly](https://github.com/App-vNext/Polly) 来实现弹性 HTTP 调用，这是一个. net 弹性和瞬时故障处理库，允许开发人员以流畅和线程安全的方式表达策略，如重试、断路器、超时、隔板隔离和回退。

## 什么是 HttpClientFactory

`HttpClientFactory`是一种创建 HttpClient 实例的新机制，这些实例在后台得到适当的管理。HttpClientFactory 管理处理程序的生命周期，这样我们就有了一个可以重用的处理程序池，同时还可以对它们进行轮换，以便考虑 DNS 更改。

使用 HttpClient 的昂贵部分实际上是创建 HttpClientHandler 和连接。通过在 HttpClientFactory 中汇集这些资源，可以更有效地使用连接。使用 HttpClientFactory 请求 HttpClient，每次都会创建一个新的实例，这意味着无需担心改变其状态。HttpClient 可能使用也可能不使用池中现有的 HttpClientHandler，因此使用现有的打开连接。

### 如何使用 HttpClientFactory

像中的大多数对象一样。net 核心框架中，HttpClientFactory 已经过优化，因此可以通过依赖注入来使用它。我将举例说明使用 HttpClientFactory 时采用的一种典型方法，在这种方法中，我将开发一个 Linux 守护进程，它将用于联系 Rest API 端点来提取和处理数据。

为此，我们将在中使用 IHost。net 核心控制台应用程序，并进一步扩展典型的使用场景。我们还将看到一个例子[如何使用配置 API。net core 控制台应用](https://dev.to/gary_woodfine/how-to-use-configuration-api-in-net-core-console-application-2o7a-temp-slug-5976270)。

在这个场景中，我们生成一个简单的控制台应用程序，然后修改代码以利用 IHost 来实现依赖注入、日志记录和配置。您将需要添加一些包，详细内容可以在参考文章中找到。

我还添加了必需的 json 文件:

*   应用程序设置. json
*   appsettings。开发. json
*   appsettings。生产. json
*   主机设置. json

```
class Program
    {
        private const string EnvironmentVariablePrefix = "ADZUNA_FEED_";
        public static async Task Main(string[] args)
        {

            var host = new HostBuilder()
                .ConfigureHostConfiguration(hostConfig =>
                {
                    hostConfig.SetBasePath(Directory.GetCurrentDirectory());
                    hostConfig.AddJsonFile("hostsettings.json", true);
                    hostConfig.AddEnvironmentVariables(EnvironmentVariablePrefix);
                    hostConfig.AddCommandLine(args);
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.SetBasePath(hostingContext.HostingEnvironment.ContentRootPath);
                    config.AddJsonFile("appsettings.json", true, true);
                    config.AddJsonFile($"appsettings.{hostingContext.HostingEnvironment.EnvironmentName}.json");
                })
                .ConfigureServices((hostContext, services) =>
                    {
                        services.AddLogging();

                    }
                )
                .Build();
            await host.RunAsync();                      
        } 
```

Enter fullscreen mode Exit fullscreen mode

> 信息
> 
> 在**里。net core 3.0** 你将能够生成所有
> 这个锅炉电镀代码作为**工人服务模板**可用的项目

既然已经配置了基本的项目模板，我们就可以开始了。正如您可能从代码中推断的那样，我们将要开发的服务将连接到 Adzuna API 服务来提取数据。但是，我将尝试使这些概念尽可能通用，这样您就可以理解并连接到任何选择的 Web API 服务。

在连接我们的 HttpClientFactory 之前，我们需要做的第一件事是编辑我们的`appsettings.[Environment].json`文件，以便包含我们需要的配置数据

```
{
   "adzuna": {
      "key" : "",
      "secret": "",
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到我们将存储我们需要的配置细节，这些是你注册使用 [Adzuna API 时收到的凭证。](https://developer.adzuna.com/)

Adzuna API 使开发者能够通过 Adzuna 的 API 获得最新的广告、工作、房产和汽车广告以及数据。Adzuna 的最新就业数据为您自己的网站、报告和数据可视化提供动力。

在特定的场景中，我还想创建一个配置类来包含我将在应用程序中使用的基本 URL。所以我创建了另一个 POCO 类，如下所示。

```
 [JsonObject("baseUrls")]
    public class BaseUrls
    {
        [JsonProperty("adzuna")]
        public string Adzuna { get; set; }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们将详细信息添加到我们的环境特定的应用程序设置文件中，如下所示:

```
{
   "adzuna": {
      "key" : "",
      "secret": ""

   },
   "baseUrls": {
      "adzuna" : "http://api.adzuna.com/v1/api/"
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

> 信息
> 
> 在包含基地址
> 时，一定要确保包含结尾的 **/** ，因为这是唯一可行的排列方式。

有了详细信息，我们现在可以编辑我们的`Program.cs`来启用依赖注入，并从我们的应用程序设置文件
中检索配置详细信息

```
 dotnet add package Microsoft.Extensions.Http –version 2.2.0 
```

Enter fullscreen mode Exit fullscreen mode

我们现在准备开始为我们的服务编写代码，添加一个我们称之为`JobProcessingService`的新类，它将实现`IHostedService`接口，该接口为主机管理的对象定义了两个方法。

我们将在这里开发的守护进程，可能会每小时运行几次，并且至少会运行几个作业，因此使用令牌构建某种取消过程对我们来说很重要。为了这篇文章的目的，我将实现一个“足够好”的方法，但这绝不是一个完整的方法，它只是有一些基础，但至少提供了一些所需的理解。

```
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Threenine.Job.Feed.Entity;
using Threenine.Job.Feed.Interfaces;

namespace Threenine.Job.Feed.Services
{
    public class JobProcessingService : IHostedService, IDisposable
    {
        private CancellationTokenSource _cts;
        private Task _currentTask;

        private readonly IDownloadService<AdzunaFeed> _service;
        public JobProcessingService(IDownloadService<AdzunaFeed> service)
        {
            _service = service;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _cts = CancellationTokenSource.CreateLinkedTokenSource(cancellationToken);

            // In later version of this task we will get a list of
            //skills which we will be make subsequent calls
            //however for now I just provided an example
          _currentTask = _service.GetFeed("C#",_cts.Token);

            return   _currentTask.IsCompleted ?  _currentTask : Task.CompletedTask;
        }

        public async Task StopAsync(CancellationToken cancellationToken)
        {

            if (_currentTask == null)
            {
                return;
            }

            try
            {
                _cts.Cancel();
            }
            finally
            {
               await Task.WhenAny(_currentTask, Task.Delay(Timeout.Infinite, cancellationToken));
            }
        }

        public void Dispose()
        {
            _cts.Cancel();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果后台任务完成，我们将进入实现`HttpClient`的服务的实际实现，同样，这里的实现仅足以提供用于演示目的的实现的味道，但是您将看到足够多的如何实现 HttpClient 并在真实场景中使用它。

这个实现中的代码的主要缺点是我们调用检索数据并反序列化它，但是我们实际上并没有做任何事情！但它只是提供了一个如何使用`HttpClient`和`HttpClientFactory`
的例子

```
using System;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Options;
using Threenine.Job.Feed.Config;
using Threenine.Job.Feed.Entity;
using Threenine.Job.Feed.Interfaces;

namespace Threenine.Job.Feed.Services
{
    public class AdzunaDownloadService : IDownloadService<AdzunaFeed>
    {
        private readonly HttpClient _client;
        private readonly AdzunaCredentials _creds;

        public AdzunaDownloadService(HttpClient client, IOptions<AdzunaCredentials> creds)
        {
            _client = client;
            _creds = creds.Value;
        }

        public async Task<AdzunaFeed> GetFeed(string criteria, CancellationToken token)
        {
            var requestUrl = $"jobs/gb/search?app_id={_creds.Key}&app_key={_creds.Secret}&what={criteria}";
            var response = await _client.GetAsync(requestUrl, token);

            if (!response.IsSuccessStatusCode) throw new ApplicationException();

            var stream = await response.Content.ReadAsStreamAsync();
            return stream.DeserializeFromJson<AdzunaFeed>();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到我在 Stream 类上使用了一个扩展方法来`DeserializeFromJson`我想为了清楚起见我会包括这个扩展方法。这个方法的代码在一个名为`StreamExtensions.cs`
的新类中

```
using System;
using System.IO;
using Newtonsoft.Json;

namespace Threenine.Job.Feed
{
    public static class StreamExtensions
    {
        public static T DeserializeFromJson<T>(this Stream stream)
        {
            if (stream == null) throw new ArgumentNullException(nameof(stream));

            if (!stream.CanRead) throw new NotSupportedException("can't read from stream");

            using (var sread = new StreamReader(stream))
            using (var jsonText = new JsonTextReader(sread))
            {
                var serialiser = new JsonSerializer();
                return serialiser.Deserialize<T>(jsonText);
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这些部分的代码几乎已经完成，我们现在只需要连接我们的应用程序启动。

我们现在将编辑 Program.cs，如下图所示

```
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Threenine.Job.Feed.Config;
using Threenine.Job.Feed.Entity;
using Threenine.Job.Feed.Interfaces;
using Threenine.Job.Feed.Services;

namespace Threenine.Job.Feed
{
    class Program
    {
        private const string EnvironmentVariablePrefix = "ADZUNA_FEED_";

        public static async Task Main(string[] args)
        {
            var host = new HostBuilder()
                .ConfigureHostConfiguration(hostConfig =>
                {
                    hostConfig.SetBasePath(Directory.GetCurrentDirectory());
                    hostConfig.AddJsonFile("hostsettings.json", true);
                    hostConfig.AddEnvironmentVariables(EnvironmentVariablePrefix);
                    hostConfig.AddCommandLine(args);
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.SetBasePath(hostingContext.HostingEnvironment.ContentRootPath);
                    config.AddJsonFile("appsettings.json", true, true);
                    config.AddJsonFile($"appsettings.{hostingContext.HostingEnvironment.EnvironmentName}.json");
                })
                .ConfigureServices((hostingContext, services) =>
                    {
                        var baseUrls = new BaseUrls();
                        hostingContext.Configuration.GetSection("baseUrls").Bind(baseUrls);
                        services.Configure<AdzunaCredentials>(options =>
                            hostingContext.Configuration.GetSection("adzuna").Bind(options));

                        services.AddHttpClient<IDownloadService<AdzunaFeed>, AdzunaDownloadService>(client =>
                        {
                            client.BaseAddress = new Uri(baseUrls.Adzuna);
                            client.DefaultRequestHeaders
                                .Accept
                                .Add(new MediaTypeWithQualityHeaderValue("application/json"));
                        });
                        services.AddHostedService<JobProcessingService>();
                        services.AddLogging();
                    }
                )
                .Build();
            await host.RunAsync();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

我们已经看到了如何在. net 控制台应用程序中使用 HttpClientFactory 来创建将在 HostedService 应用程序中使用的 HttpClient 实例的示例。