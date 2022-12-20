# 基于 HTTP 方法的 AWS ALB 路由

> 原文：<https://dev.to/k4ml/aws-alb-routing-based-on-http-method-3lp1>

tldr-这是不可能的。

与 ELB 经典版相比，新的 AWS ALB 具有更灵活的路由传入请求的方式，但仍有不足之处。它只支持基于路径或主机路由。因此，您可以将对`/customers/logs/`和`/customers/orders/`的请求路由到不同的目标组，比如不同的 ec2 实例集群。

但是不可能基于 http 方法路由，像`POST /customers/orders/`和`GET /customers/orders/`到不同的目标。一个实际的用例是当`GET /customers/orders/`是资源密集型的，并且您想要分离请求以避免对服务的其他部分造成影响。

今天，一个朋友也分享了来自 Gojek(印度尼西亚打车服务)的一个叫 [Weaver](https://github.com/gojektech/weaver) 的家伙的负载平衡器。它似乎支持基于方法路由:-

```
{
  "id": "gojek_hello",
  "criterion" : "Method(`POST`) && Path(`/gojek/hello-service`)",
  "endpoint" : {
    "shard_expr": ".serviceType",
    "matcher": "body",
    "shard_func": "lookup",
    "shard_config": {
      "999": {
        "backend_name": "hello_backend",
        "backend":"http://hello.golabs.io"
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

基于[Vulcan 和路由](https://godoc.org/github.com/vulcand/route)表达的标准，这是 MailGun 的另一个负载平衡器。瓦肯德的文件说:-

> 包路由提供 http 包兼容的路由库。它可以通过主机名、方法、路径和头来路由 http 请求。

以上就是我自己的笔记。

图片归功于[https://www.flickr.com/photos/small_realm/14699606019](https://www.flickr.com/photos/small_realm/14699606019)。