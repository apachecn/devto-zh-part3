# 如何配置 Blazor 应用程序

> 原文：<https://dev.to/rembou1/how-to-configure-a-blazor-application-4hpo>

# 如何配置 Blazor 应用程序

我们构建的大多数应用程序都需要某种配置:连接字符串、生产与否、api 密钥...客户端应用程序并没有什么不同，尽管你在配置中存储的东西不太合理，因为客户端可以访问它。在这篇博文中，我将解释如何设置我的 Blazor 项目，以访问不同环境中所需的不同配置。

## 如何定义环境

环境将决定使用哪种配置:开发、测试、生产...我们不能使用构建配置(debug，release ),因为这两件事是不同的:您可以决定使用生产配置运行带有 debug 的应用程序构建，因为您在生产中有一个 bug，或者您可以决定在您的测试环境中部署您的发布构建，因为您需要在发布之前做最后一次检查。

在 Blazor 中，我们没有任何看起来像环境的东西(我们对客户端计算机的访问是有限的)或者设置配置的可执行参数。唯一看起来像 URI 的是:Blazor 应用程序通过调用 URI 启动，就像程序通过命令行启动一样。URI 由多个部分组成:

*   协议
*   域名
*   小路
*   查询字符串 My solution 将使用第二部分和最后一部分:
*   一个环境将被绑定到一个域名(“myproject.com”将是生产环境，“localhost”将是开发环境)
*   这个环境可以被一个查询字符串参数覆盖，这样我们就可以在不重新构建应用程序的情况下切换环境。

这里的任务是做两件事:

*   决定我们在哪个环境下工作
*   基于此环境加载配置，并使其可用于应用程序的其余部分。

## 启动时查找环境

第一步是找到给定 Uri 的环境，这是我为此创建的类

```
/// <summary>
/// This class is used for picking the environment given a Uri
/// </summary>
public class EnvironmentChooser
{
    private const string QueryStringKey = "Environment";
    private string defaultEnvironment;
    private Dictionary<string, Tuple<string, bool>> _hostMapping = new Dictionary<string, Tuple<string,bool>>();

    /// <summary>
    /// Build a chooser
    /// </summary>
    /// <param name="defaultEnvironment">If no environment is found on the domain name or query then this will be returned</param>
    public EnvironmentChooser(string defaultEnvironment)
    {
        if (string.IsNullOrWhiteSpace(defaultEnvironment))
        {
            throw new ArgumentException("message", nameof(defaultEnvironment));
        }

        this.defaultEnvironment = defaultEnvironment;
    }

    public string DefaultEnvironment => defaultEnvironment;

    /// <summary>
    /// Add a new binding between a hostname and an environment
    /// </summary>
    /// <param name="hostName">The hostname that must fully match the uri</param>
    /// <param name="env">The environement that'll be returned</param>
    /// <param name="queryCanOverride">If false, we can't override the environement with a "Environment" in the GET parameters</param>
    /// <returns></returns>
    public EnvironmentChooser Add(string hostName, string env, bool queryCanOverride = false)
    {
        this._hostMapping.Add(hostName, new Tuple<string,bool>(env, queryCanOverride));

        return this;
    }

    /// <summary>
    /// Get the current environment givent the uri
    /// </summary>
    /// <param name="url"></param>
    /// <returns></returns>
    public string GetCurrent(Uri url)
    {
        var parsedQueryString = HttpUtility.ParseQueryString(url.Query);
        bool urlContainsEnvironment = parsedQueryString.AllKeys.Contains(QueryStringKey);
        if (_hostMapping.ContainsKey(url.Authority))
        {

            Tuple<string, bool> hostMapping = _hostMapping[url.Authority];
            if(hostMapping.Item2 && urlContainsEnvironment)
            {
                return parsedQueryString.GetValues(QueryStringKey).First();
            }
            return hostMapping.Item1;
        }
        if (urlContainsEnvironment)
        {

            return parsedQueryString.GetValues(QueryStringKey).First();
        }

        return DefaultEnvironment;
    }
} 
```

*   抱歉，代码样本很长
*   这个类实现了一个简单的算法，并尝试在给定配置和 uri 的情况下找到环境
*   我添加了在生产环境中忽略查询参数的能力

## 初始化配置

然后我需要使用 EnvironmentChooser 并将 IConfiguration 注入到我的服务集合中，我是这样做的:

