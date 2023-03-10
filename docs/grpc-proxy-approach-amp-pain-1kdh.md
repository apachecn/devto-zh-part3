# gRPC 代理:方法和难题

> 原文：<https://dev.to/dannypsnl/grpc-proxy-approach-amp-pain-1kdh>

几周前，我们重新讨论了配置设置问题；我们发现生成一个新的有效配置对任何人来说都太难了。你必须记住所有的领域，我们需要在这个时候，有些重复，有些会改变环境，所有的东西成为一个设置的噩梦！

因此，我们开始一个史诗来简化配置定义和生成；其中一项任务是为配置控制创建 CLI。我们必须让客户可以通过这个 CLI 工具上传或下载他们的配置。我们有一些用于内部交换配置的 gRPC 服务，所以我们想重用它们，并且我们已经有一个用于安全过滤连接的 api 网关，所以我们也想基于它。然后我开始研究 gRPC 代理服务器。

我们找到的东西是:[https://github.com/mwitkow/grpc-proxy](https://github.com/mwitkow/grpc-proxy)。这是一个基于 gRPC 流的代理。而且很容易设置。这是我们选择它的主要原因。一台样机没花多长时间:

```
server := grpc.NewServer(
    grpc.CustomCodec(proxy.Codec()),
    grpc.UnknownServiceHandler(proxy.TransparentHandler(director)))

func director(ctx context.Context, fullMethodName string) (context.Context, *grpc.ClientConn, error) {
  // ignore implementation of dispatching to different service part
  // I would mention it later
} 
```

而且我发现 gRPC 服务器是`http.Server`，听起来很棒！

```
func AddRoutes(group *gin.RouterGroup) {
    group.POST("/", func(c *gin.Context) {
        server.ServeHTTP(c.Writer, c.Request)
    })
}

// main
g := handler.Group("/grpc")
AddRoutes(g) 
```

一切看起来都很棒；我们将请求发送到作为 gRPC 端点的`domain/grpc`。但那是行不通的！基于 HTTP/2 的 gRPC，gRPC 请求的请求路径是`/packageName.ServiceName/RPCName`，硬编码在生成的`*.pb.go`文件中。意味着我们不能改变它到`/grpc`端点的路径，我们也不能将这个子路径添加到`grpc.Dial`这个函数的域参数中，当然，我们可以添加一些技巧来改变路径，这是可行的。但是不可能要求每个用户都这样做。并且在`gin`中发出 gRPC 作为通配符路径很难纠正它，尽管它可能是正确的(因为 gRPC 路径格式非常不正常，所以我们创建了一些类似的路径)，所以我们为 gRPC 端点购买了另一个域。

现在，代码应该是:

```
// We were switching the emit group by domains
// this is part of the grpc domain
g := handler.Group("/")
AddRoutes(g)

func AddRoutes(group *gin.RouterGroup) {
    // we won't use that wildcard path directly but still have to write it down for path matching
    group.POST("/*path", func(c *gin.Context) {
        server.ServeHTTP(c.Writer, c.Request)
    })
} 
```

现在让我们回到调度请求部分，在`director`中，我们有`fullMethodName`，这是 gRPC 请求路径，我们可以用它作为我们的目标识别:

```
func director(ctx context.Context, fullMethodName string) (context.Context, *grpc.ClientConn, error) {
    // we use config to expose the services and store in a map
    // here is just a pseudo code, but got the idea is enough
    target, exist := services[fullMethodName]
    if !exist {
        return ctx, nil, fmt.Errorf("no service found for %s", fullMethodName)
    }
    clientConn, err := grpc.DialContext(
        ctx,
        target,
        grpc.WithCodec(grpcproxy.Codec()),
        // ignore TLS part, but that's very easy to get it from official guide
    )
    return ctx, clientConn, err
} 
```

文章短；我们也发现了这个任务的很多问题，希望你能从中得到一些乐趣，就像我们一样，痛苦但快乐，平静。