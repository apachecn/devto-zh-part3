# 第 020 集-前端和 HttpClient 的后端-ASP.NET 核心:从 0 到矫枉过正

> 原文：<https://dev.to/joaofbantunes/episode-020-the-backend-for-frontend-and-the-httpclient-asp-net-core-from-0-to-overkill-39e3>

在这一集中，我们将开始为 frontend 构建我们的后端，这将在 Vue.js 前端应用程序和我们将开发的后端 API 之间架起一座桥梁。目前，这主要是在 ASP.NET 核心中玩`HttpClient`的借口，因为我们将在未来改进 BFF，为不需要额外逻辑的请求提供一些代理功能。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/A8ZCVzeqFtA](https://www.youtube.com/embed/A8ZCVzeqFtA)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在这一集中，我们将开始为 frontend 构建我们的后端，这将在 Vue.js 前端应用程序和我们将开发的后端 API 之间架起一座桥梁。

前端的后端(我们称之为 BFF)并不是必需的，但当我们处理由多个服务组成的应用程序时，它会很有用，这也是我们开发 PlayBall 应用程序的目标。

拥有 BFF 的主要目的是在前端和后端之间创建一个更简单的接口。在这个项目中，Vue.js 应用程序很可能是我们将开发的唯一前端，但想象一下我们会有更多的选择(Android，iPhone，智能手表，电视，...)，BFF 将允许我们为特定前端创建定制的 API，简化客户端设备中的工作，而不是在服务器中完成，更接近支持服务。

在这一集里，我们基本上只是创建一个项目，并以此为借口在 ASP.NET 核心中摆弄`HttpClient`,看看最近出现的一些相关的使用模式。未来我们将改进 BFF，这样对服务的简单调用可以被自动代理，允许我们只在需要为前端定制 API 时编写代码。

为了在未来改进 BFF，我们将探索 [ProxyKit](https://github.com/damianh/ProxyKit) 和 [Ocelot](https://github.com/ThreeMammals/Ocelot) (或者在那之前出现的其他项目)，但那是另一个时间的主题🙂。

## 新建项目

因为前端的后端是特定于...前端(🙃)，我们将它放在 Vue.js 应用程序旁边，在同一个 [GitHub 存储库](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend)中。

这将是一个 ASP.NET 核心 2.2 应用程序，就像我们在过去的剧集中开发的 [GroupManagement](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement) 服务。在回购协议中，我们会有以下结构:

```
.
├── client
|   ├── Vue.js application files
|   └── ...
├── server
|   ├── src
|   |   └── CodingMilitia.PlayBall.WebFrontend.BackForFront.Web
|   |       ├── CodingMilitia.PlayBall.WebFrontend.BackForFront.Web.csproj
|   |       ├── BFF application files
|   |       └── ...
|   ├── CodingMilitia.PlayBall.WebFrontend.BackForFront.sln
|   └── .gitignore
├── LICENSE
└── README 
```

Enter fullscreen mode Exit fullscreen mode

在开始这一集的主要目标(玩`HttpClient`)之前，让我们做一些初始设置。从`Startup`类开始，我们希望让 MVC 工作，因为我们将在控制器中实现“代理”逻辑。我们在过去已经看到了这一点，但是作为一个复习:

`Startup.cs`

```
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services
            .AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);

            // ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseMvc();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

关于应用程序的组织，这一次，我们将创建一个`Features`文件夹，并在其中按特性划分内容，而不是“经典”`Controllers`、`Models`和`Views`文件夹。现在`GroupManagement`服务只有组逻辑，所以 BFF 只有一个`Groups`文件夹，有一个`GroupsController`和一个`GroupModel`。我们稍后会看到控制器，但是关于`GroupModel`，它是组管理服务中的一个精确副本。

`Features\Groups\GroupModel.cs`

```
public class GroupModel
{
    public long Id { get; set; }
    public string Name { get; set; }
    public string RowVersion { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 实现请求

让我们看一下我们的`GroupsController`实现，看看我们是如何满足请求的。这真的是锅炉板代码，这就是为什么我们将在后面的一集里试图摆脱它，但我认为它值得一目了然。

在深入研究代码之前，请注意以下几点:

*   我们将把`HttpClient`作为构造函数中的一个参数，稍后我们将看到如何配置它。`HttpClient`带有预先配置的组管理服务的基址。
*   我们现在不会担心处理错误，但我们将来肯定会这样做。

让我们从获取所有组的操作开始。

`Features\Groups\GroupsController.cs`

```
[Route("groups")]
public class GroupsController : ControllerBase
{
    private const string BaseAddress = "/groups";
    private readonly HttpClient _httpClient;

    public GroupsController(HttpClient httpClient)
    {
        _httpClient = httpClient;
    }

    [HttpGet]
    [Route("")]
    public async Task<ActionResult<IReadOnlyCollection<GroupModel>>> GetAllAsync(CancellationToken ct)
    {
        var response = await _httpClient.GetAsync(BaseAddress, ct);
        var result = await response.Content.ReadAsAsync<IReadOnlyCollection<GroupModel>>(ct);
        return Ok(result);
    }

    // ... 
```

Enter fullscreen mode Exit fullscreen mode

我想指出的第一件事是`GetAllAsync`方法的返回类型，因为在本系列中我还没有使用过它。通常，当实现一个 API 动作方法时，我们会将返回的模型指定为返回类型，或者如果我们需要返回一个不同的状态代码(比如 500、404 等)，我们会将其声明为一个`IActionResult`。

是一个有趣的小课堂，可以让我们两全其美。如果我们要返回一个`NotFound()`，它会工作得很好，如果我们要直接返回一个模型，它也会工作得很好。这是因为它有隐式操作符来进行这些转换。拐角处的箱子就是你在上面看到的那个。隐式操作符不与接口一起工作，因此有了`return Ok(result)`。例如，如果我用`GroupModel[]`代替，它会工作得很好。

关于 HTTP 请求，我认为没有什么奇怪的事情发生。我们用基址发出一个 GET 请求，因为我们想列出可用的组，然后读取 JSON 响应，这样我们就可以返回它。这些模型完全匹配，因此不需要任何映射逻辑。

继续执行“按 id 获取”操作。

`Features\Groups\GroupsController.cs`

```
// ...
[HttpGet]
[Route("{id}")]
public async Task<ActionResult<GroupModel>> GetByIdAsync(long id, CancellationToken ct)
{
    var response = await _httpClient.GetAsync($"{BaseAddress}/{id}", ct);
    if(response.StatusCode == HttpStatusCode.NotFound)
    {
        return NotFound();
    }
    var result = await response.Content.ReadAsAsync<GroupModel>(ct);
    return result;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

同样，更多的是相同的。我们在发出请求之前做了一些额外的 url 组合，然后我们检查是否得到了 404，以便在需要时传播该状态，否则我们解析响应并返回它，如您所见，利用了模型的隐式转换。

其余的操作大同小异，所以我就把它们放在这里供参考。

`Features\Groups\GroupsController.cs`

```
// ...
[HttpPut]
[Route("{id}")]
public async Task<ActionResult<GroupModel>> UpdateAsync(long id, GroupModel model, CancellationToken ct)
{
    var response = await _httpClient.PutAsJsonAsync($"{BaseAddress}/{id}", model, ct);
    if (response.StatusCode == HttpStatusCode.NotFound)
    {
        return NotFound();
    }
    var result = await response.Content.ReadAsAsync<GroupModel>(ct);
    return result;
}

[HttpPut]
[HttpPost]
[Route("")]
public async Task<ActionResult<GroupModel>> AddAsync(GroupModel model, CancellationToken ct)
{
    var response = await _httpClient.PostAsJsonAsync(BaseAddress, model, ct);
    var result = await response.Content.ReadAsAsync<GroupModel>(ct);
    return CreatedAtAction(nameof(GetByIdAsync), new { id = result.Id }, result);
}

[HttpDelete]
[Route("{id}")]
public async Task<IActionResult> RemoveAsync(long id, CancellationToken ct)
{
    await _httpClient.DeleteAsync($"{BaseAddress}/{id}", ct);
    return NoContent();
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 http client(ASP.NET 核心 2.1 之前)

在看我们将如何使用`HttpClient`之前，让我们先简单地讨论一下在 aside 核心 2.1 之前它通常是如何使用的。

我在过去的[帖子](https://blog.codingmilitia.com/2016/10/27/redirect-how-you-should-not-use-httpclient)中提到过这一点，但是使用`HttpClient`并不像一开始人们期望的那样简单。

`HttpClient`实现了`IDisposable`接口，所以很容易认为使用它的最佳方式是将它包装在一个`using`块中，在那里发出所需的请求，然后将其处理掉。不幸的是，这不是最好的方法，因为即使我们处理了它，它用来发出请求的套接字也不会立即释放，它会保持打开一段时间。这可能对负载不太重的应用程序没有影响，但是如果有足够的负载，我们可以开始看到由于这种行为导致的端口耗尽(更多信息[这里](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/))。

知道了这些，有什么解决办法？嗯，只需创建一个`HttpClient`并共享它(或至少有限数量的实例)。用来发出请求的方法(`GetAsync`、`PostAsync`等等)是线程安全的，所以这没有什么问题，只要我们小心处理其他的东西(例如，弄乱`BaseAddress`和`DefaultRequestHeaders`)。这并不理想，但是知道了问题所在，我们就可以绕过这个问题，对吗？

当我们似乎找到了一个不错的解决方案时，另一个问题却困扰着我们！如果我们在应用程序的生命周期中保留客户端，最终的 DNS 变化是不会被检测到的(更多信息[这里](http://byterot.blogspot.com/2016/07/singleton-httpclient-dns.html))！同样，我们通过摆弄`ServicePointManager`找到了另一个解决方案，我们准备好了(同样，点击[这里](http://byterot.blogspot.com/2016/07/singleton-httpclient-dns.html)了解更多细节)。

但是有很多奇怪的事情要经历，可能还有其他我不知道的问题。幸运的是，在 ASP.NET 核心 2.1 中，出现了一组新的特性来缓解这种痛苦，主要是新的`HttpClientFactory`。

## 使用 HttpClient

我们已经准备好发出请求的控制器，并且我们已经看到了过去使用`HttpClient`所面临的一些问题，所以现在是时候看看如何在 ASP.NET 核心> = 2.1 中使用它了。

> 在这篇文章中，我不会详细介绍新的`HttpClient`和`HttpClientFactory`使用模式，我只是用它们来实现我们在应用程序中需要的东西，但是为了更详细地阅读，史蒂夫·戈登有一个很棒的系列文章，你可以看看，从[这个](https://www.stevejgordon.co.uk/introduction-to-httpclientfactory-aspnetcore)开始。

正如我简单提到的，有一种新类型叫做`HttpClientFactory`，顾名思义，它为我们提供了`HttpClient`的实例。前面提到的关于`HttpClient`的问题并不直接源于它，而是源于它在内部用来发出请求的`HttpClientHandler`。有了这些新特性，这些处理程序就被`HttpClient`共享和使用，所以我们真的不需要担心如何处理客户端(同样，要获得更详细的解释，请查看[这篇文章](https://www.stevejgordon.co.uk/introduction-to-httpclientfactory-aspnetcore))。

我们可以将工厂直接注入到需要发出请求的类中，或者我们可以将类配置为我键入的 client，直接在构造函数中获取`HttpClient`而不需要工厂。在这种情况下，我们将使用类型化客户端，但是当然也有工厂更受欢迎的情况。

在`Startup`类中，我们将为类型化客户端添加配置。

`Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services
        .AddHttpClient<GroupsController>((serviceProvider, client) => 
        {
            // TODO: use serviceProvider to fetch the base address from configuration
            client.BaseAddress = new Uri("http://localhost:5002");
        });
    //... 
```

Enter fullscreen mode Exit fullscreen mode

尽管这段代码只是设置了基址，但是我们可以配置客户端的各个方面。我们也可以将这些配置委托给将要使用的实际类，我会说这是一个偏好问题，或者在某些情况下一个比另一个更有意义。

这应该是让它运行的方法，但事实并非如此，因为我们不是将`HttpClient`注入到一个“普通”的类中，而是将它注入到一个控制器中。控制器没有像我们注册的其他类一样注册到依赖注入容器中作为服务，所以如果我们像这样运行应用程序并向`http://localhost:5000/groups`发出请求，我们将得到一个类似
的错误

```
An unhandled exception occurred while processing the request.
InvalidOperationException: Unable to resolve service for type 'System.Net.Http.HttpClient' while attempting to activate 'CodingMilitia.PlayBall.WebFrontend.BackForFront.Web.Features.Groups.GroupsController'. 
```

Enter fullscreen mode Exit fullscreen mode

为了能够利用带有控制器的类型化客户端，我们需要返回到`ConfigureServices`中的 MVC 配置，并添加一行额外的代码来将控制器注册为服务，这样所有这些都可以很好地连接起来。

`Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services
        .AddMvc()
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
        .AddControllersAsServices();
    //... 
```

Enter fullscreen mode Exit fullscreen mode

还有其他类似的方法，比如`AddTagHelpersAsServices`，但是我们现在只需要控制器。

现在我们可以再次测试，一切都应该像预期的那样工作。

## 使用 Polly 添加重试逻辑

在这篇文章中，我们将快速浏览的最后一件事是使用[波利](https://github.com/App-vNext/Polly)让`HttpClient`在请求失败时重试。

> 我有一个关于波利的旧帖子([这里](https://blog.codingmilitia.com/2016/11/08/simpler-error-handling-in-net-applications-using-polly))和视频([这里](https://youtu.be/wBZmdx-5-Cs))。在本文中，我们将快速浏览一下 Polly 和 ASP.NET 核心中的 HttpClient 之间最近增加的集成。Polly 有更多的功能，不仅仅是我们在这篇文章中简单介绍的重试。

要进行 Polly `HttpClient`集成，我们需要安装`Microsoft.Extensions.Http.Polly` NuGet 包。然后在`Startup`类中添加几行代码，我们就万事俱备了。

`Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services
        .AddHttpClient<GroupsController>((serviceProvider, client) => {/*...*/})
        .AddPolicyHandler(
            HttpPolicyExtensions
            .HandleTransientHttpError()
            .WaitAndRetryAsync(5, attempt => TimeSpan.FromSeconds(attempt))); 
```

Enter fullscreen mode Exit fullscreen mode

`AddPolicyHandler`是我们安装的 NuGet 包的扩展方法。它允许我们配置应用于`HttpClient`的策略。策略是一个 Polly 概念，我们可以在其中包装一个动作执行，向它添加一些额外的行为，即实现一些弹性模式。策略的一些例子是重试、断路或超时。

在上面的代码中，我们为来自文档的暂时性 HTTP 错误配置了一个重试策略:范围在 500(服务器错误)和 408(请求超时)。我们有很多配置重试的选项，比如立即重试、一段时间后重试、出错时总是重试、重试若干次，以及其他一些选项。

这里采用的方法是在每次失败后重试之前等待，最多 5 次重试。每次请求失败时，在重试秒数后重试，或者更清楚地说，第一次重试将等待 1 秒，最后一次重试将等待 5 秒。

要看到这一点，我们可以到组管理服务，稍微修改一下代码，添加一些随机的异常等等🙂。

## 其他

这样做是为了先看看前端的后端，或者更好的是，一个使用`HttpClient`的借口😛。如果你认为我们在这里实现的有点傻，那么...可能是这样，因为我们有工具来为我们做这种代理，而不需要编写大量的 boiler plate 代码。如上所述，我们将在未来解决这个问题，但现在我认为应该指出`HttpClient`的新使用模式，再次提醒错误使用它可能带来的麻烦。

此外，我们可以完全绕过后端，让前端负责调用所有需要的服务。然而，我认为这将在未来给我们带来更多的灵活性，特别是对于像认证和授权这样的事情(我们将在下一集看到)，或者如果我们与不公开 HTTP API 的服务进行交互(例如，我很确定我们将在某个时候使用 [gRPC](https://grpc.io/) )。

帖子中的链接:

*   [你用错了 HttpClient，这会破坏你软件的稳定性](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/)
*   [Singleton HttpClient？小心这种严重的行为以及如何修复它](http://byterot.blogspot.com/2016/07/singleton-httpclient-dns.html)
*   [ASP.NET 核心 2.1 中的 http client factory](https://www.stevejgordon.co.uk/introduction-to-httpclientfactory-aspnetcore)
*   [波利](https://github.com/App-vNext/Polly)
*   更简单的错误处理。使用 Polly 的. NET 应用程序
*   [ProxyKit](https://github.com/damianh/ProxyKit)
*   [豹猫](https://github.com/ThreeMammals/Ocelot)

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode020)。

感谢分享和反馈！

谢谢你的来访，西阿兹！