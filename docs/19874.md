# 针对 ASPNET 核心服务器设置带有 E2E 测试的 Azure DevOps CI 管道

> 原文：<https://dev.to/rembou1/setup-an-azure-devops-ci-pipeline-with-e2e-tests-against-a-aspnet-core-server-48im>

在我的项目 Toss 中，我有经典的单元/集成测试，也有使用 Selenium WebDriver 的端到端测试。我需要这些测试有两个原因:

*   您总是需要它们:)因为这是集成的最后一步，当您编写单元/集成测试时，您无法想到所有可能出错的事情。在这里，你可以 99%确定你的应用程序的基本使用不会被一个版本破坏。
*   Blazor 是一项实验性技术，每隔 1-2 个月就会有新版本推出，可能会带来突破性的变化。我需要一种方法来了解升级后我的应用程序是否仍然正常。

大多数情况下，E2E 测试是作为部署过程的一部分运行的，因为您需要部署整个系统:您的网站，还有您的依赖项(数据库、文件系统、web 服务器、其他服务...)

## 硒铬网络驱动

Selenium Chrome 驱动程序是一项技术，它可以实现软件和浏览器之间的通信，因此我们可以像人类一样运行测试。英寸净你用包[硒。Chrome.WebDriver](https://www.nuget.org/packages/Selenium.Chrome.WebDriver/) 。您的代码将如下所示:

```
[Fact]
public void FullE2eTest()
{
    var driver = new ChromeDriver();
    driver.Navigate().GoToUrl("http://localhost:9898/");
    Assert.Equal("TOSS", driver.Title);
} 
```

*   我用 xUnit 来运行这个
*   这只是去我的应用程序的主页，并检查页面标题

## 启动 ASPNET 核心服务器

这里的一个挑战是在测试运行器(xUnit)中运行 ASPNET 核心应用程序，这样由 web 驱动程序操纵的 chrome 实例就能够运行测试。为此，我从 [Blazor repo](https://github.com/aspnet/Blazor) 获得了解决方案。ASPNET Core 可以运行它自己的名为 Kestrel 的 web 服务器。我们需要在后台线程中使用正确的参数执行 BuildWebHost，如下所示:

```
public class AspNetSiteServerFixture : IDisposable
    {
        public FakeEmailSender EmailSender { get; private set; }
        public Uri RootUri => _rootUriInitializer.Value;

        public IWebHost Host { get; set; }

        private readonly Lazy<Uri> _rootUriInitializer;

        public AspNetSiteServerFixture()
        {
            _rootUriInitializer = new Lazy<Uri>(() =>
                new Uri(StartAndGetRootUri()));
        }     

        private static string FindClosestDirectoryContaining(
            string filename,
            string startDirectory)
        {
            var dir = startDirectory;
            while (true)
            {
                if (File.Exists(Path.Combine(dir, filename)))
                {
                    return dir;
                }

                dir = Directory.GetParent(dir)?.FullName;
                if (string.IsNullOrEmpty(dir))
                {
                    throw new FileNotFoundException(
                        $"Could not locate a file called '{filename}' in " +
                        $"directory '{startDirectory}' or any parent directory.");
                }
            }
        }

        protected static void RunInBackgroundThread(Action action)
        {
            var isDone = new ManualResetEvent(false);

            new Thread(() =>
            {
                action();
                isDone.Set();
            }).Start();

            isDone.WaitOne();
        }

        protected string StartAndGetRootUri()
        {
            Host = CreateWebHost();
            RunInBackgroundThread(Host.Start);
            EmailSender = Host.Services.GetService(typeof(IEmailSender)) as FakeEmailSender;
            return Host.ServerFeatures
                .Get<IServerAddressesFeature>()
                .Addresses.Single();
        }

        public void Dispose()
        {
            // This can be null if creating the webhost throws, we don't want to throw here and hide
            // the original exception.
            Host?.StopAsync();
        }

        protected IWebHost CreateWebHost()
        {
            var solutionDir = FindClosestDirectoryContaining(
                          "Toss.sln",
                          Path.GetDirectoryName(typeof(Program).Assembly.Location));
            var sampleSitePath = Path.Combine(solutionDir, typeof(Toss.Server.Program).Assembly.GetName().Name);

            return Toss.Server.Program.BuildWebHost(new[]
            {
                "--urls", "http://127.0.0.1:0",
                "--contentroot", sampleSitePath,
                "--environment", "development",
                "--databaseName",CosmosDBFixture.DatabaseName,
                "--test","true"
            });
        }
    } 
```

*   这是一个很大的代码，对不起，但我找不到任何
*   我们在 xUnit 中使用了一个 Fixture，它将在测试的整个执行过程中一直存在
*   我们需要获得网站内容根来交付静态内容(像我的 Blazor 应用程序二进制文件)，这是由 FindClosestDirectoryContaining 完成的
*   我发送“- test ”,这样我的服务器将使用我无法设置的东西(如电子邮件平台或 stripe)的假依赖项运行
*   我只需要将这个 Fixture 注入到我的测试中，这样我就能够获得带有 RootUri 的服务器 URL

## 作伪依赖

现在我需要在 ASPNET 核心端读取测试参数，这样我就不会注入我的依赖项的真正实现。我是这样做的:

```
public void ConfigureServices(IServiceCollection services)
{
    // Add application services.
    if (Configuration.GetValue<string>("test") == null)
    {
        services.AddTransient<IRandom, RandomTrue>();
        services.AddTransient<IEmailSender, EmailSender>();
        services.AddSingleton<IStripeClient, StripeClient>(s => new StripeClient(Configuration.GetValue<string>("StripeSecretKey")));
    }
    else
    {
        services.AddSingleton<IRandom, RandomFake>();
        //We had it as singleton so we can get the content later during the asset phase
        services.AddSingleton<IEmailSender, FakeEmailSender>();
        services.AddSingleton<IStripeClient, FakeStripeClient>();
    }
} 
```

*   我需要随机注射，这样我的测试结果是可预测的
*   用这个代码“EmailSender = Host。services . get service(type of(iemail sender))作为 FakeEmailSender"，我可以得到系统要发送的电子邮件的内容(如激活链接)。

## Azure DevOps 集成

在 Azure DevOps 上运行这些测试应该不会比在本地机器上难，但是有一些事情需要做。

首先，我们需要使用安装在代理上的 web 驱动程序。webdriver 路径作为环境变量传递，因此我们可以像这样读取它

```
public class BrowserFixture : IDisposable
    {
        public IWebDriver Browser { get; }

        public ILogs Logs { get; }

        public ITestOutputHelper Output { get; set; }

        public BrowserFixture()
        {
            var opts = new ChromeOptions();
            var binaryLocation = Environment.GetEnvironmentVariable("ChromeWebDriver");
            if (string.IsNullOrEmpty(binaryLocation))
            {
                binaryLocation = ".";
            }

            var driver = new ChromeDriver(binaryLocation,opts,TimeSpan.FromMinutes(3));
            Browser = driver;
        }

        public void Dispose()
        {
            Browser.Dispose();
        }
    } 
```

*   这个 fixture 将被注入到我所有的 E2E 测试中，所以它将只运行一次
*   webDriver 构造函数的第三个参数给出了默认的命令超时。似乎虚拟机代理有点慢，所以你必须耐心等待(但它是免费的)...)

我还需要解决一些事情:

*   模态前台需要更长的时间才能消失，所以我必须像这样添加一个等待

```
_webDriveWaitDefault.Until(b => !b.FindElements(By.CssSelector(".modal-backdrop")).Any()); 
```

*   应用程序 appsettings.json 的 secret 必须用空值填充，因为机密存储在开发人员的计算机上
*   我不得不强制网络驱动全屏显示，因为一些按钮默认是不可见的

```
Browser.Manage().Window.FullScreen(); 
```

## 结论

设置起来并不难，但是 VM 代理非常慢，所以每次测试大约需要 10 分钟。现在，我有了一个有效的 CI 渠道，您可以在这里看到[https://dev.azure.com/remibou/toss/_build?definitionId=1](https://dev.azure.com/remibou/toss/_build?definitionId=1)。

## 引用

*   [https://github.com/aspnet/Blazor](https://github.com/aspnet/Blazor)
*   [https://docs . Microsoft . com/en-us/azure/devo PS/pipelines/test/continuous-test-selenium？view=vsts](https://docs.microsoft.com/en-us/azure/devops/pipelines/test/continuous-test-selenium?view=vsts)