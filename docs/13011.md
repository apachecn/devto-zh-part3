# 具有 HttpClientFactory 的 API 速率限制 HTTP 处理程序

> 原文：<https://dev.to/sebnilsson/api-rate-limit-http-handler-with-httpclientfactory-3o36>

大多数 API 都有某种速率限制。例如， [GitHub 的限制是](https://developer.github.com/v3/#rate-limiting)每小时 5000 个请求。作为 API 的消费者，这可以通过对 API 的请求进行计时或通过缓存结果来限制您的使用来解决。

当一个 API **限制你的每秒请求数**时会怎样？这可能是您希望**在代码的中心位置**处理的事情，而不是分散到您对 API 进行 HTTP 调用的所有地方。

[![Funnel](img/f964e55c867503c23a174320713d028d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ob4WkIbQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.sebnilsson.com/wimg/ratelimithttpmessagehandler/water-funnel.jpg)

对我来说，解决方案是在 [`HttpClientFactory`](https://docs.microsoft.com/en-us/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 的设置中添加一个[外发请求中间件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/http-requests?view=aspnetcore-2.2#outgoing-request-middleware)。

这样，我就可以配置启动服务来使用这个带有`HttpClientFactory` :
的`RateLimitHttpMessageHandler`类

```
services
    .AddHttpClient<IApi, Api>()
    .AddHttpMessageHandler(() =>
        new RateLimitHttpMessageHandler(
            limitCount: 5,
            limitTime: TimeSpan.FromSeconds(1)))
    .AddDefaultTransientHttpErrorPolicy(); 
```

这确保了无论我在哪里使用类`IApi`，通过依赖注入，它都会将对 API 的调用限制在每秒 5 次。

`RateLimitHttpMessageHandler` :
的简化版代码

```
public class RateLimitHttpMessageHandler : DelegatingHandler
{
    private readonly List<DateTimeOffset> _callLog =
        new List<DateTimeOffset>();
    private readonly TimeSpan _limitTime;
    private readonly int _limitCount;

    public RateLimitHttpMessageHandler(int limitCount, TimeSpan limitTime)
    {
        _limitCount = limitCount;
        _limitTime = limitTime;
    }

    protected override async Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request,
        CancellationToken cancellationToken)
    {
        var now = DateTimeOffset.UtcNow;

        lock (_callLog)
        {
            _callLog.Add(now);

            while (_callLog.Count > _limitCount)
                _callLog.RemoveAt(0);
        }

        await LimitDelay(now);

        return await base.SendAsync(request, cancellationToken);
    }

    private async Task LimitDelay(DateTimeOffset now)
    {
        if (_callLog.Count < _limitCount)
            return;

        var limit = now.Add(-_limitTime);

        var lastCall = DateTimeOffset.MinValue;
        var shouldLock = false;

        lock (_callLog)
        {
            lastCall = _callLog.FirstOrDefault();
            shouldLock = _callLog.Count(x => x >= limit) >= _limitCount;
        }

        var delayTime = shouldLock && (lastCall > DateTimeOffset.MinValue)
            ? (limit - lastCall)
            : TimeSpan.Zero;

        if (delayTime > TimeSpan.Zero)
            await Task.Delay(delayTime);
    }
} 
```