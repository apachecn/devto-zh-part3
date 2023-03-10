# 第 017 集-更多的身份，更多的剃刀页-ASP.NET 核心:从 0 到矫枉过正

> 原文：<https://dev.to/joaofbantunes/episode-017-more-identity-more-razor-pages-asp-net-core-from-0-to-overkill-1h8b>

在这一集里，我们继续开发我们的认证服务，同时继续探索 ASP.NET 核心身份和 Razor 页面。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/TO2MNzdsZWw](https://www.youtube.com/embed/TO2MNzdsZWw)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在本帖中，我们将使用 ASP.NET 核心[身份](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-2.2)和[剃刀页面](https://docs.microsoft.com/en-us/aspnet/core/razor-pages/?view=aspnetcore-2.2)总结上一集开始的登录和注册过程。

### 自上次发布后的变化

我将跳过自上一篇文章以来我做的一些事情，因为不是所有的事情都太有趣，但总结起来:

*   对之前创建的页面进行了调整，为添加引导做准备。
*   添加了一些缺失的页面，如电子邮件确认、密码更改和重置。

如果您对这些有任何疑问，请随时联系我们😉

## 双因素认证

为了实现双因素认证，我们将使用[基于时间的一次性密码算法](https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm)或简称为 TOTP。

与我们目前已经实现的身份验证流程的其他部分一样，双因素身份验证(从现在开始我将缩写为 2fa)并不太难实现，因为 ASP.NET 核心身份提供了我们实现它所需的一切。然而，它需要大量的页面来实现完整的功能集，所以让我们快速看一下所需的主要步骤:

*   用户启用和配置 2fa 的方法
*   为用户提供二维码以设置设备
*   生成并向用户显示恢复代码列表，以防配置的设备发生任何问题
*   禁用 2fa，同时重置已配置的设备
*   在登录过程中添加一个步骤，要求输入 2fa 代码或恢复代码作为备用代码

我不会深入所有这些步骤的细节，因为我认为通过查看[代码](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode017)来了解它们中的大多数应该不会太难，所以我将只快速浏览其中的一些。

当我们开始看一些代码时，请记住，当我们从 Identity 中取出所有位时，大部分代码都是现成的，正如我们在上一集中看到的。我将展示的一些部分完全是开箱即用的，其他部分我做了一些调整以更符合我的喜好。

### 管理双因素认证

在帐户主页中，我们需要一种链接到 2fa 管理页面的方式，因此在`Index.cshtml`结束时，我们添加了以下内容:

`Pages/Account/Index.cshtml`

```
<!-- ... -->
<div>
    @if (Model.IsTwoFactorEnabled)
    {
        <a asp-page="./ManageTwoFactor" class="btn btn-link">Manage two-factor authentication</a>
    }
    else
    {
        <a asp-page="./ManageTwoFactor" class="btn btn-link">Enable two-factor authentication</a>
    }
</div>
<!-- ... --> 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要一个用于 2fa 管理的主页，在本例中是`ManageTwoFactor.cshtml`。在本页中，我们仅根据用户的 2fa 配置的当前状态显示一些选项。

如果用户已经启用了两个因素，根据恢复代码的使用情况，我们可能会建议生成一组新的代码。

除此之外，在这里我们为用户提供了以下选项:

*   禁用 2fa(如果已启用)
*   忘记当前的浏览器，下次登录时询问代码
*   添加验证器设备或重置现有设备

`Pages/Account/ManageTwoFactor.cshtml`

```
@page
@model CodingMilitia.PlayBall.Auth.Web.Pages.Account.ManageTwoFactorModel
@{
    ViewData["Title"] = "Manage two-factor authentication";
}
<h4>@ViewData["Title"]</h4>
<div>@Model.StatusMessage</div>
@if (Model.IsTwoFactorEnabled)
{
    if (Model.RecoveryCodesLeft == 0)
    {
        <div class="alert alert-danger">
            <strong>You have no recovery codes left.</strong>
            <p>You must <a asp-page="./GenerateRecoveryCodes">generate a new set of recovery codes</a> before you can log in with a recovery code.</p>
        </div>
    }
    else if (Model.RecoveryCodesLeft == 1)
    {
        <div class="alert alert-danger">
            <strong>You have 1 recovery code left.</strong>
            <p>You can <a asp-page="./GenerateRecoveryCodes">generate a new set of recovery codes</a>.</p>
        </div>
    }
    else if (Model.RecoveryCodesLeft <= 3)
    {
        <div class="alert alert-warning">
            <strong>You have @Model.RecoveryCodesLeft recovery codes left.</strong>
            <p>You should <a asp-page="./GenerateRecoveryCodes">generate a new set of recovery codes</a>.</p>
        </div>
    }

    if (Model.IsMachineRemembered)
    {
        <form method="post" style="display: inline-block">
            <button type="submit" class="btn btn-primary">Forget this browser</button>
        </form>
    }
    <a asp-page="./DisableTwoFactor" class="btn btn-primary">Disable two-factor authentication</a>
    <a asp-page="./GenerateRecoveryCodes" class="btn btn-primary">Reset recovery codes</a>
}

<h5>Authenticator app</h5>
@if (!Model.HasAuthenticator)
{
    <a id="enable-authenticator" asp-page="./AddTwoFactorApp" class="btn btn-primary">Setup authenticator app</a>
}
else
{
    <a id="enable-authenticator" asp-page="./AddTwoFactorApp" class="btn btn-primary">Setup another authenticator app</a>
    <a id="reset-authenticator" asp-page="./ResetTwoFactorApp" class="btn btn-primary">Reset authenticator app</a>
} 
```

Enter fullscreen mode Exit fullscreen mode

这个页面的代码隐藏相当简单，因为我们所做的就是利用`UserManager`和`SignInManager`来获取信息，并在 POST 请求时忘记浏览器。

`Pages/Account/ManageTwoFactor.cshtml.cs`

```
//...
public async Task<IActionResult> OnGetAsync()
{
    var user = await _userManager.GetUserAsync(User);
    if (user == null)
    {
        return NotFound($"Unable to load user with ID '{_userManager.GetUserId(User)}'.");
    }

    HasAuthenticator = await _userManager.GetAuthenticatorKeyAsync(user) != null;
    IsTwoFactorEnabled = await _userManager.GetTwoFactorEnabledAsync(user);
    IsMachineRemembered = await _signInManager.IsTwoFactorClientRememberedAsync(user);
    RecoveryCodesLeft = await _userManager.CountRecoveryCodesAsync(user);

    return Page();
}

public async Task<IActionResult> OnPostAsync()
{
    var user = await _userManager.GetUserAsync(User);
    if (user == null)
    {
        return NotFound($"Unable to load user with ID '{_userManager.GetUserId(User)}'.");
    }

    await _signInManager.ForgetTwoFactorClientAsync();
    StatusMessage =
        "The current browser has been forgotten. When you login again from this browser you will be prompted for your 2fa code.";
    return RedirectToPage();
}
//... 
```

Enter fullscreen mode Exit fullscreen mode

### 设置新的认证器设备

当设置新设备时，我们会前往`AddTwoFactorApp.cshtml`。在这里，我们提供了一个二维码(如果用户喜欢/需要输入，则直接提供密钥)供用户在 authenticator 应用程序上扫描。扫描后，用户应输入代码以继续配置过程。

开箱即用的实现准备使用一个 JavaScript 库来呈现 QR 代码，但是由于我试图在认证服务中最小化 JS 的使用，我们将使用 [SkiaSharp 在服务器端生成 QR 代码。二维码](https://github.com/guitarrapc/SkiaSharp.QrCode)。

对于服务器端生成，我想到了几个将 QR 码传递到前端的选项:

*   让一个端点接收密钥并生成图像作为输出
*   在 base 64 中生成并编码图像，然后将其直接嵌入页面

第一个选项可能更干净，因为从页面的角度来看，它是一个常规的静态图像，但是作为一个 GET 请求，我们最终会在服务器日志中使用共享密钥。如果我能避免泄露秘密，我会的，所以这是一个 base 64 编码的图像！🙂

为了实现 QR 码的生成，我们创建了一个新的类(具有匹配的接口)，恰当地命名为`Base64QrCodeGenerator`(之后不要忘记在 DI 容器中注册它)。

`Base64QrCodeGenerator.cs`

```
public class Base64QrCodeGenerator : IBase64QrCodeGenerator
{
    public string Generate(Uri target)
    {
        using (var generator = new QRCodeGenerator())
        {
            var code = generator.CreateQrCode(target.OriginalString, ECCLevel.Q);

            var info = new SKImageInfo(256, 256);
            using (var surface = SKSurface.Create(info))
            {
                var canvas = surface.Canvas;
                canvas.Render(code, info.Width, info.Height);

                using (var image = surface.Snapshot())
                using (var data = image.Encode(SKEncodedImageFormat.Png, 100))
                {
                    return Convert.ToBase64String(data.ToArray());
                }
            }
        }            
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

老实说，我没有花太多时间在这段代码上，它肯定可以被优化。无论如何，我们已经有了一个非常基本的 QR 码生成器，它获得一个`Uri`并返回 base 64 编码的图像。

我不会在这里粘贴`AddTwoFactorApp.cshtml`代码，因为它只是显示 QR 代码，并有一个输入供用户从她的设备插入代码。关于后面的代码，我们可以看一下其中的一些部分。

`Pages/Account/AddTwoFactorApp.cshtml`

```
public class AddTwoFactorAppModel : PageModel
{
    // ...

    public string SharedKey { get; set; }

    public string QrCode { get; set; }

    [TempData] public string[] RecoveryCodes { get; set; }

    [TempData] public string StatusMessage { get; set; }

    [BindProperty] public InputModel Input { get; set; }

    public class InputModel
    {
        [Required]
        [StringLength(7, ErrorMessage = "The {0} must be at least {2} and at max {1} characters long.",
            MinimumLength = 6)]
        [DataType(DataType.Text)]
        [Display(Name = "Verification Code")]
        public string Code { get; set; }
    }

    public async Task<IActionResult> OnGetAsync()
    {
        var user = await _userManager.GetUserAsync(User);
        if (user == null)
        {
            return NotFound($"Unable to load user with ID '{_userManager.GetUserId(User)}'.");
        }

        await LoadSharedKeyAndQrCodeUriAsync(user);

        return Page();
    }

    // ...

    private async Task LoadSharedKeyAndQrCodeUriAsync(PlayBallUser user)
    {
        var unformattedKey = await _userManager.GetAuthenticatorKeyAsync(user);
        if (string.IsNullOrEmpty(unformattedKey))
        {
            await _userManager.ResetAuthenticatorKeyAsync(user);
            unformattedKey = await _userManager.GetAuthenticatorKeyAsync(user);
        }

        SharedKey = FormatKey(unformattedKey);
        var email = await _userManager.GetEmailAsync(user);
        QrCode = _qrCodeGenerator.Generate(GenerateQrCodeUri(email, unformattedKey));
    }

    private string FormatKey(string unformattedKey)
    {
        // makes a simple formatting of the key, to make it simpler for the user to copy it
        // ...
    }

    private Uri GenerateQrCodeUri(string email, string unformattedKey)
    {
        const string authenticatorUriFormat = "otpauth://totp/{0}:{1}?secret={2}&issuer={0}&digits=6";

        return new Uri(
            string.Format(
                authenticatorUriFormat,
                _urlEncoder.Encode("PlayBall"),
                _urlEncoder.Encode(email),
                unformattedKey));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 GET 请求中，我们利用`UserManager`来获取共享的双因子密钥，然后格式化密钥以供显示，并为其创建 Qr 代码。

`Pages/Account/AddTwoFactorApp.cshtml`

```
public class AddTwoFactorAppModel : PageModel
{
    // ...

    public async Task<IActionResult> OnPostAsync()
    {
        // get and verify user exists...
        // verify model state is valid...
        // ...

        var is2faTokenValid = await _userManager.VerifyTwoFactorTokenAsync(
            user, _userManager.Options.Tokens.AuthenticatorTokenProvider, verificationCode);

        if (!is2faTokenValid)
        {
            ModelState.AddModelError("Input.Code", "Verification code is invalid.");
            await LoadSharedKeyAndQrCodeUriAsync(user);
            return Page();
        }

        await _userManager.SetTwoFactorEnabledAsync(user, true);
        var userId = await _userManager.GetUserIdAsync(user);
        _logger.LogInformation("User with ID '{UserId}' has enabled 2FA with an authenticator app.", userId);

        StatusMessage = "Your authenticator app has been verified.";

        if (await _userManager.CountRecoveryCodesAsync(user) == 0)
        {
            var recoveryCodes = await _userManager.GenerateNewTwoFactorRecoveryCodesAsync(user, 10);
            RecoveryCodes = recoveryCodes.ToArray();
            return RedirectToPage("./ShowRecoveryCodes");
        }
        else
        {
            return RedirectToPage("./ManageTwoFactor");
        }
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在 POST 请求中，我们再次使用`UserManager`验证插入的代码。如果代码有效，我们将 2fa 设置为 enabled，如果还没有生成任何恢复代码，我们将生成代码并将它们放在临时数据属性中，然后将用户重定向到`ShowRecoveryCodes`页面以查看它们。

### 使用双因素认证登录

有了双因素身份认证设置，现在当用户登录时，我们必须确保他在此过程中从他的设备之一插入代码。

在`Login`页面上，已经准备好了到`LoginWithTwoFactor`页面的重定向，但是基本上唯一需要的是:

`Pages/LoginWithTwoFactor.cshtml.cs`

```
public async Task<IActionResult> OnPostAsync(string returnUrl = null)
{
    // validate model...
    // sign in with password...
    // ...
    if (signInResult.RequiresTwoFactor)
    {
        return RedirectToPage("./LoginWithTwoFactor", new { ReturnUrl = returnUrl, RememberMe = Input.RememberMe });
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

`LoginWithTwoFactor`是一个简单的页面，带有一个表单，供用户介绍其设备的代码。在代码隐藏方面，逻辑类似于我们在`Login.cshtml.cs`中看到的，主要区别在于我们用来验证数据的`SignInManager`方法:

`Pages/LoginWithTwoFactor.cshtml.cs`

```
var result = await _signInManager.TwoFactorAuthenticatorSignInAsync(authenticatorCode, rememberMe, Input.RememberMachine); 
```

Enter fullscreen mode Exit fullscreen mode

`LoginWithRecoveryCode`页面基本相同，但是再次使用了不同的`SignInManager`方法:

`Pages/LoginWithRecoveryCode.cshtml.cs`

```
var result = await _signInManager.TwoFactorRecoveryCodeSignInAsync(recoveryCode); 
```

Enter fullscreen mode Exit fullscreen mode

至此，我想我们已经了解了双因素身份认证实施的主要部分。还是那句话，如果看 GitHub 上的代码有什么不对的地方，戳我！

## 用 LibMan 添加客户端库

在 PlayBall 的主前端，我们使用 [npm](https://www.npmjs.com/) 来管理依赖性。在 auth 服务中，到目前为止我们没有使用任何东西，因为到目前为止我们还不需要 JavaScript，CSS 已经被完全遗忘了。

现在我们将改变这一点，并使用 [Bootstrap](https://getbootstrap.com/) 来尝试使我们的页面不那么可怕。我知道 Bootstrap 并不是事实上的标准，但我只是希望页面更有用，不要在 CSS 上花太多时间，因为这远不是我的最佳技能😛。

我们可以直接从 CDN 导入 Bootstrap，但是这样我们就看不到 [LibMan](https://docs.microsoft.com/en-us/aspnet/core/client-side/libman/libman-cli?view=aspnetcore-2.2) 在工作。

LibMan(图书馆管理员的简称)是一个简单的。NET 命令行工具，我们可以用它来管理前端库。

我们可以用下面的命令安装 LibMan:

```
dotnet tool install -g Microsoft.Web.LibraryManager.Cli 
```

Enter fullscreen mode Exit fullscreen mode

然后在我们的 web 项目的文件夹中，我们可以运行`libman init`来初始化它。它会要求我们提供默认的提供者，如果我们不想指定的话，它将是 cdnjs。结果是一个带有一些元数据的`json`文件:

```
{  "version":  "1.0",  "defaultProvider":  "cdnjs",  "libraries":  []  } 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想让库默认转到一个特定的文件夹，我们可以做`libman init --default-destination wwwroot/lib`，这给我们:

```
{  "version":  "1.0",  "defaultProvider":  "cdnjs",  "defaultDestination":  "wwwroot/lib",  "libraries":  []  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们想安装引导程序，所以我们做`libman install twitter-bootstrap`并得到:

```
{  "version":  "1.0",  "defaultProvider":  "cdnjs",  "defaultDestination":  "wwwroot/lib",  "libraries":  [  {  "library":  "twitter-bootstrap@4.2.1"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以在安装库时指定提供者和目标路径，但是因为我们已经设置了默认值，所以不需要这样做。

在下一节中，为页面创建一个基本布局，我们可以包含一个到我们刚刚安装的引导资产的链接。

LibMan 有比我在这里展示的更多的选项，但这是我到目前为止所需要的全部([查看文档了解更多信息](https://docs.microsoft.com/en-us/aspnet/core/client-side/libman/libman-cli?view=aspnetcore-2.2))。虽然它没有很多选择，因为它应该是非常简单的。如果我们需要更多的复杂性，就不缺乏替代方案。

## 向页面添加基本布局

为了给所有页面添加一个基本布局，我们可以从创建一个`Shared`文件夹作为`Pages`的子文件夹开始。在那里我们创建了一个新的`_Layout.cshtml`文件(它是模板的一部分)。在新文件中，我们可以添加如下内容:

`Pages/Shared/_Layout.cshtml`

```
<!DOCTYPE html>
<html>
<head>
    @ViewData["Title"]
    <environment include="Development">
        <link rel="stylesheet" href="~/lib/css/bootstrap.css"/>    
    </environment>
    <environment exclude="Development">
        <link rel="stylesheet" href="~/lib/css/bootstrap.min.css"/>    
    </environment>
</head>
<body>
<div class="container">
    @RenderBody()
</div>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

大部分是正常的`HTML`,有几个剃刀特征:

*   标签助手，它允许我们根据应用程序当前的执行环境来呈现页面的特定部分。
*   `@RenderBody()`方法调用，我们将它放在我们希望呈现所请求页面内容的地方。

现在要使用布局，我们可以到每一页并在页眉中配置它，但这将是大量的工作，我们最终会忘记一些东西。相反，我们可以在`Pages`文件夹的根目录下创建一个`_ViewStart.cshtml`文件，内容如下:

`Pages/_ViewStart.cshtml`

```
@{
    Layout = "_Layout";
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，该文件将由该文件夹中的所有其他视图(及其后代)执行，使它们都使用我们创建的基本布局。

然而在`Account`子文件夹中，我们想要一个稍微不同的布局，允许用户注销。为了实现这一点，我们可以遵循一个非常相似的方法。创建另一个`Shared`文件夹，这一次作为`Account`的后代，并在其中创建另一个布局文件(这一次我将其命名为`_Account.cshtml`)。在这个新文件中，我们可以添加如下内容:

`Pages/Shared/_Account.cshtml`

```
@{
    Layout = "_Layout";
}
<form asp-page="/Logout" method="post">
    <button type="submit" class="btn btn-secondary">Logout</button>
</form>
<div class="container">
    @RenderBody()
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这使得这个布局继承了原来的布局，同时添加了一个注销按钮。在`Account`文件夹的根目录中，我们现在可以创建另一个`_ViewStart.cshtml`文件，包含:

`Pages/Account/_ViewStart.cshtml`

```
@{
    Layout = "Shared/_Account";
} 
```

Enter fullscreen mode Exit fullscreen mode

## 为状态消息添加局部视图

为了总结这篇文章，让我们来看看如何创建局部视图。

还记得我们放在大多数页面顶部的状态信息吗，在上一集我只是把它放在了一个`div`里面？好吧，我们把它放到局部视图中，给它一个更好的 UI。

在`Pages/Account`文件夹中，我们可以添加一个名为`_StatusMessage.cshtml`的新局部视图(有一个文件模板)。在那里我们有以下内容:

`Pages/Account/_StatusMessage.cshtml`

```
@model string

@if (!String.IsNullOrEmpty(Model))
{
    var statusMessageClass = Model.StartsWith("Error") ? "danger" : "success";
    <div class="alert alert-@statusMessageClass alert-dismissible" role="alert">
        <button type="button" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        @Model
    </div>
} 
```

Enter fullscreen mode Exit fullscreen mode

该模型是一个单一的`string`，因为它是我们现在所需要的。然后，如果我们有一个填充的模型(如果没有，我们不会渲染任何东西)，我们显示消息，用一些引导类装饰，加上一个按钮来消除消息。

现在，为了使用局部视图，在每一页上，我们都可以用下面的内容替换带有消息的`div`

```
<partial name="_StatusMessage" for="StatusMessage" /> 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

这是它的这个职位对 ASP.NET 核心身份和剃刀页。我们看了一下实现认证流的一些最有趣的部分(当然是从我的角度)。同样，不要忘记 ASP.NET 核心身份已经自带了大部分现成的，所以只有在您需要定制它时才会遇到这种麻烦。

帖子中的链接:

*   [ASP.NET 核心剃刀页](https://docs.microsoft.com/en-us/aspnet/core/razor-pages/?view=aspnetcore-2.2)
*   [ASP.NET 核心身份](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-2.2)
*   [基于时间的一次性密码算法](https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm)
*   [中文字幕 ocr:贾宝玉字幕校对:贾宝玉](https://github.com/guitarrapc/SkiaSharp.QrCode)
*   [利伯曼(图书馆经理)](https://docs.microsoft.com/en-us/aspnet/core/client-side/libman/libman-cli?view=aspnetcore-2.2)
*   [npm](https://www.npmjs.com/)
*   [自举](https://getbootstrap.com/)

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode017)。

最后一点，当我录制视频的时候没有那么多，但写完帖子后，这感觉有点到处都是，太多的话题浓缩在一个视频/帖子中。我会努力想出一个更好的组织策略🙂

鉴于我正在实现一个完整的应用程序，我想在一集里浓缩几个主题是正常的，但我不完全确定阅读体验是否很棒，所以如果你有建议，请告诉我。

感谢分享和反馈！

谢谢你的来访，西阿兹！