# 让我们来谈谈健康检查

> 原文：<https://dev.to/douglasmakey/health-checks-386j>

根据这篇[优秀文章](https://docs.microsoft.com/en-us/azure/architecture/patterns/health-endpoint-monitoring)中的 azure 文档，他们声明。

*“监控 web 应用程序和后端服务是一种很好的实践，通常也是一种业务需求，以确保它们可用并正常运行。但是，监控云中运行的服务比监控内部服务更困难。”*

*“有许多因素会影响云托管的应用程序，例如网络延迟、性能、底层计算和存储系统的可用性以及它们之间的网络带宽。这些因素中的任何一个都可能导致服务完全或部分失败。因此，您必须定期验证服务是否正常运行，以确保所需的可用性级别。*

当我们使用部署在容器编排器中的多个微服务时，我们会遇到一个问题，即“如何检测正在运行的微服务实例无法处理请求？”。

## 解

通过向应用程序上的端点发送请求来实现健康监控。应用程序应该执行必要的检查，并返回其状态指示。如果一切正常，则“健康检查”通常返回 200，如果服务失败，则返回 503。

这将是健康检查的基本介绍。

## 什么是健康检查？

健康检查基本上是由微服务(例如 HTTP /health)提供的端点，用于检查服务是否正常运行。

## 为什么要用健康检查？

所有微服务都应该执行健康检查。在健康检查失败的情况下，编排工具可以使用这些检查“作为 K8s”来终止实例或向监控工具发出警报。

## 健康体检可以查什么？

一切都将取决于我们的服务或我们的要求是什么。

例如，如果我们的服务使用 PostgreSQL 持久化数据或使用 Redis 进行缓存，我们需要确保我们的服务可以“作为 PostgreSQL 或 Redis”与我们的存储服务进行通信，因为我们的逻辑依赖于这些存储服务。如果我们不能与数据库通信，我们的服务就无法工作。

另一个例子是，如果我们的微服务接收文件，如图像，并将它们存储在磁盘上，我们需要检查磁盘上是否有可用空间。否则，我们的微服务将无法工作。

要检查的最重要的情况是:

*   服务实例使用的基础结构服务的连接状态
*   其他微服务的状态(如果需要)。
*   主机的状态，例如磁盘空间
*   专用逻辑

我见过很多项目只是实现健康检查来返回状态为 200 的响应，而不做任何检查。例如:

```
func HealthCheck(w http.ResponseWriter, r *http.Request) {
    w.WriteHeader(http.StatusOK)
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

***求求你，别这样！*T3】**

运行状况检查是一个强大的盟友，它允许我们做许多事情来检查我们的微服务的行为，如果与一些监控工具结合使用，它可以避免我们头痛。

我将使用我在上一篇文章中编写的服务来寻找像 uber 这样的司机，以实现健康检查的示例。

[跟踪服务](https://dev.to/douglasmakey/tracking-service-with-go-and-redis-43mm)

[追踪服务 V2](https://dev.to/douglasmakey/tracking-service-v2-28fd)

我们需要为 healthcheck 创建一个处理程序，在这个处理程序中，我们将实现我们想要做的所有检查。

如果您已经阅读了我过去的文章，您会看到我的微服务使用 redis 来注册驱动程序的位置，并且它还使用 redis 的命令“georadius”功能来搜索驱动程序，因此我的微服务 100%依赖于 redis，这意味着我需要检查我的微服务是否可以与 redis 通信。

对于这个任务，我使用 redis 的命令 Ping，这个命令用于测试连接是否仍然存在，或者测量延迟。

文件处理程序/健康检查. go

```
func health(w http.ResponseWriter, r *http.Request) {
    if r.Method != "GET" {
        w.WriteHeader(http.StatusMethodNotAllowed)
        return
    }

    // Get instance redis client
    redis := storages.GetRedisClient()
    // Checks that the communication with redis is alive.
    if err := redis.Ping().Err(); err != nil {
        // Put yours logs HERE
        log.Printf("redis unaccessible error: %v ", err)
        w.WriteHeader(http.StatusServiceUnavailable)
    } else {
        w.WriteHeader(http.StatusOK)
    }

    return
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，接下来我们需要添加我们的 healtcheck 路由器。

```
func NewHandler() *http.ServeMux {
    mux := http.NewServeMux()
        // Add healthcheck
    mux.HandleFunc("/health", health)
        // ....
    mux.HandleFunc("/tracking", tracking)
    mux.HandleFunc("/search", search)

    // V2
    mux.HandleFunc("/v2/search", v2.SearchV2)
    mux.HandleFunc("/v2/cancel", v2.CancelRequest)
    return mux
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们用 redis 在 docker 上运行服务和容器。

```
docker run -p 6379:6379 -d redis  
go run main.go 
```

Enter fullscreen mode Exit fullscreen mode

使用 curl 来消费我们的新端点健康，以了解我们的服务是否正常。

```
curl -X GET -I localhost:8000/health                                                                                                              1212:50:15
HTTP/1.1 200 OK
Date: Sat, 12 Jan 2019 15:50:23 GMT
Content-Length: 0 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我们用 redis 停止容器，并尝试点击“/health”，healthcheck 响应应该是 503 服务不可用，因为我们的服务无法与 redis 通信，“因为我们用 Redis 停止了容器。”

```
curl -X GET -I localhost:8000/health                                                                                                              1313:38:24
HTTP/1.1 503 Service Unavailable
Date: Sat, 12 Jan 2019 16:38:37 GMT
Content-Length: 0 
```

Enter fullscreen mode Exit fullscreen mode

对于这个微服务，只需要检查与 redis 的连接，因为这个服务非常简单，但取决于你的微服务，你需要应用不同的检查。

[Github 回购](https://github.com/douglasmakey/tracking)