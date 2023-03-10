# 扩展 Saturn 以支持基本认证

> 原文：<https://dev.to/azure/extending-saturn-to-support-basic-authentication-3ip1>

最近，我需要为一个项目的本地开发创建一个模拟 API，我决定使用 [Saturn](https://saturnframework.org/) ，它是这样描述自己的:

> 一个关注开发人员生产力、性能和可维护性的现代 web 框架

Saturn 是用 F#写的，如果整个项目都是 F#的话，这似乎是一个合理的选择。这里有一个[入门](https://saturnframework.org/docs/guides/how-to-start/)指南，所以我就不赘述了，相反我会把重点放在这个项目特别需要的东西上，基本认证，因为我嘲笑的 API 在幕后使用了它，我想模拟它。

## 扩展土星应用

从概念上讲，Saturn 使用[计算表达式](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/computation-expressions?WT.mc_id=devto-blog-aapowell)抽象出 ASP.NET 管道，给你一个非常清晰的 F#语法来定义你的应用。

我想让应用程序定义像这样工作:

```
let app = application {
    use_basic_auth
    // the rest of our app setup
    url (sprintf "http://0.0.0.0:%d/" port) } 
```

Enter fullscreen mode Exit fullscreen mode

为此，我们需要在星期六`ApplicationBuilder`创建一个[自定义操作](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/computation-expressions?WT.mc_id=aaronpowell-blog-aapowell#custom-operations)。谢天谢地，F#使得扩展你不拥有的类型变得非常容易，所以让我们开始吧:

```
type ApplicationBuilder with
    [<CustomOperationAttribute("use_basic_auth")>]
    member __.UseBasicAuth(state : ApplicationState) =
        state 
```

Enter fullscreen mode Exit fullscreen mode

我们将在`application`计算表达式上定义我们的新属性，并将其命名为`use_basic_auth`，它将执行已定义的函数，该函数的签名为`ApplicationState -> ApplicationState`。

### 添加中间件

我们要做的第一件事是编辑 Saturn 使用的中间件，以包含身份验证，由于它是 ASP.NET 核心，我们需要[添加它的身份中间件](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-2.2&tabs=visual-studio&WT.mc_id=devto-blog-aapowell)。让我们更新我们的`UserBasicAuth`函数:

```
type ApplicationBuilder with
    [<CustomOperationAttribute("use_basic_auth")>]
    member __.UseBasicAuth state =
        let middleware (app : IApplicationBuilder) =
            app.UseAuthentication()

        { state with
            AppConfigs = middleware::state.AppConfigs } 
```

Enter fullscreen mode Exit fullscreen mode

那很容易！我们添加了一个名为`middleware`的新函数，它将认证中间件添加到管道中。然后，我们将使用`::` List 函数将我们的中间件添加到中间件集合的`head`中，并使用当前的`ApplicationState`创建一个新的记录类型，只需更新`AppConfigs`属性。

### 实现基本认证

在我们的管道中启用了身份验证之后，我们接下来需要告诉它我们想要使用哪种身份验证，以及如何实际处理它！

```
type ApplicationBuilder with
    [<CustomOperationAttribute("use_basic_auth")>]
    member __.UseBasicAuth state =
        let middleware (app : IApplicationBuilder) =
            app.UseAuthentication()

        let service (s : IServiceCollection) =
            s.AddAuthentication("BasicAuthentication")
                .AddScheme<AuthenticationSchemeOptions, BasicAuthHandler>("BasicAuthentication", null)
                |> ignore

            s.AddTransient<IUserService, UserService>() |> ignore
            s

        { state with
            ServicesConfig = service::state.ServicesConfig
            AppConfigs = middleware::state.AppConfigs } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个`service`函数，它接受`IServiceCollection`，添加认证服务作为`BasicAuthentication`(因此管道知道它是那个类型)，添加处理程序(一个称为`BasicAuthHandler`的类型)，并且还在依赖注入框架中注册了一个类型来访问我们的用户。然后，我们用这个新函数修改我们的记录类型，一切就绪！

#### 实现基本认证处理程序

好了，我们还没有*完全*完成，我们应该看看我们如何在`BasicAuthHandler`类型中实际实现基本的认证处理器，以及我们的用户存储。

让我们从用户存储开始，因为我已经做得很简单了，毕竟，这是一个模拟:

```
type IUserService =
    abstract member AuthenticateAsync : string -> string -> Async<bool>

type UserService() =
    let users = [("aaron", "password")] |> Map.ofList

    interface IUserService with
        member __.AuthenticateAsync username password =
            async {
                return match users.TryGetValue username with
                       | (true, user) when user = password -> true
                       | _ -> false } 
```

Enter fullscreen mode Exit fullscreen mode

是的，这里没有什么精彩的，我只是创建了一个类型，在内存中测试用户和密码。在非模拟系统中，您可能希望更安全地实现它，但它确实满足了我现在的需求。😉我还将它创建为一个接口，这样我可以向下注入它，或者如果我要编写测试，我可以模仿它( *Narator:他没有编写测试*)。

既然我们已经有了一种方法来验证用户的凭证是否有效，那么是时候实现处理认证的类了，`BasicAuthHander` :

```
type BasicAuthHandler(options, logger, encoder, clock, userService : IUserService) =
    inherit AuthenticationHandler<AuthenticationSchemeOptions>(options, logger, encoder, clock) 
```

Enter fullscreen mode Exit fullscreen mode

这种类型从 ASP.NET 核心框架中的`AuthenticationHandler`继承而来，并且需要我们实现`HandleAuthenticateAsync`函数才是有用的，所以让我们从这里开始:

```
type BasicAuthHandler(options, logger, encoder, clock, userService : IUserService) =
    inherit AuthenticationHandler<AuthenticationSchemeOptions>(options, logger, encoder, clock)

    override this.HandleAuthenticateAsync() =
        task { return AuthenticateResult.Fail "Not Implemented" } 
```

Enter fullscreen mode Exit fullscreen mode

*补充说明:我使用 TaskBuilder.fs 包来创建一个使用`task`计算表达式的`Task<T>`响应。*

作为中间件管道的一部分，这个函数在每个请求上执行，我需要确保提供了`Authorization`头，并且其中有一个有效的基本 Auth 令牌。让我们首先确保头部存在一个`match`表达式:

```
override this.HandleAuthenticateAsync() =
    let request = this.Request
    match request.Headers.TryGetValue "Authorization" with
    | (true, headerValue) ->
        task { return AuthenticateResult.Fail("Not implemented") }
    | (false, _) ->
        task { return AuthenticateResult.Fail("Missing Authorization Header") } 
```

Enter fullscreen mode Exit fullscreen mode

模式匹配将检查我们是否有报头，如果报头存在，则进入适当的块，如果不存在，我们将挑战失败，并产生一个`401`响应。

为了验证这个令牌，我将从一个快速的函数开始解包，如下所示:

```
type Credentials =
     { Username: string
       Password: string }

let getCreds headerValue =
    let value = AuthenticationHeaderValue.Parse headerValue
    let bytes = Convert.FromBase64String value.Parameter
    let creds = (Encoding.UTF8.GetString bytes).Split([|':'|])

    { Username = creds.[0]
      Password = creds.[1] } 
```

Enter fullscreen mode Exit fullscreen mode

这将把编码后的字符串解码成一个`username:password`对，我将其作为记录返回(您可以使用匿名记录类型或元组，完全由您决定)。现在我们可以用我们的`IUserService` :
来验证它

```
override this.HandleAuthenticateAsync() =
    let request = this.Request
    match request.Headers.TryGetValue "Authorization" with
    | (true, headerValue) ->
        async {
        let creds = getCreds headerValue.[0]

        let! userFound = userService.AuthenticateAsync creds.Username creds.Password

        return match userFound with
                | true ->
                    let claims = [| Claim(ClaimTypes.NameIdentifier, creds.Username); Claim(ClaimTypes.Name, creds.Username) |]
                    let identity = ClaimsIdentity(claims, this.Scheme.Name)
                    let principal = ClaimsPrincipal identity
                    let ticket = AuthenticationTicket(principal, this.Scheme.Name)
                    AuthenticateResult.Success ticket
                | false ->
                    AuthenticateResult.Fail("Invalid Username or Password") }
        |> Async.StartAsTask
    | (false, _) ->
        task { return AuthenticateResult.Fail("Missing Authorization Header") } 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用另一个`match`来反对我们的`IUserService.AuthenticateAsync`的结果(它使用 F# `async`)，如果用户是有效的，我们将创建一个声明票，并成功地将它返回给管道以请求继续。

## 用我们的`router`接线

现在是时候在我们想要进行身份验证的路由上添加身份验证了，我们使用`router`计算表达式来完成。我们将从管道开始:

```
let matchUpUsers : HttpHandler = fun next ctx -> next ctx

let authPipeline = pipeline {
    requires_authentication (Giraffe.Auth.challenge "BasicAuthentication")
    plug matchUpUsers } 
```

Enter fullscreen mode Exit fullscreen mode

将`pipeline`的`requires_authentication`属性设置为来自[长颈鹿](https://github.com/giraffe-fsharp/Giraffe)的`BasicAuthentication`挑战(土星构建于其上的网络框架)。

最后，我们的`router` :
时间到了

```
let webApp = router {
    pipe_through authPipeline
    // define routes
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

Saturn 的计算表达式设计非常简洁，事实上，您只需扩展表示您想要扩展的 Saturn 部分的类型。通过这种方式，我们可以非常容易地添加一个定制的身份验证提供者。

希望这能帮助其他人扩展土星。😊