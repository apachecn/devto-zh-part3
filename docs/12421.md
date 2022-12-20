# 第 016 集-身份认证和剃刀页-ASP.NET 核心:从 0 到过度杀伤

> 原文：<https://dev.to/joaofbantunes/episode-016---authentication-with-identity-and-razor-pages---aspnet-core-from-0-to-overkill-82k>

在这一集里，我们开始使用 ASP.NET 核心身份和 Razor 页面构建认证服务。这将是一个独立的应用程序，集中所有必需的用户认证逻辑。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/wcrIn0AJQlA](https://www.youtube.com/embed/wcrIn0AJQlA)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在本帖中，我们将看看如何开始使用 ASP.NET 核心[身份](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-2.2)，它提供了为我们的 web 应用程序实现身份验证所需的一些细节。我们将在一个独立的认证服务中实现这一点，因此它可以被不同的客户端应用程序(web 前端、移动应用程序、...).

为了实现这个应用程序，我们将使用 [Razor Pages](https://docs.microsoft.com/en-us/aspnet/core/razor-pages/?view=aspnetcore-2.2) 。既然我们已经了解了 MVC，我们可以利用这个机会学习在 ASP.NET 核心中构建服务器端渲染应用程序的另一种方法。

## 实现认证的主要选项

在我们真正开始构建我们的身份验证服务之前，我想先看看我们必须做的可能选项。

我们来看看以下选项:

1.  预配置了身份的新 web 应用程序
2.  使用脚手架工具向应用程序添加身份
3.  手动向应用程序添加身份
4.  无视身份，滚自己的

### 1。预配置了身份的新 web 应用程序

这是获得带身份验证的完整应用程序的最简单方法。之后您可能需要做一些调整(例如，默认情况下它使用 SQLite 数据库)，但是您很快就可以启动并运行了。

要从命令行执行此操作，您只需使用以下命令:

```
dotnet new webapp --auth Individual -o WebAppName 
```

`auth`参数表示我们希望使用个人账户进行身份验证。其他选项包括(来自帮助输出):

```
None             - No authentication
Individual       - Individual authentication
IndividualB2C    - Individual authentication with Azure AD B2C
SingleOrg        - Organizational authentication for a single tenant
MultiOrg         - Organizational authentication for multiple tenants
Windows          - Windows authentication 
```

如果你沿着这条路走下去，你会注意到我们只有很少的特定于身份的代码，主要是一个`_ViewStart.cshtml`文件和`Startup`类中的一些配置。这是因为所有的身份信息都保存在其他包中，包括默认的 UI。如果我们想要覆盖它的特定部分，我们可以搭建它(这与下一个选项相关)。

### 2。使用脚手架工具向应用程序添加身份

如果我们想要在一个已经存在的应用程序中使用身份来实现身份验证(即使在我们创建它的最后一分钟还存在😛)或者覆盖默认实现中的一些东西，我们可以使用一个[工具来为我们搭建它](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/scaffold-identity?view=aspnetcore-2.2)。

首先，我们必须安装该工具(如果您想像我一样从终端进行安装，如果 Visual Studio 没有为您提供帮助的话)。

注意:以下所有内容都在[文档](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/scaffold-identity?view=aspnetcore-2.2)中，我把它们放在这里是为了更快地查阅。

```
dotnet tool install -g dotnet-aspnet-codegenerator 
```

我们需要向项目添加一个包。

```
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
```

现在我们可以在我们的项目中使用脚手架标识。这里我们也有很多选项，例如，如果我们运行带有`useDefaultUI`标志的生成器，比如`dotnet aspnet-codegenerator identity --useDefaultUI`，最终结果将类似于创建带有预配置身份的应用程序。另一方面，如果我们不使用这个标志，这个支架将包含所有的身份 UI 文件，所以我们可以随心所欲地使用它，再加上一个用于身份需求的`DbContext`、一些启动配置和`ScaffoldingReadme.txt`,我们应该检查它们以进行一些必要的额外配置。

### 3。手动向应用程序添加身份

另一个选项，也是我们在这个项目中要做的，是创建一个空的应用程序，并利用 Identity 提供的类开始构建所需的身份验证页面。

这种方法的工作量可能比要求的要多，因为前面的选项会以少得多的工作量得到几乎相同的结果，但是由于这更像是一个学术项目，我们希望尽可能多地了解身份是如何工作的，所以我们将走更长的路。

为了避免浪费太多时间，我们可以创建另一个项目并在其上搭建标识，这样我们就可以把它作为参考。

### 4。无视身份，滚自己的

我们也可以选择忽略已经存在的东西，推出我们自己的产品。在某些情况下，这可能是有意义的，但我认为大多数时候没有意义，尤其是在更复杂的主题中，如身份验证。

如果有一些东西已经被测试并证明运行良好(也是由比我聪明的人构建的)，我会说使用这样的资源是一个更好的选择。

## 创建 web 应用

对于这个认证服务，我在这里创建了一个新的 GitHub repo [。它的组织方式和我们在《T2》第 002 集](https://github.com/AspNetCoreFromZeroToOverkill/Auth/)中谈到的一样。

在`src`文件夹中，我们通过运行以下命令创建一个新的空 web 应用程序项目:

```
dotnet new web -o CodingMilitia.PlayBall.Auth.Web 
```

然后我们可以对`Startup`类做一些修改，只是为了准备我们将要构建的东西，一个 Razor Pages 应用程序(Razor Pages 和 MVC 一起出现，所以现在我们看不到任何 Razor Pages 特定的东西)。

`Startup.cs`

```
public class Startup
{
    private readonly IConfiguration _configuration;

    public Startup(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    public void ConfigureServices(IServiceCollection services)
    {
        services
            .AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseStaticFiles();
        app.UseMvcWithDefaultRoute();
    }
} 
```

## 数据库

现在我们已经准备好了应用程序的基础，让我们通过创建一个`DbContext`来开始认证工作，为我们处理持久性。我们可以(或许应该)将数据库部分放在不同的项目中，但是为了简单起见，我们将把所有内容放在 web 应用程序项目中。

在一个新的`Data`文件夹中，我们创建了一个新的类`AuthDbContext`。这个类继承自`IdentityDbContext<TUser>`(其中`TUser`继承自`IdentityUser`)，它提供了 Identity 持久化数据所需的一切。

从这个`IdentityDbContext<TUser>`继承允许我们覆盖我们可能想要的某些东西。在这种情况下，我们指定我们想要一个类`PlayBallUser`来代表我们的用户。

`AuthDbContext.cs`

```
public class AuthDbContext : IdentityDbContext<PlayBallUser>
{
    public AuthDbContext(DbContextOptions<AuthDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        builder.HasDefaultSchema("public");
        base.OnModelCreating(builder);
    }
} 
```

如果我们希望用户的配置文件有额外的信息，除了已经存在于`IdentityUser`中的信息，我们可以将它添加到一个继承自它的类中，在本例中是`PlayBallUser`。我们现在真的不需要任何额外的信息，所以`PlayBallUser`只是一个从`IdentityUser`继承的空类。在这种情况下，我们不需要创建一个新的类，我们可以只使用`IdentityUser`，但我还是这样做了😛。

创建迁移与我们已经在第 011 集中看到的相同，所以我将跳过这一部分。

为了了解这个上下文是如何与身份相关联的，让我们看看`Startup`类。

## 启动配置

为了准备我们的应用程序使用 Identity(以及告诉 Identity 使用创建的`AuthDbContext`)，我们必须对`Startup`类进行一些更改。

`Startup.cs`

```
public class Startup
{
    private readonly IConfiguration _configuration;

    public Startup(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        services.AddDbContext<AuthDbContext>(options =>
        {
            options.UseNpgsql(_configuration.GetConnectionString("AuthDbContext"));
        });

        services
            .AddIdentity<PlayBallUser, IdentityRole>(options =>
            {
                options.Password.RequireDigit = false;
                options.Password.RequiredLength = 12; 
                options.Password.RequireLowercase = false;
                options.Password.RequireUppercase = false;
                options.Password.RequireNonAlphanumeric = false;
            })
            .AddEntityFrameworkStores<AuthDbContext>()
            .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options =>
        {
            options.LoginPath = "/Login";
            options.LogoutPath = "/Logout";
            options.AccessDeniedPath = "/AccessDenied";
        });
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseStaticFiles();
        app.UseAuthentication();
        app.UseMvcWithDefaultRoute();
    }
} 
```

我可能添加了太多尚未进入上下文的东西，但让我们一次看一件事，从`ConfigureServices`开始。

我们从`AddDbContext`开始，在依赖注入容器中注册数据库上下文，就像我们在过去的章节中看到的那样。

然后，我们配置身份服务:

*   `AddIdentity<PlayBallUser, IdentityRole>`告诉 Identity 我们想用`PlayBallUser`来表示用户，用`IdentityRole`(由 Identity 提供，像`IdentityUser`)来表示角色，但是我们不会真的在应用程序中使用角色。
    *   选项允许我们配置各种东西，在这种情况下，我们只是调整密码要求，这有令人讨厌的默认值。比起许多挑剔的规则，我更喜欢使用更大的密码。
*   `AddEntityFrameworkStores`告诉 Identity 应该使用什么样的数据库上下文来实现持久性。
*   `AddDefaultTokenProviders`将身份配置为使用其自带的默认令牌提供程序。这些是在诸如双因素认证、密码重置等方面使用的令牌的提供者。

在身份细节之后，我们有`ConfigureApplicationCookie`，我觉得这个名字不太合适，因为它不仅仅是 cookie 特有的东西。在本例中，我们设置了一些引用端点，因此身份验证/授权流程知道根据具体情况重定向到哪里。我们还没有这些页面，但当我们有了，框架将能够根据需要自动发送用户到登录，注销和访问被拒绝的页面。

进入`Startup`类的`Configure`方法，我们刚刚添加了认证中间件的注册，通过使用`UseAuthentication`，它将负责检查所有请求中的认证用户，将其信息存储在请求上下文中(`HttpContext.User`)。

## 注册页面

配置就绪后，让我们开始创建页面，从注册页面开始。如上所述，我们将使用 Razor 页面，但现在我们将尽可能保持简单，不用担心共享布局或页面的整体外观。

按照通常的惯例，我们在项目根目录下创建一个`Pages`文件夹，在那里我们将放置不同的页面。

在这个新文件夹中，我们添加了一个名为`Register`的新页面，这为我们创建了两个新文件，`Register.cshtml`和`Register.cshtml.cs`。第一个是我们放置页面标记的地方，后者有服务器端逻辑来处理请求。虽然不完全相同，但如果我们想将其映射到 MVC，那么`*.cshtml`将是视图，`*.cshtml.cs`将是控制器。`*.cshtml.cs`文件有时也被称为代码隐藏文件，因为它们总是与`*.cshtml`一起添加支持逻辑。不知道我们是否打算这样称呼它，但它来自 ASP.NET 网络表单，我们在那里有`*.aspx`和`*.aspx.cs`。

* * *

快速注意，在继续使用`Register`页面之前，我们不应该忘记创建一个`_ViewImports.cshtml`来导入标签助手，因为我们将需要它们来构建我们的标记。

`_ViewImports.cshtml`

```
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers 
```

* * *

我们可以从查看`Register.cshtml`文件开始，因为它相当简单。

`Register.cshtml`

```
@page
@model CodingMilitia.PlayBall.Auth.Web.Pages.RegisterModel

<h1>Register</h1>
<form asp-route-returnUrl="@Model.ReturnUrl" method="post">
    <div asp-validation-summary="All"></div>
    <div >
        <label asp-for="Input.Email"></label>
        <input asp-for="Input.Email"/>
        <span asp-validation-for="Input.Email"></span>
    </div>
    <div>
        <label asp-for="Input.Password"></label>
        <input asp-for="Input.Password"/>
        <span asp-validation-for="Input.Password"></span>
    </div>
    <div >
        <label asp-for="Input.ConfirmPassword"></label>
        <input asp-for="Input.ConfirmPassword"/>
        <span asp-validation-for="Input.ConfirmPassword"></span>
    </div>
    <button type="submit">Register</button>
</form> 
```

在文件的开头，我们有一个`@page`，将它标识为 Razor 页面，而不是常规的 MVC 视图，因为那些也是`*.cshtml`文件。这使得页面本身就像一个控制器的动作，而不需要控制器来处理请求，然后将呈现逻辑转发给视图。

文件中的第二行指出实现页面动作逻辑的类。在这种情况下，它是包含在`Register.cshtml.cs`文件中的`RegisterModel`。

文件的其余部分更像我们在 MVC 中所习惯的，但是有一些细微的差别。`Model`属性引用了在顶部定义的页面模型(`RegisterModel`)。正如我们在查看代码隐藏文件时将会看到的，我们所指的`Input`是页面模型的一个属性，它被标记为一个`BindProperty`属性。这实现了与 MVC 控制器动作中的输入模型相似的目标。

带有`asp-validation-summary="All"`属性的`div`将显示页面上发现的所有验证错误(只有在我们单击提交按钮之后，因为验证是在服务器端发生的)。具有`asp-validation-for`属性的各种`span`将显示每个表单域的验证错误。

现在让我们看看页面请求处理逻辑，它在`Register.cshtml.cs`文件中。

`Register.cshtml.cs`

```
[AllowAnonymous]
public class RegisterModel : PageModel
{
    private readonly SignInManager<PlayBallUser> _signInManager;
    private readonly UserManager<PlayBallUser> _userManager;
    private readonly ILogger<RegisterModel> _logger;
    private readonly IEmailSender _emailSender;

    public RegisterModel(
        UserManager<PlayBallUser> userManager,
        SignInManager<PlayBallUser> signInManager,
        ILogger<RegisterModel> logger,
        IEmailSender emailSender)
    {
        _userManager = userManager;
        _signInManager = signInManager;
        _logger = logger;
        _emailSender = emailSender;
    }

    [BindProperty]
    public InputModel Input { get; set; }

    public string ReturnUrl { get; set; }

    public class InputModel
    {
        [Required]
        [EmailAddress]
        [Display(Name = "Email")]
        public string Email { get; set; }

        [Required]
        [StringLength(100, ErrorMessage = "The {0} must be at least {2} and at max {1} characters long.", MinimumLength = 12)]
        [DataType(DataType.Password)]
        [Display(Name = "Password")]
        public string Password { get; set; }

        [DataType(DataType.Password)]
        [Display(Name = "Confirm password")]
        [Compare("Password", ErrorMessage = "The password and confirmation password do not match.")]
        public string ConfirmPassword { get; set; }
    }

    public void OnGet(string returnUrl = null)
    {
        ReturnUrl = returnUrl;
    }

    public async Task<IActionResult> OnPostAsync(string returnUrl = null)
    {
        if (ModelState.IsValid)
        {
            var user = new PlayBallUser
            {
                UserName = Input.Email, 
                Email = Input.Email,
            };
            var result = await _userManager.CreateAsync(user, Input.Password);
            if (result.Succeeded)
            {
                _logger.LogInformation("New user created.");

                var code = await _userManager.GenerateEmailConfirmationTokenAsync(user);
                var callbackUrl = Url.Page(
                    "/ConfirmEmail",
                    pageHandler: null,
                    values: new { userId = user.Id, code = code },
                    protocol: Request.Scheme);

                await _emailSender.SendEmailAsync(Input.Email, "Confirm your email",
                    $"Please confirm your account by <a href='{HtmlEncoder.Default.Encode(callbackUrl)}'>clicking here</a>.");

                await _signInManager.SignInAsync(user, isPersistent: false);

                if (string.IsNullOrWhiteSpace(returnUrl))
                {
                    return LocalRedirect("~/");
                }
                else
                {
                    return Redirect(returnUrl);
                }

            }
            foreach (var error in result.Errors)
            {
                ModelState.AddModelError(string.Empty, error.Description);
            }
        }

        // If we got this far, something failed, redisplay form
        return Page();
    }
} 
```

让我们走过这面代码墙😛

从简单的比特开始，注入依赖。请特别注意`SignInManager<PlayBallUser>`和`UserManager<PlayBallUser>`，因为当我们实现这个认证逻辑时，这些是一些最常用的类。名称本身就说明了这项服务的作用，但我们很快就会看到它们的实际应用。其他依赖项也很容易猜到它们的目的是什么，一个日志记录器和用户注册时发送电子邮件的方式，允许电子邮件确认。

现在来看看公开的属性，我们有几个属性，`ReturnUrl`和前面提到的`Input`。`ReturnUrl`是通过查询字符串中的请求传递的，所以我们将它存储在一个属性中，这样我们就可以在页面标记中访问它(注意它在`Register.cshtml`的表单标记帮助器中的用法)。`Input`属性标有`BindProperty`，这意味着当页面处理 POST 请求时，它将经过模型绑定，其信息被表单发送的内容所滋润。

在属性之后，我们看到了`InputModel`类的定义。因为它确实是特定于页面的，我们可以只在`RegisterModel`类中声明，但是如果我们愿意，我们也可以把它放在其他地方。它的属性修饰属性是典型的用于验证的属性，在传统的 MVC 应用程序中也可以看到。我想说这里不需要解释，它们已经很明显了。

最后，我们到达请求处理逻辑。通过使用`OnGet`、`OnPost`、`OnPut`和`OnDelete`，给定 HTTP 方法，对页面路由的请求被匹配到正确的处理程序方法。还有异步同行、`OnGetAsync`、`OnPostAsync`等。

实际上没有那么多事情发生。我们只是将返回的 url 存储在一个属性中，这样就可以在 HTML 创建中访问它。

现在在`OnPostAsync`中，我们有注册新用户的逻辑。作为一个参数，我们只得到相同的返回 url，但是正如前面提到的,`Input`属性也用 post 提交的结果初始化了它的值。

在检查模型是否有效之后，我们利用`_userManager`来创建新用户。如果这个操作成功，我们再次使用`_userManager`为它生成一个用于电子邮件确认的令牌(使用`GenerateEmailConfirmationTokenAsync`，然后我们将在发送给用户的电子邮件中使用它。它最终会把用户带到一个`ConfirmEmail`页面，我们还没有创建这个页面。

电子邮件发送后，我们可以登录用户(使用`_signInManager`)或者重定向到登录页面。在这种情况下，我们只是登录。

之后，我们可以将用户重定向到`ReturnUrl`(或者其他地方，如果不存在的话)。

如果用户创建不成功，我们可以获取错误信息并将其添加到`ModelState`中，这样就可以显示给用户了。

* * *

旁注:我们真的不需要添加任何防伪验证代码，就像添加我们在 003 集看到的`ValidateAntiForgeryToken`属性，因为 [Razor Pages 为我们自动完成了](https://docs.microsoft.com/en-us/aspnet/core/razor-pages/index?view=aspnetcore-2.2#xsrfcsrf-and-razor-pages)。

* * *

* * *

另一个注意事项:这里并不真正需要`AllowAnonymous`,因为默认情况下页面允许匿名访问，除非它们所在的文件夹被配置为要求用户进行身份验证，但事实并非如此。

* * *

## 登录页面

继续进入登录页面，我们会看到更多的是相同的，作为主要的身份感兴趣的部分。

先说`Login.cshtml`。

`Login.cshtml`

```
@page
@model CodingMilitia.PlayBall.Auth.Web.Pages.LoginModel

<h1>Login</h1>

<form method="post">
    <div asp-validation-summary="All"></div>
    <div>
        <label asp-for="Input.Email"></label>
        <input asp-for="Input.Email"/>
        <span asp-validation-for="Input.Email"></span>
    </div>
    <div >
        <label asp-for="Input.Password"></label>
        <input asp-for="Input.Password"/>
        <span asp-validation-for="Input.Password"></span>
    </div>
    <div >
        <div>
            <label asp-for="Input.RememberMe">
                <input asp-for="Input.RememberMe"/>
                @Html.DisplayNameFor(m => m.Input.RememberMe)
            </label>
        </div>
    </div>
    <div >
        <button type="submit">Log in</button>
    </div>
    <div >
        <p>
            <a id="forgot-password" asp-page="./ForgotPassword">Forgot your password?</a>
        </p>
        <p>
            <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
        </p>
    </div>
</form> 
```

简单的东西，非常类似于注册页面。我们有另一个表单，它有一个后台属性`Input`来处理它的字段，还有一些验证属性来告诉用户哪里出错了。在页面的末尾，我们有几个链接带用户恢复密码和注册页面。

现在来看代码隐藏。

`Login.cshtml.cs`

```
[AllowAnonymous]
public class LoginModel : PageModel
{
    private readonly SignInManager<PlayBallUser> _signInManager;
    private readonly ILogger<LoginModel> _logger;

    public LoginModel(SignInManager<PlayBallUser> signInManager, ILogger<LoginModel> logger)
    {
        _signInManager = signInManager;
        _logger = logger;
    }

    [BindProperty]
    public InputModel Input { get; set; }

    public string ReturnUrl { get; set; }

    [TempData]
    public string ErrorMessage { get; set; }

    public class InputModel
    {
        [Required]
        [EmailAddress]
        public string Email { get; set; }

        [Required]
        [DataType(DataType.Password)]
        public string Password { get; set; }

        [Display(Name = "Remember me?")]
        public bool RememberMe { get; set; }
    }

    public void OnGet(string returnUrl = null)
    {
        if (!string.IsNullOrEmpty(ErrorMessage))
        {
            ModelState.AddModelError(string.Empty, ErrorMessage);
        }

        ReturnUrl = returnUrl;
    }

    public async Task<IActionResult> OnPostAsync(string returnUrl = null)
    {
        returnUrl = returnUrl ?? Url.Content("~/");

        if (ModelState.IsValid)
        {
            var result = await _signInManager.PasswordSignInAsync(Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
            if (result.Succeeded)
            {
                _logger.LogInformation("User logged in.");
                return Redirect(returnUrl);
            }
            if (result.RequiresTwoFactor)
            {
                return RedirectToPage("./LoginWith2fa", new { ReturnUrl = returnUrl, RememberMe = Input.RememberMe });
            }
            if (result.IsLockedOut)
            {
                _logger.LogWarning("User account locked out.");
                return RedirectToPage("./Lockout");
            }
            else
            {
                ModelState.AddModelError(string.Empty, "Invalid login attempt.");
                return Page();
            }
        }

        // If we got this far, something failed, redisplay form
        return Page();
    }
} 
```

如上所述，这个页面的工作方式与注册页面类似。额外单词最有价值的部分可能是对`SignInManager`和临时数据的使用。

关于`SignInManager`，我们使用它来登录用户，利用`PasswordSignInAsync`方法。除了告诉我们登录是成功还是失败，结果还有一些额外的信息，比如帐户是否需要双因素身份验证(在这种情况下，我们应该重定向到处理这个问题的页面)或者帐户是否被锁定，这意味着用户此时不能登录。

关于临时数据，您可能已经注意到了`ErrorMessage`属性中的`TempData`属性。当我们想重定向到另一个页面，但显示一些额外的信息时，这是很有用的。在这种情况下，假设在另一个页面中发生了一些事情，需要用户重新登录。该页面将在重定向到登录页面之前设置错误消息，使登录页面显示该消息。访问临时数据后，该数据将被删除，不再显示。

默认情况下，临时数据存储在 cookies 中，但也有其他选择，如您在这里看到的。

## 注销页面

注销页面确实没什么可看的，但我们还是要做。我将跳过`Logout.cshtml`，它只显示了一个`<h1>Logout</h1>`。

在`Logout.cshtml.cs`上，我们只需在收到 post 请求时使用`SignInManager`注销用户。

```
[AllowAnonymous]
public class LogoutModel : PageModel
{
    private readonly SignInManager<PlayBallUser> _signInManager;
    private readonly ILogger<LogoutModel> _logger;

    public LogoutModel(SignInManager<PlayBallUser> signInManager, ILogger<LogoutModel> logger)
    {
        _signInManager = signInManager;
        _logger = logger;
    }

    public void OnGet()
    {
    }

    public async Task<IActionResult> OnPost(string returnUrl = null)
    {
        await _signInManager.SignOutAsync();
        _logger.LogInformation("User logged out.");
        if (returnUrl != null)
        {
            return Redirect(returnUrl);
        }
        else
        {
            return Page();
        }
    }
} 
```

## 账户页面

既然我们可以注册、登录和注销用户，我们可以看看帐户设置，用户只有在通过身份验证后才能获得。

为了保留所有与账户管理相关的页面，我们在`Pages`中创建了一个新文件夹`Account`。

为了使对该文件夹中页面的所有访问都得到验证，我们可以向所有页面模型类添加`Authorize`属性，但是我们可以做得更好。回到`Startup`类，我们可以添加一些 Razor Pages 约定来使我们的生活更容易。

`Startup.cs`

```
public class Startup
{
   // ...

    public void ConfigureServices(IServiceCollection services)
    {
        services
            .AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
            .AddRazorPagesOptions(options =>
            {
                options.Conventions.AuthorizeFolder("/Account");
            });
        // ...
    }

    // ...
} 
```

通过将这个`AuthorizeFolder`扩展方法用于 Razor Pages 约定，我们避免了到处散布属性(正如我提到的，不需要`AllowAnonymous`属性，因为其他页面在这个文件夹之外)。

我们可以使用`AddRazorPagesOptions`在 Razor 页面中配置更多的东西，所以你可以探索它，但是现在这是我们所需要的。

现在来处理一下账户页面。在新创建的`Account`文件夹中，我们可以添加一个`Index`页面，这将是帐户设置的入口点。

`Account/Index.cshtml`

```
@page
@model CodingMilitia.PlayBall.Auth.Web.Pages.Account.IndexModel

<form asp-page="../Logout" method="post">
    <button type="submit">Logout</button>
</form>
<div>@Model.StatusMessage</div>
<div >
    <form id="profile-form" method="post">
        <div asp-validation-summary="All"></div>
        <div>
            <label asp-for="Username"></label>
            <input asp-for="Username" disabled/>
        </div>
        <div>
            <label asp-for="Input.Email"></label>
            @if (Model.IsEmailConfirmed)
            {
                <div>
                    <input asp-for="Input.Email"/>
                </div>
            }
            else
            {
                <input asp-for="Input.Email"/>
                <button id="email-verification" type="submit" asp-page-handler="SendVerificationEmail">Send verification email</button>
            }
            <span asp-validation-for="Input.Email"></span>
        </div>
        <button id="update-profile-button" type="submit">Save</button>
    </form>
</div> 
```

这里我们有另一个相当简单的形式。该页面显示用户的当前信息，在这种情况下，只有用户名和电子邮件(这是相同的，但如果我们想要的话可以不同)。

如果电子邮件尚未通过验证，将显示发送验证电子邮件的选项。如果您注意到，我们使用了一个`asp-page-handler`属性，它将验证电子邮件请求映射到页面模型中一个名为`OnPostSendVerificationEmailAsync`的方法。这是处理页面请求的方法，这些请求不映射到通常的(在 Razor 页面中)`OnGet`、`OnPost`等等。

现在我们来看看`Account/Index.cshtml.cs`里发生了什么。

`Account/Index.cshtml.cs`

```
public class IndexModel : PageModel
{
    private readonly UserManager<PlayBallUser> _userManager;
    private readonly SignInManager<PlayBallUser> _signInManager;
    private readonly IEmailSender _emailSender;

    public IndexModel(
        UserManager<PlayBallUser> userManager,
        SignInManager<PlayBallUser> signInManager,
        IEmailSender emailSender)
    {
        _userManager = userManager;
        _signInManager = signInManager;
        _emailSender = emailSender;
    }

    public string Username { get; set; }

    public bool IsEmailConfirmed { get; set; }

    [TempData]
    public string StatusMessage { get; set; }

    [BindProperty]
    public InputModel Input { get; set; }

    public class InputModel
    {
        [Required]
        [EmailAddress]
        public string Email { get; set; }
    }

    public async Task<IActionResult> OnGetAsync()
    {
        var user = await _userManager.GetUserAsync(User);
        if (user == null)
        {
            return NotFound($"Unable to load user with ID '{_userManager.GetUserId(User)}'.");
        }

        var userName = await _userManager.GetUserNameAsync(user);
        var email = await _userManager.GetEmailAsync(user);

        Username = userName;

        Input = new InputModel
        {
            Email = email
        };

        IsEmailConfirmed = await _userManager.IsEmailConfirmedAsync(user);

        return Page();
    }

    public async Task<IActionResult> OnPostAsync()
    {
        if (!ModelState.IsValid)
        {
            return Page();
        }

        var user = await _userManager.GetUserAsync(User);
        if (user == null)
        {
            return NotFound($"Unable to load user with ID '{_userManager.GetUserId(User)}'.");
        }

        var email = await _userManager.GetEmailAsync(user);
        if (Input.Email != email)
        {
            //TODO: what if the first succeeds but the second fails?
            var setUserNameResult = await _userManager.SetUserNameAsync(user, Input.Email);
            var setEmailResult = setUserNameResult.Succeeded
                ? (await _userManager.SetEmailAsync(user, Input.Email))
                : IdentityResult.Failed();

            if (!setUserNameResult.Succeeded || !setEmailResult.Succeeded)
            {
                var userId = await _userManager.GetUserIdAsync(user);
                throw new InvalidOperationException($"Unexpected error occurred setting email for user with ID '{userId}'.");
            }
        }

        await _signInManager.RefreshSignInAsync(user);
        StatusMessage = "Your profile has been updated";
        return RedirectToPage();
    }

    public async Task<IActionResult> OnPostSendVerificationEmailAsync()
    {
        if (!ModelState.IsValid)
        {
            return Page();
        }

        var user = await _userManager.GetUserAsync(User);
        if (user == null)
        {
            return NotFound($"Unable to load user with ID '{_userManager.GetUserId(User)}'.");
        }

        var userId = await _userManager.GetUserIdAsync(user);
        var email = await _userManager.GetEmailAsync(user);
        var code = await _userManager.GenerateEmailConfirmationTokenAsync(user);
        var callbackUrl = Url.Page(
            "/Account/ConfirmEmail",
            pageHandler: null,
            values: new { userId = userId, code = code },
            protocol: Request.Scheme);
        await _emailSender.SendEmailAsync(
            email,
            "Confirm your email",
            $"Please confirm your account by <a href='{HtmlEncoder.Default.Encode(callbackUrl)}'>clicking here</a>.");

        StatusMessage = "Verification email sent. Please check your email.";
        return RedirectToPage();
    }
} 
```

像以前一样，要处理的主要依赖项仍然是`UserManager`和`SignInManager`。

在 properties 部门，我们继续看到相同的概念，`Input`处理表单提交，temp 数据存储一些消息以呈现给用户。

看一下`OnGetAsync`，我们从数据库中获取当前用户的信息，将代表当前请求上下文中的用户的`ClaimsPrincipal`传递给`UserManager`，该信息是从 cookies 中获得的。

在我们得到所有我们需要的(用户名，电子邮件和电子邮件验证状态)后，我们可以向用户展示这些信息。

在`OnPostAsync`中，我们检查电子邮件是否被更改，如果是，我们尝试更改它，再次求助于`UserManager`。由于用户名和电子邮件是相同的，对于当前版本的 ASP.NET 核心，我们需要单独更新它们，这不仅是一件痛苦的事情，而且如果第一个成功，第二个失败，可能会导致问题，因为这不是在事务中完成的。关于这个问题，GitHub 上有一个[问题，所以希望它能很快得到解决。](https://github.com/aspnet/AspNetCore/issues/6613)

如果电子邮件被更改，那么我们必须刷新存储在 cookies 中的用户信息，以便在下一次请求时，数据与所做的更改保持一致。

最后，我们到达`OnPostSendVerificationEmailAsync`，它的功能与我们在注册页面看到的基本相同。获取有关用户的一些信息，包括生成新的电子邮件验证令牌，然后准备并发送电子邮件。

## 其他

这一集到此为止，这一集可不小😛。在下一集，我们将继续研究身份和构建剩余的页面。

我认为主要的收获应该是，在构建应用程序的身份验证过程时，身份已经满足了我们的大多数常规需求。一切都是非常可配置的，但是如果我们不需要所有这些，或者如果我们只是想做一些小的修改，使用默认值加上一些脚手架可以节省相当多的时间。

帖子中的链接:

*   [ASP.NET 核心剃刀页](https://docs.microsoft.com/en-us/aspnet/core/razor-pages/?view=aspnetcore-2.2)
*   [ASP.NET 核心身份](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-2.2)
*   [ASP.NET 核心项目脚手架标识](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/scaffold-identity?view=aspnetcore-2.2)
*   [ASP.NET 核心中的会话和应用状态](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/app-state?view=aspnetcore-2.2)

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode016)。

感谢分享和反馈！

谢谢你的来访，西阿兹！