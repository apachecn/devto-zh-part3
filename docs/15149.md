# 特使第一印象

> 原文：<https://dev.to/dzeban/envoy-first-impression-3871>

当我用 nginx 做[流量镜像时，我偶然发现了一个令人惊讶的问题——如果镜像后端很慢，nginx 会延迟原始请求。这真的很糟糕，因为您认为镜像是“一劳永逸”的。无论如何，我已经通过只镜像部分流量解决了这个问题，但这促使我寻找另一个可以镜像流量而没有这些问题的代理。这是我最终找到时间和精力去调查](https://alex.dzyoba.com/blog/nginx-mirror/)[特使](https://www.envoyproxy.io/)的时候——我已经听说了很多关于它的伟大的事情，并且总是想用它来弄脏我的手。

以防你从未听说过它——Envoy 是一个代理服务器，最常用于服务网格场景，但它也可以是一个边缘代理。

在这篇文章中，我将只寻找**边缘代理场景**，因为我从未维护过服务网格。记住这个用例。此外，我将不可避免地将 Envoy 与 nginx 进行比较，因为这是我所知道和使用的。

## 特使有什么了不起

我想试用 Envoy 的主要原因是它有几个引人注目的特性:

*   可观察性
*   高级负载平衡策略
*   主动检查
*   展开性

让我们打开列表！

### 可观性

可观察性是 Envoy 中最彻底的特性之一。它的设计原则之一是提供网络通信的透明性，因为现代系统是用所有这些疯狂的微服务构建的。

开箱即用，它为包括普罗米修斯在内的各种度量系统提供了大量的度量标准。

为了在 nginx 中获得这种洞察力，你必须购买 [nginx plus](https://www.nginx.com/products/nginx/live-activity-monitoring) 或者使用 VTSmodule，从而自己编译 nginx。希望我的项目 [nginx-vts-build](https://github.com/alexdzyoba/nginx-vts-build) 会有所帮助——我正在构建带有 vts 模块的 nginx，作为带有 systemd 服务和基本配置的股票 nginx 的替代产品。就当是 nginx 发行版吧。目前，它只有一个 Debian 9 版本，但我愿意接受建议。如果您有功能需求，请告诉我。但是让我们回到特使。

除了度量标准，Envoy [还可以与 Jaeger 这样的分布式追踪系统](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/tracing)集成。

最后，它可以[捕获流量](https://www.envoyproxy.io/docs/envoy/latest/operations/traffic_capture)，以供 wireshark 进一步分析。

我只看过普罗米修斯的度量标准，它们相当不错！

### 高级负载均衡

Envoy 中的负载平衡[功能非常丰富](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/load_balancing/load_balancers)。它不仅支持循环、加权和随机策略，还支持使用一致哈希算法(如 ketama 和 maglev)的负载平衡。后者的意义在于，在上游集群
重新平衡的情况下，交通模式的变化更少。

同样，你可以在 nginx 中获得同样的[高级功能，但前提是你要为 nginx plus 付费。](https://www.nginx.com/products/nginx/load-balancing)

### 主动检查

为了[检查上游端点的健康状况](https://www.envoyproxy.io/docs/envoy/latest/operations/traffic_capture),特使将主动发送请求并期待有效的回答，因此该端点将保留在上游集群中。这是开源 nginx 缺乏的一个非常好的特性(但是 [nginx plus 有](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-health-check/#active-health-checks))。

### 扩展性

可以将 Envoy 配置为 [Redis 代理](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/redis)、 [DynamoDB 过滤器](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/dynamo)、 [MongoDB 过滤器](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/mongo)、 [grpc 代理](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/grpc)、 [MySQL 过滤器](https://www.envoyproxy.io/docs/envoy/latest/configuration/network_filters/mysql_proxy_filter)、[节俭过滤器](https://www.envoyproxy.io/docs/envoy/latest/configuration/network_filters/thrift_proxy_filter)。

这并不是一个致命的特性，imho，考虑到这些协议支持的大部分都是实验性的，但无论如何它是很好的，并表明特使是可扩展的。

它还支持开箱即用的 Lua 脚本。对于 nginx，你必须使用 [OpenResty](https://openresty.org/) 。

## 特使有什么了不起的

仅上述特性就足以成为使用 Envoy 的理由。然而，我发现有几件事阻止我从 nginx 切换到 Envoy:

*   不缓存
*   无静态内容服务
*   缺乏灵活的配置
*   仅 Docker 包装

### 无缓存

特使[不支持缓存响应](https://github.com/envoyproxy/envoy/issues/868)。这是 edge 代理的必备特性，nginx 很好地实现了它。

### 没有静态内容的上菜

虽然 Envoy 在网络方面做得很好，但除了初始配置文件加载和运行时配置处理之外，它并不访问文件系统。如果你想提供静态文件，比如前端的东西(js，html，css ),那么你就不走运了——Envoy 不支持这个。Nginx 也做得很好。

### 缺乏灵活的配置

特使是通过 YAML 配置的，对我来说，它的配置感觉非常明确，尽管我认为这实际上是一件好事——明确的比隐含的好。但是我觉得 Envoy 配置受到了 Envoy 中专门实现的特性的限制。*也许是缺乏使用 Envoy 的经验和旧习惯*，但我觉得在 nginx 的地图、重写模块(使用`if`指令)和其他不错的模块中，我有一个非常灵活的配置系统，允许我实现任何东西。当然，这种灵活性的成本是复杂性的一部分——nginx 配置需要一些学习和实践，但在我看来这是值得的。

尽管如此，Envoy 支持动态配置，尽管它不像你可以通过 REST 调用更改某些配置部分，它是关于配置设置的发现——这就是整个 XDS 协议及其 EDS、CDS、RDS 和 what-not-DS 的全部内容。

引用[文档](https://github.com/envoyproxy/data-plane-api/blob/master/XDS_PROTOCOL.md):

> Envoy 通过文件系统或通过查询一个或多个管理服务器来发现它的各种动态资源。

重点是我-我想指出，你必须提供一个服务器，将响应特使发现(XDS)的请求。

然而，没有现成的解决方案来执行特使的 XDS 协议。有一个[转子](https://github.com/turbinelabs/rotor)，但背后的公司关闭了，所以这个项目基本上是死的。

有一个 Istio，但它是一个我现在不想碰的怪物。此外，如果你在 Kubernetes 上，那么有一个七边形轮廓，但不是每个人都需要和使用 Kubernetes。

最后，您可以使用 [go-control-plane 存根](https://github.com/envoyproxy/go-control-plane)实现自己的 XDS 服务。

但是那个好像没有用。我看到大多数人使用 DNS forEDS 和 CD。特别是，记住 Consul 有 DNS 接口，似乎我们可以使用 Consul 动态地向特使提供主机列表。这不是什么大新闻，因为我可以(也确实)使用 Consul 通过在`proxy_pass`和`resolver`指令中使用 DNS 名称来提供 nginx 的后端列表。

此外，[领事连接支持特使](https://www.consul.io/docs/connect/proxies/envoy.html)的代理请求，但这不是关于特使-这是关于领事有多棒！

因此，特使的整个动态配置真的令人困惑，难以理解，因为每当你试图谷歌它时，你都会收到大量关于 Istio 的帖子，令人分心。

### Docker 专用包装

这是一件小事，但它让我很恼火。还有，我不喜欢 Docker 图片没有带版本的标签。也许这是有意让你总是运行最新版本，但它似乎很奇怪。

### 关于不太重要的部分的结论

最后，我并不是说 Envoy 不好，从我的角度来看，它只是对高级代理和进程外服务网格数据平面有不同的关注。边缘代理部分只是一个额外的好处，适用于一些但不是很多的情况。

## 镜像呢？

话虽如此，让我们看看特使在实践中，并重复镜像实验从我以前的职位。

这里有两个最小的配置——一个用于 nginx，另一个用于 Envoy。两者都做同样的事情——简单地将请求代理到一些后端服务。

```
# nginx proxy config

upstream backend {
    server backend.local:10000;
}

server {
    server_name proxy.local;
    listen 8000;

    location / {
        proxy_pass http://backend;
    }
} 
```

```
# Envoy proxy config
static_resources:
  listeners:
  - name: listener_0
    address:
      socket_address:
        protocol: TCP
        address: 0.0.0.0
        port_value: 8001
    filter_chains:
    - filters:
      - name: envoy.http_connection_manager
        config:
          stat_prefix: ingress_http
          route_config:
            virtual_hosts:
            - name: local_service
              domains: ['*']
              routes:
              - match:
                  prefix: "/"
                route:
                  cluster: backend
          http_filters:
          - name: envoy.router
  clusters:
  - name: backend
    type: STATIC
    connect_timeout: 1s
    hosts:
      - socket_address:
          address: 127.0.0.1
          port_value: 10000 
```

他们的表现一模一样:

```
$ # Load test nginx
$ hey -z 10s -q 1000 -c 1 -t 1 http://proxy.local:8000

Summary:
  Total:    10.0006 secs
  Slowest:  0.0229 secs
  Fastest:  0.0002 secs
  Average:  0.0004 secs
  Requests/sec: 996.7418

  Total data:   36881600 bytes
  Size/request: 3700 bytes

Response time histogram:
  0.000 [1] |
  0.002 [9963]  |■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  0.005 [3] |
  0.007 [0] |
  0.009 [0] |
  0.012 [0] |
  0.014 [0] |
  0.016 [0] |
  0.018 [0] |
  0.021 [0] |
  0.023 [1] |

...

Status code distribution:
  [200] 9968 responses

$ # Load test Envoy
$ hey -z 10s -q 1000 -c 1 -t 1 http://proxy.local:8001

Summary:
  Total:    10.0006 secs
  Slowest:  0.0307 secs
  Fastest:  0.0003 secs
  Average:  0.0007 secs
  Requests/sec: 996.1445

  Total data:   36859400 bytes
  Size/request: 3700 bytes

Response time histogram:
  0.000 [1] |
  0.003 [9960]  |■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  0.006 [0] |
  0.009 [0] |
  0.012 [0] |
  0.015 [0] |
  0.019 [0] |
  0.022 [0] |
  0.025 [0] |
  0.028 [0] |
  0.031 [1] |

...

Status code distribution:
  [200] 9962 responses 
```

无论如何，让我们检查一下关键部分——延迟镜像到后端。快速提醒——在这种情况下，nginx 会抑制原始请求，从而影响您的生产用户。

以下是特使的镜像配置:

```
# Envoy mirroring config
static_resources:
  listeners:
  - name: listener_0
    address:
      socket_address:
        protocol: TCP
        address: 0.0.0.0
        port_value: 8001
    filter_chains:
    - filters:
      - name: envoy.http_connection_manager
        config:
          stat_prefix: ingress_http
          route_config:
            virtual_hosts:
            - name: local_service
              domains: ['*']
              routes:
              - match:
                  prefix: "/"
                route:
                  cluster: backend
                  request_mirror_policy:
                    cluster: mirror
          http_filters:
          - name: envoy.router
  clusters:
  - name: backend
    type: STATIC
    connect_timeout: 1s
    hosts:
      - socket_address:
          address: 127.0.0.1
          port_value: 10000
  - name: mirror
    type: STATIC
    connect_timeout: 1s
    hosts:
      - socket_address:
          address: 127.0.0.1
          port_value: 20000 
```

基本上，我们已经将`request_mirror_policy`添加到主路由中，并为镜像定义了集群。让我们进行负载测试吧！

```
$ hey -z 10s -q 1000 -c 1 -t 1 http://proxy.local:8001

Summary:
  Total:    10.0012 secs
  Slowest:  0.0046 secs
  Fastest:  0.0003 secs
  Average:  0.0008 secs
  Requests/sec: 997.6801

  Total data:   36918600 bytes
  Size/request: 3700 bytes

Response time histogram:
  0.000 [1] |
  0.001 [2983]  |■■■■■■■■■■■■■■■■■
  0.001 [6916]  |■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  0.002 [72]    |
  0.002 [2] |
  0.002 [0] |
  0.003 [0] |
  0.003 [3] |
  0.004 [0] |
  0.004 [0] |
  0.005 [1] |

...

Status code distribution:
  [200] 9978 responses 
```

零错误和惊人的延迟！这是一个胜利，它证明了特使的镜像是真正的“开火并忘记”！

## 结论

Envoy 的网络质量非常好——它的镜像是经过深思熟虑的，它的负载平衡非常先进，我喜欢主动健康检查功能。

我不相信在边缘代理场景中使用它，因为您可能需要 web 服务器的功能，如缓存、内容服务和高级配置。

至于服务网络——当有机会时，我肯定会对此进行评估，所以请继续关注——订阅[博客 Atom feed](https://alex.dzyoba.com/feed) 并查看[我的 twitter @AlexDzyoba](https://twitter.com/AlexDzyoba/) 。

暂时就这样吧，下次再说！