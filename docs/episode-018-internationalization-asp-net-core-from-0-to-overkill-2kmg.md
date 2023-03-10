# 第 018 集-国际化-ASP.NET 核心:从 0 到矫枉过正

> 原文：<https://dev.to/joaofbantunes/episode-018-internationalization-asp-net-core-from-0-to-overkill-2kmg>

在这一集里，我们将使用 ASP.NET 核心国际化支持来使我们的认证服务支持多种语言。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/gov2ZVUSrYs](https://www.youtube.com/embed/gov2ZVUSrYs)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

既然我们在玩 [Razor Pages](https://docs.microsoft.com/en-us/aspnet/core/razor-pages/?view=aspnetcore-2.2) ，这是一个探索如何准备应用程序以支持多种语言的好机会。这也适用于普通的 [MVC](https://docs.microsoft.com/en-us/aspnet/core/mvc/overview?view=aspnetcore-2.2) 。

**快速提示:**当我在帖子中使用 i18n 时，它意味着国际化。这是因为它以`i`开头，以`n`结尾，中间有 18 个字符。不是我的主意！😛

## 为 i18n 配置服务和中间件

我们需要做的第一件事是配置服务和中间件来处理国际化。

### 配置服务

让我们从服务开始。在`Startup.ConfigureServices`中，我们将添加 3 样东西:

1.  添加本地化服务
2.  告诉 MVC 我们想要使用本地化的某些方面(它也代表 Razor Pages，因为它与 MVC 相耦合)
3.  为我们将使用的语言/文化添加配置

`Startup.cs`

```
public class Startup
{
    // ...
    public void ConfigureServices(IServiceCollection services)
    {
        // (1)
        services.AddLocalization(options => options.ResourcesPath = "Resources");

        services
            .AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
            .AddRazorPagesOptions(options =>
            {
                options.Conventions.AuthorizeFolder("/Account");
            }) 
            // (2)
            .AddViewLocalization(LanguageViewLocationExpanderFormat.Suffix)
            .AddDataAnnotationsLocalization();

        // (3)
        services.Configure<RequestLocalizationOptions>(options =>
        {
            var cultures = new[]
            {
                new CultureInfo("en"),
                new CultureInfo("pt")
            };
            options.DefaultRequestCulture = new RequestCulture("en");
            options.SupportedCultures = cultures;
            options.SupportedUICultures = cultures;
        });
        // ...
    }    
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在`(1)`中，我们通过调用扩展方法来注册所需的服务。在该方法的选项中，我们指出了我们的资源文件的位置，我们将把它放在项目根目录下的`Resources`文件夹中。

对于`(2)`，我们为 MVC 提供了一些关于本地化的额外信息。`AddViewLocalization`配置在视图中使用本地化的需求(例如，能够使用`IViewLocalizer`,我们稍后会看到),而`AddDataAnnotationsLocalization`有相同的目标，但是是关于我们的视图模型上的数据注释。

在`(3)`中，我们向配置中添加了一个`RequestLocalizationOptions`实例。这个对象由我们稍后将要注册的中间件使用，所以我们可以将它传递到那里，但是因为我们需要一个可用文化的列表来呈现给用户，所以我们可以将它注册为一个配置，并将其用作这些文化的源。

### 配置中间件

在中间件方面，我们只需要注册一个新的，负责根据它的一些属性设置请求的文化。

`Startup.cs`

```
public class Startup
{
    // ...
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // middlewares that don't depend on localization can come before it...
        app.UseRequestLocalization(
            app.ApplicationServices.GetRequiredService<IOptions<RequestLocalizationOptions>>().Value);
        // middlewares that depend on localization must come afterwards...
    }    
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，中间件按以下顺序检查查询字符串、cookies 和接受语言头中的区域性。这个顺序可以改变，我们甚至可以添加定制的提供者来获取请求的文化。开箱即用的(与中间件默认使用的相对应)是`QueryStringRequestCultureProvider`、`CookieRequestCultureProvider`和`AcceptLanguageHeaderRequestCultureProvider`。

## 使用资源文件

### resx 文件

为了存储我们翻译的字符串，我们将使用资源文件(`*.resx`)，因为它是开箱即用的。资源文件是`XML`，它保存了与一个键相关的字符串，这样我们就可以获取它们。

下面是一个资源文件中条目的例子:

```
<!-- ... -->    
<data name="ForgotPassword" xml:space="preserve">
    <value>Forgot your password?</value>
</data>
<!-- ... --> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以创建定制的资源提供者，也许将来我们应该看看这个，用`JSON`创建一些更简单的东西。并不是说我对`XML`有问题，但是这些资源文件有点复杂，在 Visual Studio 之外使用很痛苦(例如在 Rider 或 VS 代码中),那里有一个专用于`resx`文件的编辑器。

#### resx 文件名约定

这不是强制性的，但通常我们会将一个资源文件与一个特定的视图/控制器/页面等相关联，以保持事物的有序性并避免大量的资源文件，但我们稍后会看到这一点。除此之外，文件名的一部分应该指示资源所代表的文化。

如果我们不指定区域性，资源文件将被视为默认文件，在受支持的区域性与特定文件不匹配时使用。如果我们想要指定文化，我们可以做类似于`SomeResource.pt-PT.resx`或`SomeResource.pt.resx`的事情。第一个文件将专门匹配来自葡萄牙的葡萄牙语请求，而第二个文件更通用，因此普通葡萄牙语和巴西葡萄牙语(pt-BR)都将使用该文件。

### 查看具体资源

如前所述，常见的方法是使用多个资源文件，通常将它们与特定的视图/页面/控制器等相关联。为了建立关联，我们使用命名约定。

让我们从创建添加 i18n 支持到我们的登录页面开始，从视图开始。在项目的根目录中，我们应该有一个名为`Resources`的文件夹，正如在调整`ConfigureServices`方法时提到的。在这个文件夹中，我们将复制页面的文件夹结构，所以我们添加了另一个名为`Pages`的文件夹。在这里，我们可以创建两个资源文件，以支持我们配置的文化，因此`Login.en.resx`和`Login.pt.resx`，匹配视图的名称，即`Login.cshtml`。

在 login 视图中，我们有一个可以提取到资源文件中的`Forgot your password?`字符串。在资源文件中，我们为英语资源添加了一个带有关键字`ForgotPassword`和文本`Forgot your password?`的新条目，为葡萄牙语资源添加了`Esqueceu a palavra passe?`。

**注意:**
另一种方法是将英语文本保留在页面中(因为它是默认的区域性)，并使用默认区域性的文本作为键，只为替代区域性创建资源文件。我不喜欢这种方法，因为如果我们想要调整文本，那么我们需要调整所有资源文件中的键(或者更糟的是，我们忘记了必须这样做)。使用专用钥匙，那就不是问题了。

现在我们需要让视图使用这个新资源。在`Login.cshtml`的顶部，我们添加了一个新行“注入”一个我们可以用来获取本地化字符串的`IViewLocalizer`。

`Login.cshtml`

```
<!-- ... -->
@inject Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer Localizer
<!-- ... --> 
```

Enter fullscreen mode Exit fullscreen mode

文本所在的地方，我们用`@Localizer["ForgotPassword"]`代替。现在，如果我们发出请求，我们将获得英文文本(除非您将接受语言设置为葡萄牙语)。为了看到葡萄牙语文本显示出来，我们可以将`?culture=pt`添加到查询字符串中(我们稍后会考虑允许用户更改文化)。).

### 页面模型具体资源

特定于页面模型的资源与视图资源有许多共同之处。在`Resources/Pages`中，我们创建了两个新的资源文件，命名为`LoginModel.en.resx`和`LoginModel.pt.resx`，它们与我们的页面模型类的名称`LoginModel`相匹配。现在，在这些新文件中，我们可以添加一个条目，关键字`InvalidLoginAttempt`和值`Invalid login attempt.`表示英语，`Tentativa de login inválida.`表示葡萄牙语。

为了利用这一点，我们在`LoginModel`构造函数中添加了一个新的`IStringLocalizer<LoginModel>`参数。这个注入的参数将与创建的资源文件相关联，所以我们可以用它来获取我们的字符串。

现在我们使用了`Invalid login attempt.`字符串，我们可以用注入的 localizer 替换它。

`Login.cshtml.cs`

```
public class LoginModel : PageModel
{
    // ...
    public LoginModel(
        // ...
        IStringLocalizer<LoginModel> localizer,
        //...
        )
    {
        // ...
        _localizer = localizer;
    }

    // ...

    public async Task<IActionResult> OnPostAsync(string returnUrl = null)
    {
        returnUrl = returnUrl ?? Url.Content("~/");

        if (ModelState.IsValid)
        {
            //...
            else
            {
                ModelState.AddModelError(string.Empty, _localizer["InvalidLoginAttempt"]);
                return Page();
            }
        }
        return Page();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 页面模型内部类具体资源(带数据标注)

正如您现在可能已经开始看到的，有一种模式可以让资源文件和类/视图匹配起来。对于页面模型的内部类来说，这没有什么不同，但是关于命名，我花了一些时间才弄明白。我们很快就会到达那里，首先让我们看看这个类，我们为页面创建的那些`InputModel`之一，在这个例子中是为`LoginModel`创建的。

`Login.cshtml.cs`

```
public class LoginModel : PageModel
{
    // ...
    public class InputModel
    {
        [Required]
        [EmailAddress]
        public string Email { get; set; }

        [Required]
        [DataType(DataType.Password)]
        public string Password { get; set; }

        [Display(Name = "RememberMe")]
        public bool RememberMe { get; set; }
    }
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

该类唯一的变化是文本原来是`Remember me?`，现在是`RememberMe`，所以它是资源文件的更好的键。

我们现在需要做的就是像在其他情况下一样创建资源文件，这样当页面被呈现时就使用了正确的字符串。问题是，文件名应该是什么？在搜索网页和大量的试验和错误之后，终于想出这些文件应该被命名为`LoginModel+InputModel.en.resx`和`LoginModel+InputModel.pt.resx`。

**旁注:**在这次搜索中甚至碰到了类似的未回答的[栈溢出问题](https://stackoverflow.com/questions/51235798/asp-net-core-razor-pages-localization-for-inputmodel-inside-pagemodel)(正如 [xkcd](https://xkcd.com/979/) 所预言的)并且能够帮忙解决(即使 6 个月以后😛).

有了这些宝贵的信息，我们就可以创建所需的资源文件，并添加我们想要的英语和葡萄牙语文本。

### 共享资源

除了将资源文件与特定的视图/页面/控制器等相关联之外，可能还会有这样的情况，我们只是想要一些在多个地方使用的公共字符串。这种设置有点奇怪，但并不难操作。

在`Resources`文件夹根目录中，我们创建了一个名为`SharedResource`的新类。该类将保持为空，它只是要被使用，这样我们就有一种方法来引用资源文件，假设它们不与特定的项相关联。

在新类文件的旁边，我们可以创建资源文件，命名为`SharedResource.en.resx`和`SharedResource.pt.resx`。如果您正在使用 Visual Studio，您会注意到它将文件分组，就像它是解决方案资源管理器中的一个文件一样，并且您可以通过单击小箭头来展开(例如，与`cshtml`和`cshtml.cs`文件相同)。

现在我们有很多方法来使用共享资源。对于我在这个应用程序中做的简单测试，我只是用它来记录`LoginModel`类的`OnGet`方法中的一些内容。为了访问资源，我们在构造函数中添加了一个`IStringLocalizer<SharedResource>`参数，所以它是由框架注入的。使用它和前面的例子一样，所以`_sharedLocalizer["SampleSharedString"]`得到我们添加到资源文件中的字符串。

`Login.cshtml.cs`

```
public class LoginModel : PageModel
{
    // ...
    public LoginModel(
        // ...
        IStringLocalizer<SharedResource> sharedLocalizer)
    {
        // ...
        _sharedLocalizer = sharedLocalizer;
    }

    // ...

    public void OnGet(string returnUrl = null)
    {
        _logger.LogDebug(_sharedLocalizer["SampleSharedString"]);
        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然我没有在这个应用程序中使用它，但是快速浏览一下[文档](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization?view=aspnetcore-2.2)可以看到其他的可能性，比如在视图和数据注释中使用它。

#### 视图

在视图的例子中，我们通过像`@inject IHtmlLocalizer<SharedResource> SharedLocalizer`一样将共享定位器添加到页面的顶部，然后像前面显示的那样使用它。

#### 数据标注

对于数据注释来说，这需要更多的工作。正如我们之前所看到的，我们拥有自动与资源相关联的注释(只要名称正确)。为了使用共享资源，我们需要覆盖资源与注释的关联方式。

在`Startup`类中，当我们调用`AddDataAnnotationsLocalization`时，我们需要做额外的配置(从[文档](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization?view=aspnetcore-2.2#using-one-resource-string-for-multiple-classes) ):

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
} 
```

Enter fullscreen mode Exit fullscreen mode

通过这样做，我们覆盖了数据注释和资源配对的方式，在这种情况下，总是使用共享资源，但是如果我们愿意，也可以使用其他逻辑。不过，似乎不可能将这种方法和前面的方法混合在同一个类中。

## 商店文化偏好

现在我们已经对 i18n 进行了大部分配置，使用了资源文件并以不同的方式使用了它们，让我们来看看允许用户选择所需的文化。

我们将为用户创建一个选择框来选择文化，将选择发送到一个控制器，该控制器将它存储在一个 cookie 中，然后由我们前面谈到的`CookieRequestCultureProvider`在每个请求中使用，以设置我们应该使用的文化来呈现我们的响应。像往常一样，有很多方法可以达到相同的结果，这只是一个简单的可能性(也许如果 SEO 是一个问题，在路线上有文化是一个更好的主意？).

### 控制器

让我们首先创建一个名为`CultureController`的新控制器。它将有一个单一的动作，该动作将接收用户选择的文化(以及设置首选项后要返回的 url)。

`CultureController.cs`

```
public class CultureController : Controller
{
    [HttpPost]
    public IActionResult SelectCulture(string culture, string returnUrl)
    {
        Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(new RequestCulture(culture)),
            new CookieOptions { Expires = DateTimeOffset.UtcNow.AddYears(1) }
        );

        return LocalRedirect(returnUrl);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

控制器中没有什么特别的东西，我们只是用文化偏好将一个 cookie 添加到响应中。我们使用`CookieRequestCultureProvider`来获取提供者在解析请求时将寻找的 cookie 名称，并以提供者期望读取的格式创建 cookie 值。最后一个参数简单地将 cookie 的持续时间设置为一年。

### 局部视图

为了给用户提供选择文化的可能性，我们将创建一个带有选择框的局部视图。

`_SelectCulturePartial.cshtml`

```
@using Microsoft.AspNetCore.Builder
@using Microsoft.AspNetCore.Localization
@using Microsoft.AspNetCore.Mvc.Localization
@using Microsoft.Extensions.Options

@inject IViewLocalizer Localizer
@inject IOptions<RequestLocalizationOptions> LocalizationOptions

@{
    var requestCulture = Context.Features.Get<IRequestCultureFeature>();
    var cultureItems = LocalizationOptions.Value.SupportedUICultures
        .Select(c => new SelectListItem { Value = c.Name, Text = Localizer.GetString(c.Name) })
        .ToList();
    var returnUrl = string.IsNullOrEmpty(Context.Request.Path) ? "~/" : $"~{Context.Request.Path.Value}{Context.Request.QueryString}";
}
<div >
    <form id="selectLanguage" 
          asp-controller="Culture" 
          asp-action="SelectCulture" 
          asp-route-returnUrl="@returnUrl"
          method="post" 
          class="form-horizontal" 
          role="form">
        <select name="culture" 
                onchange="this.form.submit();" 
                asp-for="@requestCulture.RequestCulture.UICulture.Name" 
                asp-items="cultureItems"></select>
    </form>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

虽然不复杂，但这局部中有一堆东西我们可以仔细看看。

首先，我们注入我们在`Startup.ConfigureServices`中讨论过的`IOptions<RequestLocalizationOptions>`,以获得显示给用户的文化。

有了支持的文化，我们可以创建一个`SelectListItem`列表，并使用`asp-items`属性将它作为内容传递给选择框标签帮助器。选择项目文本被本地化，从`Resources/Pages/Shared/_SelectCulturePartial.en.resx`(和它的`pt`)获得信息。

剩下的是一个典型的表单，当选择的值改变时提交文化。

最后，我们转到`_Layout.cshtml`文件，通过在那里添加行`@await Html.PartialAsync("_SelectCulturePartial")`来使用局部视图。

## 其他

这就是我们对 ASP.NET 核心区国际化的快速了解。像往常一样，还有很多东西需要探索，成为 [docs](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization?view=aspnetcore-2.2) 是获取更多相关信息的好地方。

帖子中的链接:

*   [ASP.NET 核心区的全球化和本地化](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization?view=aspnetcore-2.2)
*   [ASP.NET 核心剃刀页](https://docs.microsoft.com/en-us/aspnet/core/razor-pages/?view=aspnetcore-2.2)
*   [ASP.NET 核心 MVC](https://docs.microsoft.com/en-us/aspnet/core/mvc/overview?view=aspnetcore-2.2)

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode018)。

感谢分享和反馈！

谢谢你的来访，西阿兹！