```
public static void AddEnvironmentConfiguration<TResource>(
    this IServiceCollection serviceCollection,
    Func<EnvironmentChooser> environmentChooserFactory)
{
    serviceCollection.AddSingleton<IConfiguration>((s) =>
    {
        var environementChooser = environmentChooserFactory();
        var uri = new Uri(s.GetRequiredService<IUriHelper>().GetAbsoluteUri());
        System.Reflection.Assembly assembly = typeof(TResource).Assembly;
        string environment = environementChooser.GetCurrent(uri);
        var ressourceNames = new[]
        {
            assembly.GetName().Name + ".Configuration.appsettings.json",
            assembly.GetName().Name + ".Configuration.appsettings." + environment + ".json"
        };
        ConfigurationBuilder configurationBuilder = new ConfigurationBuilder();
        configurationBuilder.AddInMemoryCollection(new Dictionary<string, string>()
        {
            { "Environment", environment }
        });
        Console.WriteLine(string.Join(",", assembly.GetManifestResourceNames()));
        Console.WriteLine(string.Join(",", ressourceNames));
        foreach (var resource in ressourceNames)
        {

            if (assembly.GetManifestResourceNames().Contains(resource))
            {
                configurationBuilder.AddJsonFile(
                    new InMemoryFileProvider(assembly.GetManifestResourceStream(resource)), resource, false, false);
            }
        }
        return configurationBuilder.Build();
    });
} 
```

*   我使用 IUriHelper 来获取当前 Uri(它使用 JSInterop)
*   InMemoryFileProvider 主要取自这里([https://stackoverflow.com/a/52405277/277067](https://stackoverflow.com/a/52405277/277067))，我只是更改了接受流作为输入的类
*   我使用惯例来读取资源(文件夹配置，然后是 appsettings.json 和 appsettings。{环境}。json)
*   我还添加了环境名，它可以在以后用于调试目的
*   要做到这一点，你需要添加微软。扩展。配置和微软。Extensions.Configuration.Json 添加到项目中
*   我可以用这个配置调用 JSInterop，这样我的 js 代码也可以使用它。

现在我在《创业》中这样称呼它。配置服务

```
services.AddEnvironmentConfiguration<Startup>(() => 
            new EnvironmentChooser("Development")
                .Add("localhost", "Development")
                .Add("tossproject.com", "Production", false)); 
```

*   Startup 泛型参数用于选择嵌入配置文件的程序集。

还有一件事要做:我需要在启动时强制初始化我的 singleton。如果我不这样做，那么当用户第一次导航并使用 IConfiguration 时，URI 查询参数可能不再在这里。我选择在启动时这样做:

```
//In Startup.Configure
public void Configure(IComponentsApplicationBuilder app)
{
    //force config initialisation with current uri
    IConfiguration config = app.Services.GetService<IConfiguration>();
} 
```

## 创建文件

现在我需要创建 3 个文件(它们都不是强制的，因为我检查它们是否存在) :

*   Configuration/appsettings.json
*   配置/应用程序设置。开发. json
*   配置/应用程序设置。生产. json

并将它们设置为我的项目 csproj:

```
<ItemGroup>
    <EmbeddedResource Include="Configuration\appsettings.json" />
    <EmbeddedResource Include="Configuration\appsettings.*.json" />
</ItemGroup> 
```

例如，这是我的应用程序设置。开发. json

```
{  "title":  "Welcome to Toss Development"  } 
```

## 用法

要使用它，您只需在需要的地方注入 IConfiguration

```
@inject Microsoft.Extensions.Configuration.IConfiguration configuration
<h1>@configuration["title"]</h1> 
```

## 结论

这个项目仍然显示了 Blazor 的一大优势:你可以使用在。net 在浏览器中运行。当 Blazor 发布时，我们将不再需要等待 1-2 年来获得所有需要的库(i18n、配置、序列化...)，他们已经在那里很多年了。

你可以在我的项目中找到这篇博文的大部分代码([https://github.com/RemiBou/Toss.Blazor](https://github.com/RemiBou/Toss.Blazor))。

## 引用

*   [https://docs . Microsoft . com/en-us/aspnet/core/fundamentals/configuration/？view = aspnetcore-2.2 #文件配置提供者](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2#file-configuration-provider)
*   [https://stackoverflow.com/a/52405277/277067](https://stackoverflow.com/a/52405277/277067)