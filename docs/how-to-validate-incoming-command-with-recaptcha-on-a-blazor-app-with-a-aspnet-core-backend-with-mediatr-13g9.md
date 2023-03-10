# 如何在 Blazor 应用程序上使用 reCaptcha 验证传入的命令

> 原文：<https://dev.to/rembou1/how-to-validate-incoming-command-with-recaptcha-on-a-blazor-app-with-a-aspnet-core-backend-with-mediatr-13g9>

Toss 项目是一个留言板:人们注册后发送信息。这种应用程序的威胁之一是机器人:人们创建程序，创建新的帐户，然后张贴垃圾邮件。

reCaptcha 是 Google 很久以前发明的一个工具，旨在验证一个给定的动作是否仅由人类完成。在这篇博文中，我们将看到我是如何集成这个工具的，这样我就可以很容易地验证一个给定的动作是由一个人来完成的。

## repatcha 如何工作

Recaptcha 是这样工作的:

*   给定的动作是在你的应用上完成的
*   您调用他们的 JS api 来获取令牌，如果用户不是人类(或者至少 reCaptcha 不认为它是机器人)，这个方法将失败。我们完全忽略它是如何做到的，但我相信谷歌在这项任务上比我和我每周在这个项目上的 2 小时工作更有效率。
*   我们将令牌发送到后端
*   后端将这个令牌和我们的私钥一起发送给 Google reCaptcha API
*   如果令牌是正确的，API 返回“成功”
*   如果不是，那么它返回一个错误，我们可以停止处理查询

## 获取令牌

首先我们需要添加 reCaptcha 脚本链接:

```
<script src='https://www.google.com/recaptcha/api.js?render=6LcySnsUAAAAAKFZn_ve4gTT5kr71EXVkQ_QsGot'></script> 
```

*   您可以在 reCaptcha GUI 上找到它
*   很遗憾谷歌没有提供一个具有完整性属性的版本，所以我们确信这个脚本不会被黑客修改

为了获得受保护动作的令牌，我是这样做的:

每个合理命令都继承自“NotARobot”类:

```
public class NotARobot
{
    public string Token { get; set; }
}
public class RegisterCommand : NotARobot, IRequest<CommandResult>
{
    [Required]
    [EmailAddress]
    [Display(Name = "Email")]
    public string Email { get; set; }

    [Required]
    [StringLength(100, ErrorMessage = "The {0} must be at least {2} and at max {1} characters long.", MinimumLength = 6)]
    public string Name { get; set; }

    [Required]
    [StringLength(100,ErrorMessage = "The {0} must be at least {2} and at max {1} characters long.", MinimumLength = 6)]
    [DataType(DataType.Password)]
    [Display(Name = "Password")]
    public string Password { get; set; }

    [DataType(DataType.Password)]
    [Display(Name = "Confirm password")]
    [Compare("Password", ErrorMessage = "The password and confirmation password do not match.")]
    public string ConfirmPassword { get; set; }
} 
```

*   这个类是在我的共享程序集中定义的，所以我也能够在服务器端读取它

每次我发送一个继承自 NotARobot 的命令，我都会收集一个令牌。这是在我的 HttpClient 扩展上完成的

```
 public async Task Post<T>(T data)
        {
            if (data is NotARobot)
            {
                (data as NotARobot).Token =  await JSRuntime.Current.InvokeAsync<string>("runCaptcha",this._uri);
            }
            await ExecuteHttpQuery(async () =>
            {
                var requestJson = Json.Serialize(data);
                return await _httpClient.SendAsync(await PrepareMessageAsync(new HttpRequestMessage(HttpMethod.Post, _uri)
                {
                    Content = new StringContent(requestJson, System.Text.Encoding.UTF8, "application/json")
                }));
            });
        } 
```

用 interop 调用的 js 方法定义如下:

```
runCaptcha = function (actionName) {
    return new Promise((resolve, reject) => {
        grecaptcha.ready(function () {
            grecaptcha.execute('6LcySnsUAAAAAKFZn_ve4gTT5kr71EXVkQ_QsGot', { action: actionName })
                .then(function (token) {
                    resolve(token);
                });
        });

    });
}; 
```

*   第一个参数是 Google 给出的公钥，这里是硬编码的，因为它是公开的，只在一个地方使用
*   我使用 Promise 是因为这是在 JSInterop 中处理异步执行的方式
*   这样做的话，我只需要在我的命令中添加 NotARobot，整个过程就会完成

现在我的服务器收到了令牌，我需要用 Google API 添加验证。

## 使用 MediatR 验证令牌

