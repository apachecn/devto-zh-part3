# 使用 Polly 为我的 http 调用增加弹性

> 原文：<https://dev.to/andrewcahill/adding-resilience-to-my-http-calls-using-polly-2o6>

当我在 GitHub 中构建我的 my revie([https://github.com/andrewcahill/myreverie](https://github.com/andrewcahill/myreverie))项目时，我想给我的 http 调用增加一些弹性。我听说过 Polly 框架，它使这一点变得非常容易。

Polly 是一个开源框架，它提供了对瞬时错误的恢复能力，您可以在自己的应用程序中利用这种能力，从而消除了自己手动编写代码的负担。能力类型包括重试、断路器、回退等。

如果您尝试自己编写此代码，要正确完成它可能会有点棘手，并且容易出错，因为随着时间的推移可能会出现不一致，因为这些是跨领域的问题，应该将其抽象到一个库中以供重用，所以为什么要重新发明轮子呢！

您可能要考虑的弹性类型可能是针对网络中断或系统忙于其他工作，这在较大的微服务应用中尤其重要，这些应用严重依赖于服务的相互通信，因此，与其超时或返回错误，您不希望 it 部门重试并更优雅、更智能地处理这种情况吗？

出于我的目的，我想简单地添加一个功能，如果端点由于某种原因不可用，它将“重试”一定次数，而不仅仅是在第一次尝试后失败。这是一个简单的用例，但它让我很容易熟悉这个框架。

让 Polly 开始运行是非常简单的。

在我的启动类中，我定义了 http 客户端

```
services.AddHttpClient("api").AddTransientHttpErrorPolicy(p => p.RetryAsync(5)); 
```

Enter fullscreen mode Exit fullscreen mode

这实际上意味着在返回失败响应之前，要重试 http 调用五次。我选择 5 作为重试的合理次数，但是你可以选择任何你想要的次数。

我当然需要添加 Polly 包(在我的例子中，我使用的是 v2.2.0):

```
Microsoft.Extensions.Http.Polly 
```

Enter fullscreen mode Exit fullscreen mode

添加然后添加 using 语句

```
using Polly; 
```

Enter fullscreen mode Exit fullscreen mode

在我的 web 项目的服务类中，我对 API 进行 http 调用，并使用 Dependancy 注入‘IHttpClientFactory’。Net Core 作为构造函数参数。

```
IHttpClientFactory httpClientFactory 
```

Enter fullscreen mode Exit fullscreen mode

然后，我将这个参数赋给构造函数中的一个全局变量' _httpClientFactory '，如下所示

```
_httpClientFactory = httpClientFactory; 
```

Enter fullscreen mode Exit fullscreen mode

我将一个新的 HttpClient 对象初始化为由
在启动类中定义的客户机

```
HttpClient client = _httpClientFactory.CreateClient("api"); 
```

Enter fullscreen mode Exit fullscreen mode

之后，我像平常一样进行 http 调用，但是这次使用 Polly。

我的情况是:

```
await client.GetStringAsync(_appSettings.GoalsUrl); 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果这个 http 调用失败，它将在返回到调用者之前再尝试四次，从而提高我的系统在间歇性连接问题的情况下的弹性！。

帖子[使用 Polly](https://aviddeveloper.com/adding-resilience-to-http-calls-with-polly/) 为我的 http 调用增加弹性最先出现在 [Avid Developer](https://aviddeveloper.com) 上。