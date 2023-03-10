# Kentico 12:设计模式第 4 部分——向 CMS 添加依赖注入

> 原文：<https://dev.to/seangwright/kentico-12-design-patterns-part-4-adding-dependency-injection-to-the-cms-35a>

<figure>

[![](img/db59641fe8120a468df56a4d6c96f078.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hcr5xcZT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/liul85lqvqkm9bymb72m.jpg)

<figcaption>Photo by [Rich Tervet](https://unsplash.com/@richtervet) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

既然 Kentico 已经授权其开发人员使用 ASP.NET MVC 构建应用程序，我们就可以利用框架所鼓励的设计模式和最佳实践。👍

随着我们的库和代码库改变以利用这些模式和实践，我们能在基于 ASP.NET 网络表单的 CMS 应用程序中使用它们吗？如果是这样，我们如何实现这一点？🤔

对于依赖注入的模式，答案是肯定的！🙌

我们来看看吧！

## 图样

MVC 鼓励的模式之一是[控制反转](https://en.wikipedia.org/wiki/Inversion_of_control) (IoC)。这种模式可以通过[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection) (DI)来实现，既有框架类型，也有自定义的用户定义类型。

在应用程序框架中，完成 IoC 的方式通常是允许框架创建和调用由开发人员创建的特定入口点类型——从那里我们的代码执行，直到它返回。

IoC 允许我们以优雅的方式使用 DI，因为框架负责调用我们的代码(IoC)——因此它也可以负责创建我们的代码所依赖的东西(DI)。

### MVC 中的 DI

在 MVC 应用程序中，其中一个入口点类型是`System.Web.Mvc.Controller`类，所有的定制控制器都从该类继承而来。

这里有一个例子`UserController`有(2)个依赖关系，`IUserContext`和`IEmailService` :

```
public class UserController : Controller
{
    public UserController(IUserContext userContext, IEmailService emailService)
    {
        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当 MVC 决定 HTTP 请求应该由`UserController`处理时，它需要能够构造`UserController`的实例。MVC 基于 URL 或请求体模式做出这个决定，这些模式映射到我们的应用程序中的路由约定或配置。

> 要查看混合了约定和配置的路由配置模式，请阅读我在本系列中的上一篇文章。💪
> 
> [![seangwright](img/e9e13f3e057a4f58b01316c7a9d6eeea.png)](/seangwright) [## Kentico 12:设计模式第 3 部分-提示和技巧，应用程序结构
> 
> ### Sean g . Wright 6 月 1 日 197 分钟阅读
> 
> #mvc #designpatterns #kentico #aspnet](/seangwright/kentico-12-design-patterns-part-3-tips-and-tricks-application-structure-1l2o)

如果我们通过构造函数注入来使用 DI，MVC 是如何计算出如何创建我们的控制器类的依赖关系的？或者这些类的依赖关系？

这个递归问题由 IoC 容器解决。IoC 容器被配置为“知道”如何构造应用程序中使用的任何类型的被认为是依赖的实例。这种类型的配置通常被称为“注册”。

因此，在我们的应用程序中，当 MVC 需要创建我们的`UserController`的实例时，它会有效地尝试从 IoC 容器中获取一个实例。

IoC 容器计算出创建一个`UserController`所需的整个依赖链，创建一个，然后将它返回给框架代码。

> 当使用 IoC 和 DI 时，我们的代码不会创建自己的依赖项。
> 
> 相反，所有的依赖必须从更高的地方提供给我们的代码。这是对我们自己代码的依赖性的“控制”的“倒置”。😮

### DI 在 Web 表单中

ASP.NET Web 表单架构是基于`Page`和`UserControl`类型的。

> 当使用 Kentico 时，我们相应地使用`CMSPage`和`CMSAbstractWebPart`类。

这两种类型都需要公共的无参数构造函数。

如果我们将依赖项指定为构造函数参数会发生什么？Web 窗体框架将无法创建该类型的实例，请求将失败并出现异常！🤦🏽‍♀️

ASP.NET Web Forms 不是为 MVC 鼓励的模式和实践设计的，但这并不意味着我们陷入了僵局。

## 库- Autofac

建立了设计模式后，让我们深入了解如何配置它，不是针对 MVC，而是针对 ASP.NET Web Forms，这是 Kentico 内容管理应用程序所基于的框架。

我们将使用 [Autofac](https://autofac.org/) 作为我们的 IoC 容器选择。

> . NET 中有许多 IoC 容器库。Daniel Palme 的这个基准博客包含了一个非常完整的最著名的库的列表。
> 
> 在过去，我主要使用的是简单注入器，因为它有很好的性能、简单的 API 和容器验证特性。这是一个很棒的图书馆，[集装箱验证也很棒](https://simpleinjector.readthedocs.io/en/latest/using.html#verifying-the-container-s-configuration)。👌
> 
> 也就是说，AutoFac 提供了一些很好的 API 来简化一些问题。NET 应用程序框架需要开发人员面对。它也是 Kentico 在其示例 Dancing Goat 站点中使用的 IoC 容器。

我们将使用(2)个 NuGet 包(指定版本):

1.  这是主要的 IoC 容器库，与应用框架无关。

2.  [Autofac。这是用于 Autofac 的 ASP.NET 网络表单集成包。](https://www.nuget.org/packages/Autofac.Web/)

Web Forms 集成包允许我们以几种不同的方式执行 DI。

1.  属性注入——将 IoC 容器解析的实例分配给 Web 表单`Page`或`UserControl`实例的公共属性。✔

2.  属性特性注入-同上，但只有在特定的属性被应用到那些页面或控件时。✔

3.  空属性/属性注入——使用上面的任何一种方法，只有当属性在赋值时是`null`时，才赋值一个解析的实例。✔

我们将使用第二个选项，您可以在 [Autofac 的文档](https://autofac.readthedocs.io/en/latest/integration/webforms.html)中探索其他两个选项。

> **更新(2019/07/09)** :当使用上述第一个选项时，Kentico 在 CMS(报告模块)的某些方面存在问题。在很大程度上，它是有效的，但并不完全有效，这还不够好！因此，虽然我最初推荐第一个选项，但我现在推荐第二个。
> 
> 感谢 Luminary 的 [Tony 发现了这个问题！](https://www.luminary.com/tony)

继续将 NuGet 包安装到`CMSApp`项目中。

> 在中管理 NuGet 包。如果您使用 NET Framework 项目会容易得多。NET 功能和工具。查看我的帖子以了解更多信息。💪
> 
> [![seangwright](img/e9e13f3e057a4f58b01316c7a9d6eeea.png)](/seangwright) [## 肯蒂科 12 级图书馆与现代。网络核心功能
> 
> ### Sean g . Wright 6 月 1 日 1915 分钟阅读
> 
> #csharp #dotnet #dotnetcore #kentico](/seangwright/kentico-12-class-libraries-with-modern-net-core-features-34n5)

好了，现在让我们设置我们的应用程序！

> 英寸 NET 4.7.2 [一个基于构造器的 DI 解决方案已经被添加到 ASP.NET](https://docs.microsoft.com/en-us/dotnet/framework/whats-new/#aspnet)中用于 Web 表单，这个解决方案可以被用作一个钩子点，带有一个适配器层，来集成一个 IoC 容器。
> 
> 这里是一个集成了 SimpleInjector 的[例子。](https://gist.github.com/ndc/7334d459c3eb0be594aad3973a488cad)
> 
> 我将继续使用属性注入，因为它是 Autofac 支持的，无需定制。

## 实施——肯帝科 12 CMS

Autofac 文档对如何配置我们的应用程序开始使用这个库有一个很好的演示，我将总结下面的步骤。

### 配置项目

首先，将以下 ASP.NET 模块条目添加到`<configuration><system.webServer><modules>`节点下的应用程序`web.config`中。

```
<add
    name="ContainerDisposal"
    type="Autofac.Integration.Web.ContainerDisposalModule, Autofac.Integration.Web"
    preCondition="managedHandler"/>
<add
    name="PropertyInjection"
    type="Autofac.Integration.Web.Forms.AttributedInjectionModule, Autofac.Integration.Web"
    preCondition="managedHandler"/> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，更新我们在`Global.asax.cs`中的`Global`类来支持 Autofac。

```
// Add the IContainerProviderAccessor interface
public class Global : CMSHttpApplication, IContainerProviderAccessor
{
    static Global()
    {
        /// leave existing code
    }

    // Holds the application container
    private static IContainerProvider containerProvider;

    // Used by Autofac to resolve dependencies via IContainerProviderAccessor
    public IContainerProvider ContainerProvider => containerProvider;

    // Creates a container builder, builds it, and then sets the container
    protected void Application_Start(object sender, EventArgs e)
    {
        var builder = new ContainerBuilder();

        var container = builder.Build();

        containerProvider = new ContainerProvider(container);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在已经为我们的 CMS 应用程序设置了 DI！

...除了我们还没有注册任何类型，所以我们的 IoC 容器和它到 Web 表单的集成目前没有太大帮助。😑

所以，我们用 Autofac 注册一些类型吧！

### 登记我们的类型

我们想登记哪种类型？

为什么我们不使用本系列之前文章中的`ISiteContext`和`KenticoSiteContext`类型呢？😊

首先，创建`ISiteContext`接口:

```
public interface ISiteContext
{
    string SiteName { get; }
    int SiteId { get; }
    SiteInfo Site { get; }
} 
```

Enter fullscreen mode Exit fullscreen mode

而现在的`KenticoSiteContext`实现:

```
public class KenticoSiteContext : ISiteContext
{
    public string SiteName => SiteContext.CurrentSiteName;

    public int SiteId => SiteContext.CurrentSiteID;

    public SiteInfo Site => SiteContext.CurrentSite;
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然没有什么可以阻止我们将所有的 Autofac 类型注册添加到我们的`Global.asax.cs`文件中，但是我建议将类型注册移到一个不同的类中。

我通常创建一个`DependencyResolverConfig.cs`类(或类似的东西)。

在这个类中，我们可以注册我们的类型，然后构建容器:

```
public static class DependencyResolverConfig
{
    public static IContainer BuildContainer()
    {
        var builder = new ContainerBuilder();

        builder
            .RegisterSource<KenticoRegistrationSource>();

        builder
            .RegisterType<KenticoSiteContext>()
            .As<ISiteContext>();

        var container = builder.Build();

        return container;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 随着这个文件的注册和复杂性的增长，我总是将注册转移到单独的类中，每个类关注应用程序的特定部分(例如:应用程序、数据访问、授权)。
> 
> 你可以在中找到一个[的例子。](https://gist.github.com/seangwright/4918a5c5488a9bb617ebdf1c2557fe6b#file-dependencyresolverconfig-cs)

注意那行`builder.RegisterSource<KenticoRegistrationSource>();`。

这告诉 Autofac，当它不知道如何解析类型时，将对该类型的实例的请求转发给 Kentico。这可以为我们解决的一个示例类型是`CMS.Ecommerce.ICurrentShoppingCartService`。

> `KenticoRegistrationSource`的代码有点长，所以可以在这个里找到[。](https://gist.github.com/seangwright/4918a5c5488a9bb617ebdf1c2557fe6b#file-kenticoregistrationsource-cs)

现在我们需要更新`Global.asax.cs`文件中的`Global`类，以使用新的`DependencyResolverConfig`类。唯一需要做的更改是用下面的代码替换`Application_Start`方法。

```
protected void Application_Start(object sender, EventArgs e)
{
    var container = DependencyResolverConfig.BuildContainer();

    containerProvider = new ContainerProvider(container);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 解析页面中的类型

让我们创建一个 Web 表单类，并测试解析我们的类型。

在名称为`DIPage`的`CMSApp -> CMSPages`下创建一个新的 Web 表单页面。用下面的代码替换`DIPage.aspx.cs`的内容。

```
[InjectPropertiesAttribute]
public partial class DIPage : CMSPage
{
    public ISiteContext SiteContext { get; set; }
    public ICurrentShoppingCartService CurrentShoppingCartService { get; set; }

    protected void Page_Load(object sender, EventArgs e)
    {
        var service = CurrentShoppingCartService;

        var cart = service.GetCurrentShoppingCart(
            CurrentUser, 
            SiteContext.SiteName);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将在 Visual Studio 中在`Page_Load`方法的左括号上设置一个断点，并运行 CMS 项目进行调试。

当我们点击断点并单步执行代码时，我们将看到`SiteContext`和`CurrentShoppingCartService`属性被填充了我们期望的值。

相当不错！😃✨

### 解析页面或控件之外的类型

当我们想从 Kentico 应用程序的其他部分解析我们的类型时，比如一个调度任务，我们应该怎么做？

在这种情况下，我们不能使用 DI，因为 Kentico 创建了定制调度任务类的实例。与 Autofac Web Forms 集成包不同，我们无法告诉 Kentico 从 IoC 容器中解析构造函数参数或类属性值。🤔

> Kentico 乡亲:如果有一种方法来定制这些类型的创作，我很想知道！

我们可以做的是使用 Autofac 容器作为服务定位器。

> 通常情况下，服务位置[被认为是反模式](https://blog.ploeh.dk/2010/02/03/ServiceLocatorisanAnti-Pattern/)，但是当你只被提供柠檬时，你戴上手套、眼罩，然后开始榨汁！🥤

为了演示这一点，让我们创建一个定制的调度任务，返回当前请求用户的购物车 Id。

```
public class GetCurrentUserShoppingCartIdTask : ITask
{
    private readonly ISiteContext siteContext;
    private readonly ICurrentShoppingCartService currentShoppingCartService;

    public GetCurrentUserShoppingCartIdTask()
    {
        // Get the instance of our application
        var application = HttpContext
                .Current
                .ApplicationInstance as IContainerProviderAccessor;

        // Get the global container from the application
        var container = application
                .ContainerProvider
                .ApplicationContainer;

        // Resolve our dependencies
        siteContext = container.Resolve<ISiteContext>();
        currentShoppingCartService = container.Resolve<ICurrentShoppingCartService>();
    }

    public string Execute(TaskInfo task)
    {
        string siteName = siteContext.SiteName;

        var cart = currentShoppingCartService.GetCurrentShoppingCart(
            MembershipContext.AuthenticatedUser, 
            siteName);

        return cart.ShoppingCartID.ToString();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们对已经见过的两种类型(`ISiteContext`和`ICurrentShoppingCartService`)进行依赖，但是我们没有通过类构造函数或公共属性获得它们，而是直接从容器中解析它们。

> 上面的`IContainerProviderAccessor application`是从我们在`Global.asax.cs`中的`Global`类创建的应用程序的实例，而`ILifetimeScope container`是 Web Forms 使用自定义的`Page`和`UserControl`类型来获取属性注入实例的相同东西。

因此，我们在类的构造函数中解析依赖关系，然后在`Execute()`方法中使用它们。

该构造函数中有很多粘合代码。让我们把一些移到一个扩展方法中。

```
public static class TaskExtensions
{
    public static ILifetimeScope GetContainer(this ITask task, HttpContext context)
    {
        // Get the instance of our application
        var application = context
                .ApplicationInstance as IContainerProviderAccessor;

        // Get the global container from the application
        return application
                .ContainerProvider
                .ApplicationContainer;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的构造函数如下:

```
public GetCurrentUserShoppingCartIdTask()
{
    var container = this.GetContainer(HttpContext.Current);

    // Resolve our dependencies
    siteContext = container.Resolve<ISiteContext>();
    currentShoppingCartService = container.Resolve<ICurrentShoppingCartService>();
} 
```

Enter fullscreen mode Exit fullscreen mode

> 只能在`ITask`上调用`GetContainer()`扩展方法，这可以防止服务定位器模式泄漏到我们代码库的其余部分！👍

我最后的建议是，将`Execute()`方法的业务逻辑转移到另一个类中，该类遵循 MVC-DI 和[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)的模式和实践。

这个新类将把`ISiteContext`和`ICurrentShoppingCartService`作为构造函数依赖项，并有一个简单的 API(最多只有几个方法)来处理预定的任务。

然后，我们可以使用 IoC 容器来创建它的实例，而不是它的依赖项。然后我们在`ITask.Execute()`方法中调用它的方法。

## 总结一下！

我们回顾了什么是控制反转，它如何支持依赖注入，以及 ASP.NET MVC 如何使用这些概念来帮助开发人员创建应用程序。🧐

尽管 ASP.NET Web Forms 不支持这两个现成的概念，但是我们可以用 Autofac 来增强这个框架，以允许属性注入`Page`和`UserControl`类型。😎

我们还看了一个场景，在这个场景中，我们希望解决我们无法与 IoC 容器集成的 Kentico 类型中的注册依赖项。🤗

相反，我们说 [YOLO！并使用服务定位器模式来解析我们的类型。😜](https://en.wikipedia.org/wiki/YOLO_(aphorism))

我希望这个演练对你有所帮助，解释也很清楚！

这是我将博客从 [Medium](https://medium.com/@seangwright) 切换到 DEV 后的第一篇博文，我期待着在未来写更多。

关于 Kentico 设计模式的更多信息，请点击 DEV 上的[查看我的系列](https://dev.to/search?q=Kentico%2012%20-%20Design%20Patterns)或 Kentico 标签。

## # [肯蒂科](https://dev.to/t/kentico) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:5339,&quot;name&quot;:&quot;kentico&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: kentico" aria-pressed="false">跟随</button>