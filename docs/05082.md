# 第 019 集-角色、主张和政策-ASP.NET 核心:从 0 到过度杀戮

> 原文：<https://dev.to/joaofbantunes/episode-019-roles-claims-and-policies-asp-net-core-from-0-to-overkill-1hkh>

在这一集里，我们回到授权主题，在 ASP.NET 核心中玩一会儿角色、声明和策略，学习如何使用它们来限制对应用程序某些区域的访问。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/betgDb8AH8k](https://www.youtube.com/embed/betgDb8AH8k)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在前面的几集中，我们一直致力于身份验证服务，它将作为应用程序用户注册和登录的中心点。我们还利用使用 Razor Pages 的机会来看看如何在 ASP.NET 核心中实现国际化。

现在，让我们借此机会了解一下授权、如何在 ASP.NET 核心中实施授权及其一些概念(角色、声明和策略)。我计划在稍后阶段讨论这个主题，并将其应用于我们将要实现的 API，因为身份验证服务将只处理身份验证，但 YouTube 上的一位观众说，很高兴看到它如何与 Razor 页面一起工作，所以...我们到了🙂。

## 角色(混合了一些声明和策略)

在 ASP.NET，实现授权最广为人知的方式可能是通过使用角色。在 ASP.NET 核心中，引入了一种新的方法，即基于策略的授权，但是如果我们愿意，我们仍然可以选择使用基于角色的授权。

另一件要注意的事情是，我们可以以几种不同的方式使用角色——角色本身或者基于声明的角色。在这个简单的测试中，我使用了基于声明的角色，但是我也将在其他方法的方向上添加一些指针。

### 要求角色访问页面

要求一个角色访问一个页面的工作方式和以前一样，通过在我们想要强制一个特定角色的类上添加一个`Authorize`属性。

仅仅为了这个例子，我们将创建一个名为`Admin`的`Pages`的新的子文件夹。在这里我们创建一个新的 Razor 页面，名为`Index.cshtml`。在视图中，我们可以添加一些文本，只是为了更容易地看到我们所在的页面。相关部分在代码隐藏中:

`Pages/Admin/Index.cshtml.cs`

```
[Authorize(Roles = "admin")]
public class IndexModel : PageModel
{
    public void OnGet()
    {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了`Authorize`属性，我们只能在拥有`admin`角色的情况下访问页面，而我们现在还没有，所以导航到页面会被拒绝访问。

### 使用声明向用户添加角色

为了使这个例子非常简单，我们可以在注册时使用我前面提到的声明为用户添加角色。在`Register.cshtml.cs`中，我们可以进行以下更改:

`Pages/Register.cshtml.cs`

```
public class RegisterModel : PageModel
{
    // ...
    public async Task<IActionResult> OnPostAsync(CancellationToken ct, string returnUrl = null)
    {
        // ...
        // after creating a user successfully
        var addClaimResult = await _userManager.AddClaimAsync(user, new Claim(ClaimTypes.Role, "admin"));
        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了`UserManager.AddClaimAsync`，我们可以添加任何我们想要的声明，而没有任何依赖关系(稍后我们会看到，这不是独立角色的情况)，因此我们可以利用助手常量`ClaimTypes.Role`并将新注册的用户配置为拥有`admin`角色。

现在，如果我们创建一个新用户并再次尝试访问该页面，访问将不再被拒绝，我们可以看到该页面的内容。

### 使用没有权利要求的角色

要使用没有声明的角色，我们需要做一些改变。

关于在注册时将用户添加到角色，代码将非常类似:

```
var addClaimResult = await _userManager.AddClaimAsync(user, new Claim(ClaimTypes.Role, "admin"));
// becomes
var addToRoleResult = await _userManager.AddToRoleAsync(user, "admin"); 
```

Enter fullscreen mode Exit fullscreen mode

然而，如果我们只是试图运行，我们将得到一个错误:

```
InvalidOperationException: Role ADMIN does not exist.

Microsoft.AspNetCore.Identity.EntityFrameworkCore.UserStore<TUser, TRole, TContext, TKey, TUserClaim, TUserRole, TUserLogin, TUserToken, TRoleClaim>.AddToRoleAsync(TUser user, string normalizedRoleName, CancellationToken cancellationToken) 
```

Enter fullscreen mode Exit fullscreen mode

当使用声明时，我们可以随意添加它们，我们只需要传入一个名称，它们就被创建了。然而，如果我们想单独使用角色，我们需要确保它们存在。

为了配置角色，我们可以使用`RoleManager<TRole>`类。假设我们也想在注册过程中这样做——这不是一个好主意，但是为了简单起见，我们还是要这样做。

在`RegisterModel`构造函数中，我们添加了一个新的参数并存储起来以备将来使用:
`Pages/RegisterModel.cshtml.cs`

```
public class RegisterModel : PageModel
{
    private readonly RoleManager<IdentityRole> _roleManager;

    public RegisterModel(
        // ...
        RoleManager<IdentityRole> roleManager)
    {
        // ...
        _roleManager = roleManager;
    }
    // ... 
```

Enter fullscreen mode Exit fullscreen mode

然后，在创建用户时，我们可以检查角色是否存在，如果不存在，就创建我们想要的角色。

`Pages/Register.cshtml.cs`

```
// ...
public async Task<IActionResult> OnPostAsync(CancellationToken ct, string returnUrl = null)
{
    // ...
    // after creating a user successfully
    if(!(await _roleManager.RoleExistsAsync("admin")))
    {
        await _roleManager.CreateAsync(new IdentityRole("admin"));
    }

    var addClaimResult = await _userManager.AddToRoleAsync(user, "admin");
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:同样，这种角色管理的东西不应该出现在注册过程的中间，而应该出现在应用程序启动时运行的过程中，或者甚至出现在应用程序中允许这种配置的单独区域。

现在，如果我们尝试注册一个新用户，它将会成功，角色将会赋予新用户，因此我们可以像以前一样访问管理页面。

## 政策

正如我提到的，在 ASP.NET 核心中，我们有基于策略的授权的概念。使用它，我们可以非常灵活地要求用户访问特定页面、应用程序区域甚至整个应用程序。让我们看一些使用策略的例子。

### 声明一个需要的策略

让我们从几种方法开始，说明访问应用程序的特定页面或区域需要什么策略。之后再看如何定义政策。

#### 使用授权属性

为了指示访问页面所需的策略，我们可以像对角色那样使用`Authorize`属性，只是指定一个策略。为了看到这一点，我们可以在`Pages/Admin`文件夹中创建一个名为`AttributePolicyProtected.cshtml`的新 Razor 页面。就像前面的例子一样，我们在视图中放置一些文本，只是为了通知我们所在的页面。相关部分在`AttributePolicyProtected.cshtml.cs`文件中，我们有:

`Pages/Admin/AttributePolicyProtected.cshtml.cs`

```
[Authorize(Policy = "SamplePolicy")]
public class AttributePolicyProtectedModel : PageModel
{
    public void OnGet()
    {

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将在后面看到如何定义策略，但是相关的信息是，它像以前一样强制用户拥有`admin`角色，因此可以访问前面的示例页面的用户也可以访问这个页面，只是以不同的方式声明。

#### 使用剃刀页约定俗成

除了用属性设置必需的策略之外，我们还可以用 Razor Pages 约定(或 MVC 过滤器，但在本文中我们将只使用 Razor Pages)来实现。

我们之前已经使用过 Razor Pages 约定，将应用程序的`Account`区域设置为要求对用户进行身份验证。对于同一个`AuthorizeFolder`方法，我们也可以传入一个策略名。还有其他类似的方法，这就是我们将要使用的，在这种情况下`AuthorizePage`。

`Startup.cs`

```
// ...
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services
        .AddMvc()
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.AuthorizeFolder("/Account");
            options.Conventions.AuthorizePage("/Admin/ConventionPolicyProtected", "AnotherSamplePolicy");
        })
        // ... 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，我们将名为`ConventionPolicyProtected`的页面设置为需要名为`AnotherSamplePolicy`的策略(在这个例子中，假设它与`SamplePolicy`做同样的事情)。如果我们创建了新页面`Pages/Admin/ConventionPolicyProtected.cshtml`，即使没有`Authorize`属性，我们也可以看到它强制执行了所需的策略，正如在 Razor Pages 约定中定义的那样。

### 定义一个策略

既然我们已经看到了使用策略的一些方法，那么是时候看看定义它们的几种方法了。在这种情况下，我们将看一看简单地要求一个声明并使用一个定制的`AuthorizationHandler`。还有更多，但我们只是在探索，当我们真正需要的时候，更容易找到更多的东西🙂。

#### 使用 RequireClaim

配置策略的一个非常简单的方法是使用我们在配置授权服务时获得的`AuthorizationPolicyBuilder`。我们将看一下`RequireClaim`，但是我们还可以探索更多的方法，比如`RequireRole`、`RequireAssertion`、`RequireUserName`等等。

让我们回到`Startup`类的`ConfigureServices`方法。在这里，我们将通过添加对`AddAuthorization`的调用来添加授权服务的配置。

`Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddAuthorization(options =>
    {
        options.AddPolicy("SamplePolicy", policy => policy.RequireClaim(ClaimTypes.Role, "admin"));
        // ...
    });
    // ... 
```

Enter fullscreen mode Exit fullscreen mode

在`options`对象上，这是一个类型为`AuthorizationOptions`的实例，我们可以做一些事情，但是我们将把重点放在`AddPolicy`上。`AddPolicy`方法允许我们配置新的策略，这样我们就可以像以前一样使用。在上面的示例中，我们利用`RequireClaim`方法来配置我们的策略，要求用户处于角色`admin`。使用`RequireRole`会产生相同的结果。

#### 使用授权需求和处理程序

如果我们想用更复杂的规则实现策略，我们可以使用[授权需求](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/policies?view=aspnetcore-2.2#requirements)和[处理程序](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/policies?view=aspnetcore-2.2#authorization-handlers)。需求是一个包含数据的对象，处理程序应该对其进行评估，以检查用户是否可以访问所需的页面。

为了使用需求和处理程序，我们创建了一个新的需求类，它实现了一个`IAuthorizationRequirement`，然后我们创建了一个处理程序类，它实现了`IAuthorizationHandler`(它可以处理多个需求)，或者，我们将使用的方法，从`AuthorizationHandler<TRequirement>`继承而来，它可以作为一个基础，为一个单一的需求创建一个处理程序。

从需求开始，我们将创建一个名为`UsernameRequirement`的新类，它将包含一个模式，用户名必须满足该模式才能被允许访问。

`Policies\Requirements\UsernameRequirement.cs`

```
public class UsernameRequirement : IAuthorizationRequirement
{
    public UsernameRequirement(string usernamePattern)
    {
        UsernamePattern = usernamePattern;
    }

    public string UsernamePattern { get; }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们实现名为`UsernameRequirementHandler`的处理程序，它与当前登录用户的用户名提供的模式相匹配。

`Policies\Handlers\UsernameRequirementHandler.cs`

```
public class UsernameRequirementHandler : AuthorizationHandler<UsernameRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, UsernameRequirement requirement)
    {
        if (Regex.IsMatch(context.User.Identity.Name, requirement.UsernamePattern))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，如果需求得到满足，我们必须调用`AuthorizationHandlerContext`上的`Succeed`方法，否则对所需页面的访问将被拒绝。

最后，我们必须设置授权服务来使用这些新类。为此，让我们回到`Startup`类。

`Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddAuthorization(options =>
    {
        // ...
        options.AddPolicy("AnotherSamplePolicy", policy => policy.Requirements.Add(new UsernameRequirement(".*someone.*")));
    });

    services.AddSingleton<IAuthorizationHandler, UsernameRequirementHandler>();
    // ... 
```

Enter fullscreen mode Exit fullscreen mode

这里我们必须做两件事:配置一个策略来使用新的需求，并将处理程序添加到依赖注入容器中。

为了配置策略，我们像以前一样使用`AddPolicy`，但是现在我们向策略的`Requirements`集合添加一个新的需求，而不是`RequireClaim`。我们创建了一个新的`UsernameRequirement`实例，其模式与用户名相匹配。

将`UsernameRequirementHandler`添加到 DI 中与我们现在所习惯的更加相似。我把它作为单例添加，因为它既没有状态也没有依赖关系，所以我们可以安全地保存一个单个实例，不需要总是创建一个新的。

### 喊出基于资源的授权

虽然我们不会在这篇文章中真正探讨基于资源的授权，但我认为意识到它的存在是很重要的。

在我们在当前帖子中看到的例子中，我们只是根据一些静态信息(例如，是管理员，有某个用户名，...).这在某些情况下是不够的，除了被访问的页面之外，我们还想确保用户可以看到请求的内容。

以我们正在开发的组管理服务为例，我们不希望用户能够访问所有的组，而只能访问用户所属的组。这不能仅仅用我们一直使用的静态规则来强制执行，它将取决于被访问的特定资源。我们可以将一堆 if 与我们的业务逻辑代码混合在一起，或者以一种更加分离的方式来完成。正如我们在[文档](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/resourcebased?view=aspnetcore-2.2)中看到的，ASP.NET 核心为我们提供了一些设施来实现它。

在以后的帖子中，我们可能会探讨这个主题(或者使用其他方法来实现相同的主题)，最终是针对我刚才提到的组示例，但是我想将这些信息留在这里，以供任何正在寻找实现这种粒度访问控制的方法的人使用。

## 其他

快速浏览一下，就是这个。通过 ASP.NET 核心提供的授权特性，我们可以做更多的事情(我真的鼓励你去探索[文档](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/introduction?view=aspnetcore-2.2))，但是要有一些可能性的基础知识，希望这些例子是一个好的开始。在开发应用程序时，我们可能会使用一些更相关的特性。

帖子中的链接:

*   [ASP.NET 核心授权介绍](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/introduction?view=aspnetcore-2.2)
*   [ASP.NET 核心中基于角色的授权](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/roles?view=aspnetcore-2.2)
*   [ASP.NET 核心中基于策略的授权](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/policies?view=aspnetcore-2.2)
*   [ASP.NET 核心中基于资源的授权](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/resourcebased?view=aspnetcore-2.2)

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode019)。

感谢分享和反馈！

谢谢你的来访，西阿兹！