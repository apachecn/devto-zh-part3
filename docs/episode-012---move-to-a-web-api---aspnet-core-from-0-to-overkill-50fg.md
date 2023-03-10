# 第 012 集-移动到 Web API-ASP.NET 核心:从 0 到过度

> 原文：<https://dev.to/joaofbantunes/episode-012---move-to-a-web-api---aspnet-core-from-0-to-overkill-50fg>

在这一集里，我们开始将当前的应用程序转换成一个 Web API，这样它就可以在我们将要开发的单页面应用程序中使用。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/vxDvAGP9Bh8](https://www.youtube.com/embed/vxDvAGP9Bh8)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

到目前为止，我们已经构建了一个服务器端渲染的 MVC 应用程序。然而，这个项目的目标是最终实现一个处理大部分 UI 需求的单页面应用程序。为了实现这一点，我们将把这个 MVC 应用程序转换成一个 Web API，这样 SPA(或者其他可能需要它的组件)就可以使用它和我们将来构建的其他组件。

另外，我称之为 Web API 或 HTTP API，而不是 REST API。我不想因为称它为 REST API 而不遵循所有隐含的需求(包括但不仅仅是超媒体)来实现它而惹恼任何人。假设它是一个 RESTish API(😛)，因为它实现了大多数人对它的期望，但并不完全符合 REST。

## 去掉不需要的位

由于我们正在转向 Web API，有些东西我们不再需要，可以清理/调整，即:

*   移除视图
*   用`AddMvcCore`替换`AddMvc`，只添加所需的 MVC 特性
*   从`ControllerBase`而不是`Controller`继承

关于第一个，没有太多要说的，只是删除视图的文件夹，因为我们不再需要它们了。关于其他的，让我们进入更多细节。

### 用 AddMvcCore 替换 AddMvc

在 DI 容器 MVC 的服务中注册，那些它肯定需要的服务和一些它可能需要的服务。为此，它调用`AddMvcCore`并添加更多的东西。我们可以通过直接调用`AddMvcCore`来避免注册不需要的服务，以及我们知道需要的任何其他 MVC 服务。

这会对性能有影响吗？可能没有那么多(虽然没有测量它)，但是既然我们已经在做了，我们就去掉一些不用的部分，更好地看看一些可用的 MVC 特性。但是大多数时候，保持冷静，不要担心🙂

我们来看看 GitHub 上的 [`MvcServiceCollectionExtensions.cs`](https://github.com/aspnet/AspNetCore/blob/v2.2.1/src/Mvc/src/Microsoft.AspNetCore.Mvc/MvcServiceCollectionExtensions.cs) 文件，这里定义了`AddMvc`。

```
// ...

public static class MvcServiceCollectionExtensions
{
    /// <summary>
    /// Adds MVC services to the specified <see cref="IServiceCollection" />.
    /// </summary>
    /// <param name="services">The <see cref="IServiceCollection" /> to add services to.</param>
    /// <returns>An <see cref="IMvcBuilder"/> that can be used to further configure the MVC services.</returns>
    public static IMvcBuilder AddMvc(this IServiceCollection services)
    {
        // ...

        var builder = services.AddMvcCore();

        builder.AddApiExplorer();
        builder.AddAuthorization();

        AddDefaultFrameworkParts(builder.PartManager);

        // Order added affects options setup order

        // Default framework order
        builder.AddFormatterMappings();
        builder.AddViews();
        builder.AddRazorViewEngine();
        builder.AddRazorPages();
        builder.AddCacheTagHelper();

        // +1 order
        builder.AddDataAnnotations(); // +1 order

        // +10 order
        builder.AddJsonFormatters();

        builder.AddCors();

        return new MvcBuilder(builder.Services, builder.PartManager);
    }

    private static void AddDefaultFrameworkParts(ApplicationPartManager partManager)
    {
        var mvcTagHelpersAssembly = typeof(InputTagHelper).GetTypeInfo().Assembly;
        if (!partManager.ApplicationParts.OfType<AssemblyPart>().Any(p => p.Assembly == mvcTagHelpersAssembly))
        {
            partManager.ApplicationParts.Add(new FrameworkAssemblyPart(mvcTagHelpersAssembly));
        }

        var mvcRazorAssembly = typeof(UrlResolutionTagHelper).GetTypeInfo().Assembly;
        if (!partManager.ApplicationParts.OfType<AssemblyPart>().Any(p => p.Assembly == mvcRazorAssembly))
        {
            partManager.ApplicationParts.Add(new FrameworkAssemblyPart(mvcRazorAssembly));
        }
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我清理了文件中一些我们现在要找的东西并不真正需要的部分。

查看`AddMvc`，我们看到它做的第一件事是调用`AddMvcCore`，将返回的`IMvcBuilder`存储在一个变量中，以便进一步配置，所以这也是我们需要做的事情。现在让我们看看使用构建器配置的其他东西。

*   addapi explorer——用于公开关于 MVC 应用程序的信息。例如，这对于创建 Swagger 文档端点很有用。更多信息请查看安德鲁·洛克的帖子。我们最终会使用 Swagger，但是因为我们还没有使用它...说出来吧！
*   add authorization——添加必要的授权服务，我们将来肯定会需要，但不是现在，所以...出去！
*   AddDefaultFrameworkParts——查看`AddMvc`下面的`AddDefaultFrameworkParts`实现，我们可以看到它正在从 Razor 和 TagHelpers 程序集注册服务，所以我们也可以绕过它。
*   AddFormatterMappings——我不得不看一下这个函数的实现来弄清楚它做了什么，它基本上注册了一个`FormatFilter`,检查请求的路由数据和查询字符串是否存在格式参数，就像使用`Accept`头一样使用。另一个我们不需要的，所以跳过它。
*   添加视图-视图...下一个！
*   更多剃刀的东西，也是下一个！
*   添加剃刀页-和更多剃刀...让我们继续跳绳吧。
*   AddCacheTagHelper -更多的标记助手，跳过它。
*   adddata annotations——到目前为止我们没有使用数据注释，我也不希望将来在这个 API 中使用它们(我们将使用 [Fluent 验证](https://github.com/JeremySkinner/FluentValidation))，所以我们也可以安全地忽略它。
*   AddJsonFormatters——现在我们需要这个，因为我们的 API 需要处理 JSON。
*   add CORS——我不期望我们将从不同的域访问 API，所以我们也可以安全地忽略这个。

那么，我们最终会得到什么呢？在`Startup`类中，`services.AddMvc();`被替换为`services.AddRequiredMvcComponents();`，这是我们添加到已经创建的`ServiceCollectionExtensions`类中的一个新的扩展方法。在这个文件中，我们添加了以下内容:

`ServiceCollectionExtensions.cs`

```
public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddRequiredMvcComponents(this IServiceCollection services)
    {
        var mvcBuilder = services.AddMvcCore();
        mvcBuilder.AddJsonFormatters();
        return services;
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

考虑到我们所拥有的，我们只把`AddMvc`换成了`AddMvcCore`，还增加了一个对`AddJsonFormatters`的呼叫。我们稍后将回到这个方法，因为我们将需要一些额外的配置来进行我们将要进行的更改。

### 继承自 ControllerBase 而非 Controller

到目前为止，我们的`GroupsController`类继承自`Controller`。它仍然可以从`Controller`继承，但是由于`Controller`唯一拥有的`ControllerBase`没有的东西(顺便说一下，它继承了这个东西)是对视图的支持，我们可以跳过不必要的额外部分。

因为这个改变，因为我们仍然调用`View`方法，我们将得到编译错误。我们马上开始。

## 调整端点

现在让我们将`GroupsController`重新加工成一个 API 控制器。总之，它将以签名与`IGroupsService`几乎相同的方法结束，调用服务的方法并添加一些额外的“控制器东西”到混合中，即路由、HTTP 方法和 HTTP 响应。

`GroupsController.cs`

```
[Route("groups")]
public class GroupsController : ControllerBase
{
    private readonly IGroupsService _groupsService;

    public GroupsController(IGroupsService groupsService)
    {
        _groupsService = groupsService;
    }

    [HttpGet]
    [Route("")]
    public async Task<IActionResult> GetAllAsync(CancellationToken ct)
    {
        var result = await _groupsService.GetAllAsync(ct);
        return Ok(result.ToModel());
    }

    [HttpGet]
    [Route("{id}")]
    public async Task<IActionResult> GetByIdAsync(long id, CancellationToken ct)
    {
        var group = await _groupsService.GetByIdAsync(id, ct);

        if (group == null)
        {
            return NotFound();
        }

        return Ok(group.ToModel());
    }

    [HttpPut]
    [Route("{id}")]
    public async Task<IActionResult> UpdateAsync(long id, GroupModel model, CancellationToken ct)
    {
        model.Id = id; //not needed when we move to MediatR
        var group = await _groupsService.UpdateAsync(model.ToServiceModel(), ct);

        return Ok(group.ToModel());
    }

    [HttpPut]
    [HttpPost]
    [Route("")]
    public async Task<IActionResult> AddAsync(GroupModel model, CancellationToken ct)
    {
        model.Id = 0; //not needed when we move to MediatR
        var group = await _groupsService.AddAsync(model.ToServiceModel(), ct);

        return CreatedAtAction(nameof(GetByIdAsync), new { id = group.Id }, group.ToModel());
    }

    [HttpDelete]
    [Route("{id}")]
    public async Task<IActionResult> RemoveAsync(long id, CancellationToken ct)
    {
        await _groupsService.RemoveAsync(id, ct);

        return NoContent();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我认为理解这里发生的事情真的很简单，但我会尝试指出一些小细节。

*   读操作，包括它们的路径，基本上保持不变，只是方法名有所调整，并直接返回模型而不是视图(JSON 或任何其他格式的序列化由框架处理)。

*   更新操作被绑定到 HTTP PUT，这在 HTTP APIs 中很常见，因为我们期望完全替换存储的实体。

*   add 操作绑定到 HTTP POST 和 PUT 方法，与 update 操作不同，它不需要 id。这当然依赖于这个自动生成 id 的实现。其他实现可能允许客户端提供一个 id，在这种情况下，我们可能会用一个动作替换这两个动作，如果实体不存在，则添加，否则更新。

*   add 动作返回一个 HTTP 201 创建的状态代码，包括 url 的`Location`头以获取添加的实体，还包括在响应体中。

在本节的最后一点，请注意在 add 和 update 方法中，我覆盖了来自客户端的 id。我不需要这样做，但是我要确保服务不会得到不一致/意外的 id——在 add 上定义一个还没有 id 的 id(至少按照我们现在的方式，允许定义 id 也可能是有效的)和一个不同于在更新时放在 route 上的 id。

这个问题主要源于我们在所有操作中重用同一个模型。最好的方法是对每一个都使用特定的模型，这将在我们将[mediator](https://github.com/jbogard/MediatR)添加到项目中时实现。

## 使用 ApiController 属性

API 基本上已经准备好了，但是如果我们现在尝试运行它，读操作会像预期的那样工作，但是写操作不会。

让我们通过向`http://localhost:5000/groups`发送信息`{ "name": "Test Group" }`来尝试添加一个新组。我们得到的回应如下:

```
{  "id":  1,  "name":  null,  "rowVersion":  "611"  } 
```

Enter fullscreen mode Exit fullscreen mode

`id`和`rowVersion`正确，但`name`不正确。前者很好，因为它们都是在服务器端生成的，但名字并不是 get 到的 action 方法。如果我们在`AddAsync`方法中放一个断点，我们会看到`model`参数是空的。`UpdateAsync`也是如此。

问题是动作不知道`model`应该从主体反序列化。我们可以通过在`model`参数中添加`[FromBody]`属性来轻松解决这个问题。

这是可以接受的，但我们可以做得更好。在 ASP.NET 核心 2.1 中引入了`ApiController`，允许我们修饰控制器，使它遵循一些节省我们工作的惯例，比如推断复杂的对象来自请求体。这当然可以被覆盖，但是这是一个很好的缺省值，可以避免我们进行一些输入。你可以在这里阅读更多关于这个属性[的内容。](https://docs.microsoft.com/en-us/aspnet/core/web-api/?view=aspnetcore-2.2#annotation-with-apicontroller-attribute)

要使用该属性，我们必须转到依赖注入 MVC 配置并添加以下内容:

`ServiceCollectionExtensions.cs`

```
public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddRequiredMvcComponents(this IServiceCollection services)
    {
        var mvcBuilder = services.AddMvcCore();
        mvcBuilder.SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        // ...
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

因为使用该属性需要使用 2.1 版及更高版本的新 ASP.NET 核心 MVC 位，与 2.0 相比，这可能会导致重大变化，所以我们必须明确地告诉它我们想要使用新特性。点击阅读更多关于兼容性版本[的信息。](https://docs.microsoft.com/en-us/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2)

我使用兼容性版本 2.2，因为在这个版本中，我们可以使用`ApiController`属性作为自动应用于所有控制器的集合属性，而不必修饰每个控制器。

在`Startup`类文件中，我刚刚添加了属性为`[assembly: ApiController]`，现在我们有了一个正常工作的控制器🙂

## 创建例外过滤器

基于我们在过去几集中学到的东西，我们可以创造一个`ExceptionFilter`。在这种情况下，我们将创建一个过滤器，当对一个组的更新由于过时而失败时，它会返回一个 HTTP 409 冲突状态代码——这是一个乐观并发异常，就像我们在上一集看到的那样。我们可以在以后改进过滤器来处理更多种类的错误。

`ApiExceptionFilter.cs`

```
public class ApiExceptionFilter : IExceptionFilter
{
    public void OnException(ExceptionContext context)
    {
        if (context.Exception is DbUpdateConcurrencyException)
        {
            context.Result = 
                new ConflictObjectResult(
                    new
                    {
                        Message = "The updated entity has changed, please refresh your current copy."
                    });
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在未来改进这一点，让业务层将到达 API 的异常抽象为更通用的并发异常，而不是绑定到实体框架特定的异常。目前，这已经足够好了。

现在我们回到我们的`ServiceCollectionExtensions`类，通过添加这个过滤器来总结对 MVC 配置的更改(今天)。

```
public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddRequiredMvcComponents(this IServiceCollection services)
    {
        services.AddTransient<ApiExceptionFilter>();

        var mvcBuilder = services.AddMvcCore(options =>
        {
            options.Filters.AddService<ApiExceptionFilter>();
        });
        mvcBuilder.SetCompatibilityVersion(CompatibilityVersion.Version_2_2);   
        mvcBuilder.AddJsonFormatters();
        return services;
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

这就是这篇文章的全部内容。我们将我们的 MVC 应用程序移植到了一个 Web API 上——不管怎样，它是建立在 MVC 之上的，但是你明白了！

在未来的几集里，我们将在这个 API 的基础上构建，它仍然非常简单，但是到目前为止为我们提供了很好的服务，我们能够探索许多 ASP.NET 的核心构建块和特性。

不过在下一集，我们将从 ASP.NET 核心中休息一下，开始用 Vue.js 创建一个单页面应用程序，这将是我们的 PlayBall 项目前端，并将使用这个 API 来实现它的功能。

帖子中的链接:

*   Andrew Lock[对 ASP.NET 核心 ApiExplorer 的介绍](https://andrewlock.net/introduction-to-the-apiexplorer-in-asp-net-core/)
*   [使用 ASP.NET 核心构建 web APIs】](https://docs.microsoft.com/en-us/aspnet/core/web-api/?view=aspnetcore-2.2)
*   [ASP.NET 核心 MVC 的兼容版本](https://docs.microsoft.com/en-us/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2)

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode012)。

如果你想看到从之前的代码到这一集的变化，不要忘记你可以看看 GitHub 中的提交和拉请求，例如，你可以在这里看到这一集的 PR[。](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/pull/5)

请随意提问，不要犹豫提供反馈。

谢谢你的来访，西阿兹！