我们可以在前面的源代码中看到，我的类 RegisterCommand 也实现了 MediatR 的 IRequest 接口。该命令由实现 IRequestHandler 的句柄执行。MediatR 提供了一种在处理程序执行之前添加验证的简单方法:[管道](https://github.com/jbogard/MediatR/wiki/Behaviors)。

这是我的管道代码

```
public class CaptchaMediatRAdapter<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
        where TRequest : NotARobot, IRequest<TResponse>
{
    private ICaptchaValidator captchaValidator;

    public CaptchaMediatRAdapter(ICaptchaValidator captchaValidator)
    {
        this.captchaValidator = captchaValidator;
    }

    public async Task<TResponse> Handle(TRequest request, CancellationToken cancellationToken, RequestHandlerDelegate<TResponse> next)
    {
        await this.captchaValidator.Check(((NotARobot)request).Token);
        return await next();
    }
} 
```

非常简单，它只是调用我的验证器，这个验证器应该在验证失败时抛出一个异常。这是我的验证器

```
public class CaptchaValidator : ICaptchaValidator
{
    private string _secret;
    private readonly IHttpClientFactory httpClientFactory;
    private readonly IHttpContextAccessor httpContextAccessor;

    public CaptchaValidator(string secret, IHttpClientFactory httpClientFactory, IHttpContextAccessor httpContextAccessor)
    {
        _secret = secret;
        this.httpClientFactory = httpClientFactory;
        this.httpContextAccessor = httpContextAccessor;
    }

    public async Task Check(string token)
    {
        var webClient = this.httpClientFactory.CreateClient();
        var content = new FormUrlEncodedContent(new[]
        {
            new KeyValuePair<string, string>("secret", _secret),
            new KeyValuePair<string, string>("response", token),
            new KeyValuePair<string, string>("remoteip", httpContextAccessor.HttpContext.Connection.RemoteIpAddress.ToString())
        });
        var res = await webClient.PostAsync("https://www.google.com/recaptcha/api/siteverify",content);
        var resJson = JObject.Parse(await res.Content.ReadAsStringAsync());
        var succeed = resJson["success"].ToString() == "True";
        if (!succeed)
            throw new InvalidOperationException("Captcha validation failed : " + string.Join(",", resJson["error-codes"].AsEnumerable()));
    }
} 
```

*   我们需要发送客户端 IP
*   秘密是一个...secret 在 my secrets.json 中定义，您可以在 reCaptcha GUI 中找到它
*   这里我只是解析产生的 json，看看令牌是否正确，如果不正确，我就抛出一个异常。我可以返回一个布尔值，但这种情况不应该发生，因为我只希望我的应用程序上有人。

对于注入此服务，我在我的配置服务上也是这样做的

```
services.AddHttpClient();
services.AddSingleton<ICaptchaValidator>(s => new CaptchaValidator(
          Configuration["GoogleCaptchaSecret"],                    
          s.GetRequiredService<IHttpClientFactory>(),
          s.GetRequiredService<IHttpContextAccessor>())); 
```

*   这是一个单一的，因为没有国家
*   不要忘记初始化 HttpClient，否则 IHttpClientFactory 注入将会失败

我还需要注入我的管道，我不知道为什么联发科 ASPNET 核心扩展默认不这样做

```
services.AddScoped(typeof(IPipelineBehavior<, >), typeof(CaptchaMediatRAdapter<,>)); 
```

现在一切都启动了:

*   在客户端检测到一个 NotARobot，我们试图获取一个令牌(如果用户不是人类，这将失败)
*   令牌在命令有效负载上发送
*   如果命令从 NotARobot 继承，MediatR 将执行管道
*   如果令牌不正确，验证将失败
*   如果 reCaptcha 不认为它是机器人，我的命令就会被执行。

## E2E 测试

现在我的 E2E 测试失败了，因为 Selenium WebDriver 是一个机器人:(。

我需要改变两个验证过程:在客户端和服务器端。

在客户端，我的测试执行以下脚本

```
if (Browser is IJavaScriptExecutor)
{
    //in E2E test we disable getting the token from recaptcha
    ((IJavaScriptExecutor)Browser).ExecuteScript("runCaptcha = function(actionName) { return Promise.resolve('test'); }");
} 
```

*   js 函数被覆盖，并在每次执行时返回一个“test”标记

在服务器端，我需要注入一个我的验证器的假实现

```
if (Configuration.GetValue<string>("test") != null)
{               
    services.AddSingleton<ICaptchaValidator, FakeCaptchaValidator>();
} 
```

```
public class FakeCaptchaValidator : ICaptchaValidator
    {
        public bool NextResult { get; set; } = true;
        public Task Check(string token)
        {
            if (!NextResult)
                throw new InvalidOperationException();
            return Task.CompletedTask;
        }
    } 
```

*   因为当我执行我的服务器进行 E2E 测试时，我发送了“-test true”，所以良好的实现被注入。
*   在我的 FakeCaptchaValidator 中使用 NextResult，我可以在验证码验证失败时验证我的流程失败

## 结论

在本文中，我们再次看到了 Blazor 的两个主要优势:客户机和服务器之间的代码共享以及 js 互操作性，这使得您可以使用任何现有的 javascript api。

## 参考文献

*   [https://github.com/jbogard/MediatR/wiki/Behaviors](https://github.com/jbogard/MediatR/wiki/Behaviors)
*   [https://www.google.com/recaptcha/intro/v3.html](https://www.google.com/recaptcha/intro/v3.html)