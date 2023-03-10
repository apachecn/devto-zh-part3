# 在 ASP.NET MVC 中使用 IoC

> 原文：<https://dev.to/barranger/using-ioc-in-asp-net-mvc-5995>

## 为什么要用 IoC

使用 IoC 的原因有很多，但特别是对于 ASP.NET，它允许您将代码隔离到功能组件中，并允许您将这些功能组件迁移到更新/更合适的技术中。

IoC 的另一个主要用途是测试。在您将使用完整的数据库存储库进行生产的地方，使用静态数据对于单元测试来说通常已经足够了。

## 哪个 IoC 容器

因为你目前使用的是 ASP.Net MVC，目前最好的选择是 Unity(不，不是游戏引擎:)。不是那个。NET Core 有它自己的 IoC 工具，但是如果不是全部的话，这里的大多数选项到时候都会很容易被移植。

## 样本项目

为了说明本文档中的概念，我创建了世界上最简单的项目来浏览这些示例，该项目可以在[这里](https://github.com/barranger/IoCTutorial)找到。

示例项目将简单地显示从报价服务收到的报价，该服务实现了`IQuoteService.cs`接口

## 注册服务

一旦通过 NuGet 添加了`Unity.MVC`库，你会在你的`App_Start`文件夹中找到`UnityConfig.cs`和`UnityMvcActivator.cs`。`UnityConfig.cs`文件是你注册服务的地方，比如:

```
public static void RegisterTypes(IUnityContainer container)
{
    // TODO: Register your type's mappings here.
    container.RegisterType<IQuoteService, StaticQuoteService>();
} 
```

其中`IQuoteService`是我们正在注册的接口，`StaticQuoteService`是我们将向系统公开的接口的具体实现。

## 使用服务

一旦服务设置好了，我们就要使用它们，在控制器的情况下，这是非常容易的，因为如果我们将接口声明为构造函数的参数，Unity 会自动将它们添加到我们的应用程序中，就像这样:

```
private IQuoteService quoteService;

public HomeController(IQuoteService quoteService)
{
    this.quoteService = quoteService;
} 
```

然后，我们可以在控制器中的任何地方使用该服务，就像示例对`About`路线:
所做的那样

```
public ActionResult About()
{
    ViewBag.Message = this.quoteService.GetRandomQuote();
    return View();
} 
```

有时你需要访问控制器之外的服务，这也可以通过使用`UnityConfig.Container`来实现，比如:

```
public static string GetRandomQuote()
 {
    var service = UnityConfig.Container.Resolve(typeof(IQuoteService), null) as IQuoteService;

    return $"From the RandomUtil: {service.GetRandomQuote()}";
 } 
```