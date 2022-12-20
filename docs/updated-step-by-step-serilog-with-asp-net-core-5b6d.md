# 逐步更新:ASP.NET 核心的 Serilog

> 原文：<https://dev.to/cmendibl3/updated-step-by-step-serilog-with-asp-net-core-5b6d>

你们中的许多人来到我的网站，一步一步地阅读我在 2016 年写的帖子[:带 ASP.NET 核心的 seri log](https://carlos.mendible.com/2016/09/19/step-step-serilog-asp-net-core/),这已经完全过时了，所以在这篇帖子中，我将向你展示如何设置 [Serilog](https://serilog.net/) 来与你的 ASP.NET 核心 2.2 应用一起工作。

## 1。创建一个 ASP.NET 核心项目

* * *

```
md aspnet.serilog.sample
    cd aspnet.serilog.sample
    dotnet new mvc 
```

Enter fullscreen mode Exit fullscreen mode

## 2。将下列依赖项添加到项目中

* * *

```
dotnet add package Microsoft.AspNetCore.App
    dotnet add package Microsoft.AspNetCore.Razor.Design
    dotnet add package Serilog.AspNetCore
    dotnet add package Serilog.Extensions.Logging
    dotnet add package Serilog.Sinks.ColoredConsole 
```

Enter fullscreen mode Exit fullscreen mode

## 3。将您的 Program.cs 文件更改为如下所示

* * *

```
public class Program
{
    public static void Main(string[] args)
    {
        // Create the logger
        Log.Logger = new LoggerConfiguration()
            .Enrich.FromLogContext()
            .MinimumLevel.Debug()
            .WriteTo.ColoredConsole(
                LogEventLevel.Verbose,
                "{NewLine}{Timestamp:HH:mm:ss} [{Level}] ({CorrelationToken}) {Message}{NewLine}{Exception}")
                .CreateLogger();

        try
        {
            CreateWebHostBuilder(args).Build().Run();
        }
        finally
        {
            // Close and flush the log.
            Log.CloseAndFlush();
        }
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseSerilog() // Set serilog as the loggin provider.
            .UseStartup<Startup>();
} 
```

Enter fullscreen mode Exit fullscreen mode

## 4。将记录器注入到服务或控制器中

* * *

更换家庭控制器并记录一些操作:

```
public class HomeController : Controller
    {
        // Logger instance
        ILogger<HomeController> logger;

        // Logger is injected into the controller
        public HomeController(ILogger<HomeController> logger)
        {
            this.logger = logger;
        }

        public IActionResult Index()
        {
            // Log something
            this.logger.LogDebug("Index was called");
            return View();
        }

        public IActionResult Privacy()
        {
            return View();
        }

        [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
        public IActionResult Error()
        {
            return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

点击下载完整代码

希望有帮助！