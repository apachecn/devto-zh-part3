# nginx 镜像技巧和窍门

> 原文：<https://dev.to/dzeban/nginx-mirroring-tips-and-tricks-17c2>

最近，我一直在玩 nginx 和它在 1.13.4 中出现的相对较新的 [**镜像**模块](http://nginx.org/en/docs/http/ngx_http_mirror_module.htm)。镜像模块允许您将请求复制到另一个后端，同时忽略来自它的响应。这方面的示例用例有:

*   通过观察您的新系统如何处理实际生产流量进行生产前测试
*   记录安全分析请求。这是[wall arm 工具做什么](https://docs.wallarm.com/en/admin-en/mirror-traffic-en.htm)
*   复制数据科学研究的请求
*   等等。

我已经用它对新重写的系统进行了生产前测试，看看效果如何(如果有的话；-)它可以处理生产工作量。有一些非显而易见的问题和技巧，我在开始这段旅程时没有发现，现在想分享一下。

## 基本设置

让我们从一个简单的设置开始。比方说，我们有一些处理生产工作负载的后端，我们在它前面放了一个代理:

[![nginx basic setup](img/835141e50d767e91f2756935c08c808e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yVb-uywo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/nginx-mirror-basic-setup.png)

以下是 nginx 配置:

```
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

有 2 个部分-后端和代理。代理(nginx)监听端口 8000，并在端口 10000 上向后端发送请求。没什么特别的，但是让我们做一个快速的负载测试，看看它的表现如何。我使用 [`hey`工具](https://github.com/rakyll/hey),因为它简单并且允许产生恒定负载，而不是像许多其他工具(wrk，apache benchmark，siege)一样尽可能猛烈地轰击。

```
$ hey -z 10s -q 1000 -n 100000 -c 1 -t 1 http://proxy.local:8000

Summary:
  Total:    10.0016 secs
  Slowest:  0.0225 secs
  Fastest:  0.0003 secs
  Average:  0.0005 secs
  Requests/sec: 995.8393

  Total data:   6095520 bytes
  Size/request: 612 bytes

Response time histogram:
  0.000 [1] |
  0.003 [9954]  |■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  0.005 [4] |
  0.007 [0] |
  0.009 [0] |
  0.011 [0] |
  0.014 [0] |
  0.016 [0] |
  0.018 [0] |
  0.020 [0] |
  0.022 [1] |

Latency distribution:
  10% in 0.0003 secs
  25% in 0.0004 secs
  50% in 0.0005 secs
  75% in 0.0006 secs
  90% in 0.0007 secs
  95% in 0.0007 secs
  99% in 0.0009 secs

Details (average, fastest, slowest):
  DNS+dialup:   0.0000 secs, 0.0003 secs, 0.0225 secs
  DNS-lookup:   0.0000 secs, 0.0000 secs, 0.0008 secs
  req write:    0.0000 secs, 0.0000 secs, 0.0003 secs
  resp wait:    0.0004 secs, 0.0002 secs, 0.0198 secs
  resp read:    0.0001 secs, 0.0000 secs, 0.0012 secs

Status code distribution:
  [200] 9960 responses 
```

很好，大多数请求都在不到一毫秒的时间内得到处理，并且没有错误——这是我们的基线。

## 基本镜像

现在，让我们放置另一个测试后端，并向其镜像流量

[![nginx mirror setup](img/c8b083f224b766564531859261d228b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DUI8Ed7x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/nginx-mirror-mirror-setup.png)

基本镜像配置如下:

```
upstream backend {
    server backend.local:10000;
}

upstream test_backend {
    server test.local:20000;
}

server {
    server_name proxy.local;
    listen 8000;

    location / {
        mirror /mirror;
        proxy_pass http://backend;
    }

    location = /mirror {
        internal;
        proxy_pass http://test_backend$request_uri;
    }

} 
```

我们添加`mirror`指令来将请求镜像到内部位置，并定义该内部位置。在这个内部位置，我们可以做 nginx 允许我们做任何事情，但是现在我们只是简单地代理传递所有请求。

让我们再次进行负载测试，检查镜像对性能的影响:

```
$ hey -z 10s -q 1000 -n 100000 -c 1 -t 1 http://proxy.local:8000

Summary:
  Total:    10.0010 secs
  Slowest:  0.0042 secs
  Fastest:  0.0003 secs
  Average:  0.0005 secs
  Requests/sec: 997.3967

  Total data:   6104700 bytes
  Size/request: 612 bytes

Response time histogram:
  0.000 [1] |
  0.001 [9132]  |■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  0.001 [792]   |■■■
  0.001 [43]    |
  0.002 [3] |
  0.002 [0] |
  0.003 [2] |
  0.003 [0] |
  0.003 [0] |
  0.004 [1] |
  0.004 [1] |

Latency distribution:
  10% in 0.0003 secs
  25% in 0.0004 secs
  50% in 0.0005 secs
  75% in 0.0006 secs
  90% in 0.0007 secs
  95% in 0.0008 secs
  99% in 0.0010 secs

Details (average, fastest, slowest):
  DNS+dialup:   0.0000 secs, 0.0003 secs, 0.0042 secs
  DNS-lookup:   0.0000 secs, 0.0000 secs, 0.0009 secs
  req write:    0.0000 secs, 0.0000 secs, 0.0002 secs
  resp wait:    0.0004 secs, 0.0002 secs, 0.0041 secs
  resp read:    0.0001 secs, 0.0000 secs, 0.0021 secs

Status code distribution:
  [200] 9975 responses 
```

几乎是一样的，毫秒级延迟，没有错误。这很好，因为它证明了镜像本身不会影响原始请求。

## 镜像到错误的后端

这一切都很好，但如果镜像后端有一些错误，有时会出现错误怎么办？最初的请求会怎么样？

为了测试这一点，我做了一个[琐碎的 Go 服务](https://github.com/dzeban/mirror-backend)，它可以随机注入错误。让我们发射它

```
$ mirror-backend -errors
2019/01/13 14:43:12 Listening on port 20000, delay is 0, error injecting is true 
```

看看负载测试会显示什么:

```
$ hey -z 10s -q 1000 -n 100000 -c 1 -t 1 http://proxy.local:8000

Summary:
  Total:    10.0008 secs
  Slowest:  0.0027 secs
  Fastest:  0.0003 secs
  Average:  0.0005 secs
  Requests/sec: 998.7205

  Total data:   6112656 bytes
  Size/request: 612 bytes

Response time histogram:
  0.000 [1] |
  0.001 [7388]  |■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  0.001 [2232]  |■■■■■■■■■■■■
  0.001 [324]   |■■
  0.001 [27]    |
  0.002 [6] |
  0.002 [2] |
  0.002 [3] |
  0.002 [2] |
  0.002 [0] |
  0.003 [3] |

Latency distribution:
  10% in 0.0003 secs
  25% in 0.0003 secs
  50% in 0.0004 secs
  75% in 0.0006 secs
  90% in 0.0007 secs
  95% in 0.0008 secs
  99% in 0.0009 secs

Details (average, fastest, slowest):
  DNS+dialup:   0.0000 secs, 0.0003 secs, 0.0027 secs
  DNS-lookup:   0.0000 secs, 0.0000 secs, 0.0008 secs
  req write:    0.0000 secs, 0.0000 secs, 0.0001 secs
  resp wait:    0.0004 secs, 0.0002 secs, 0.0026 secs
  resp read:    0.0001 secs, 0.0000 secs, 0.0006 secs

Status code distribution:
  [200] 9988 responses 
```

一点都没变！这很好，因为镜像后端的错误不会影响主后端。nginx 镜像模块忽略对镜像子请求的响应，所以这种行为是好的，也是有意的。

## 镜像到慢速后端

但是，如果我们的镜像后端没有返回错误，而是非常慢，该怎么办呢？原始请求如何工作？让我们来了解一下！

我的镜像后端有一个选项，可以将每个请求延迟设定的秒数。在这里，我用 1 秒钟的延迟启动它:

```
$ mirror-backend -delay 1
2019/01/13 14:50:39 Listening on port 20000, delay is 1, error injecting is false 
```

那么让我们看看负载测试显示了什么:

```
$ hey -z 10s -q 1000 -n 100000 -c 1 -t 1 http://proxy.local:8000

Summary:
  Total:    10.0290 secs
  Slowest:  0.0023 secs
  Fastest:  0.0018 secs
  Average:  0.0021 secs
  Requests/sec: 1.9942

  Total data:   6120 bytes
  Size/request: 612 bytes

Response time histogram:
  0.002 [1] |■■■■■■■■■■
  0.002 [0] |
  0.002 [1] |■■■■■■■■■■
  0.002 [0] |
  0.002 [0] |
  0.002 [0] |
  0.002 [1] |■■■■■■■■■■
  0.002 [1] |■■■■■■■■■■
  0.002 [0] |
  0.002 [4] |■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  0.002 [2] |■■■■■■■■■■■■■■■■■■■■

Latency distribution:
  10% in 0.0018 secs
  25% in 0.0021 secs
  50% in 0.0022 secs
  75% in 0.0023 secs
  90% in 0.0023 secs
  0% in 0.0000 secs
  0% in 0.0000 secs

Details (average, fastest, slowest):
  DNS+dialup:   0.0007 secs, 0.0018 secs, 0.0023 secs
  DNS-lookup:   0.0003 secs, 0.0002 secs, 0.0006 secs
  req write:    0.0001 secs, 0.0001 secs, 0.0002 secs
  resp wait:    0.0011 secs, 0.0007 secs, 0.0013 secs
  resp read:    0.0002 secs, 0.0001 secs, 0.0002 secs

Status code distribution:
  [200] 10 responses

Error distribution:
  [10]  Get http://proxy.local:8000: net/http: request canceled (Client.Timeout exceeded while awaiting headers) 
```

什么？1.9 rps？我的 1000 rps 呢？我们有错误？发生什么事了？

让我解释一下 nginx 中的镜像是如何工作的。

### nginx 中镜像的工作原理

当请求到达 nginx 时，如果启用了镜像，nginx 将创建一个镜像子请求，并执行镜像位置指定的操作——在我们的例子中，它将请求发送到镜像后端。

但事实是子请求与原始请求相关联，所以*就我对*的理解而言，除非镜像子请求没有完成，否则原始请求将会受到限制。

这就是为什么我们在之前的测试中获得了大约 2 个 RPS—`hey`发送了 10 个请求，获得了响应，发送了接下来的 10 个请求，但是它们停滞了，因为之前的镜像子请求被延迟了，然后超时开始，最后 10 个请求出错。

如果我们将 hey 中的超时增加到 10 秒，我们将不会收到任何错误和 1 个 rps:

```
$ hey -z 10s -q 1000 -n 100000 -c 1 -t 10 http://proxy.local:8000

Summary:
  Total:    10.0197 secs
  Slowest:  1.0018 secs
  Fastest:  0.0020 secs
  Average:  0.9105 secs
  Requests/sec: 1.0978

  Total data:   6732 bytes
  Size/request: 612 bytes

Response time histogram:
  0.002 [1] |■■■■
  0.102 [0] |
  0.202 [0] |
  0.302 [0] |
  0.402 [0] |
  0.502 [0] |
  0.602 [0] |
  0.702 [0] |
  0.802 [0] |
  0.902 [0] |
  1.002 [10]    |■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■

Latency distribution:
  10% in 1.0011 secs
  25% in 1.0012 secs
  50% in 1.0016 secs
  75% in 1.0016 secs
  90% in 1.0018 secs
  0% in 0.0000 secs
  0% in 0.0000 secs

Details (average, fastest, slowest):
  DNS+dialup:   0.0001 secs, 0.0020 secs, 1.0018 secs
  DNS-lookup:   0.0000 secs, 0.0000 secs, 0.0005 secs
  req write:    0.0001 secs, 0.0000 secs, 0.0002 secs
  resp wait:    0.9101 secs, 0.0008 secs, 1.0015 secs
  resp read:    0.0002 secs, 0.0001 secs, 0.0003 secs

Status code distribution:
  [200] 11 responses 
```

所以这里的要点是**如果镜像的子请求很慢，那么原始请求将被抑制**。我不知道如何解决这个问题，但我知道解决方法——只镜像部分流量。让我告诉你怎么做。

## 镜像部分流量

如果您不确定镜像后端能否处理原始负载，您可以只镜像部分流量，例如 10%。

指令是不可配置的，它将所有请求复制到镜像位置，因此不清楚如何做到这一点。实现这一点的关键是内部后视镜的位置。如果您记得我说过，您可以在镜像请求的位置对其进行任何操作。我是这样做的:

```
 1  upstream backend {
 2      server backend.local:10000;
 3  }
 4  
 5  upstream test_backend {
 6      server test.local:20000;
 7  }
 8  
 9  split_clients $remote_addr $mirror_backend {
10      50% test_backend;
11      *   "";
12  }
13  
14  server {
15      server_name proxy.local;
16      listen 8000;
17  
18      access_log /var/log/nginx/proxy.log;
19      error_log /var/log/nginx/proxy.error.log info;
20  
21      location / {
22          mirror /mirror;
23          proxy_pass http://backend;
24      }
25  
26      location = /mirror {
27          internal;
28          if ($mirror_backend = "") {
29              return 400;
30          }
31  
32          proxy_pass http://$mirror_backend$request_uri;
33      }
34  
35  }
36 
```

首先，在镜像位置，我们代理传递到从变量`$mirror_backend`(第 32 行)获取的上游。该变量根据客户端远程地址在`split_client`块(第 9-12 行)中设置。`split_client`所做的是基于左变量分布设置右变量值。在我们的例子中，我们查看请求远程地址(`$remote_addr`变量)，对于 50%的远程地址，我们将`$mirror_backend`设置为`test_backend`，对于其他请求，它被设置为空字符串。最后，部分部分在镜像位置执行——如果`$mirror_backend`变量为空，我们拒绝该镜像子请求，否则我们`proxy_pass`它。请记住，镜像子请求中的失败不会影响原始请求，因此删除具有错误状态的请求是安全的。

这种解决方案的美妙之处在于，您可以根据任何变量或组合来分割镜像流量。如果你想真正区分你的用户，那么远程地址可能不是最好的分割密钥-用户可能会使用许多 IP 或改变他们。在这种情况下，你最好使用一些用户粘性键，比如 API key。为了根据`apikey`查询参数镜像 50%的流量，我们只需在`split_client` :
中更改密钥

```
split_clients $arg_apikey $mirror_backend {
    50% test_backend;
    * "";
} 
```

当我们从 1 到 20 查询 apikeys 时，只有一半(11)会被镜像。这里是旋度:

```
$ for i in {1..20};do curl -i "proxy.local:8000/?apikey=${i}" ;done 
```

这里是镜像后端的日志:

```
...
2019/01/13 22:34:34 addr=127.0.0.1:47224 host=test_backend uri="/?apikey=1"
2019/01/13 22:34:34 addr=127.0.0.1:47230 host=test_backend uri="/?apikey=2"
2019/01/13 22:34:34 addr=127.0.0.1:47240 host=test_backend uri="/?apikey=4"
2019/01/13 22:34:34 addr=127.0.0.1:47246 host=test_backend uri="/?apikey=5"
2019/01/13 22:34:34 addr=127.0.0.1:47252 host=test_backend uri="/?apikey=6"
2019/01/13 22:34:34 addr=127.0.0.1:47262 host=test_backend uri="/?apikey=8"
2019/01/13 22:34:34 addr=127.0.0.1:47272 host=test_backend uri="/?apikey=10"
2019/01/13 22:34:34 addr=127.0.0.1:47278 host=test_backend uri="/?apikey=11"
2019/01/13 22:34:34 addr=127.0.0.1:47288 host=test_backend uri="/?apikey=13"
2019/01/13 22:34:34 addr=127.0.0.1:47298 host=test_backend uri="/?apikey=15"
2019/01/13 22:34:34 addr=127.0.0.1:47308 host=test_backend uri="/?apikey=17"
... 
```

最棒的是,`split_client`中的分区是一致的——使用`apikey=1`的请求总是被镜像。

## 结论

这就是我目前为止对 nginx 镜像模块的体验。我已经向您展示了如何简单地镜像所有流量，如何在`split_client`模块的帮助下镜像部分流量。我还介绍了在镜像后端变慢的情况下正常请求被抑制时的错误处理和不明显的问题。

希望你喜欢它！订阅 [Atom feed](https://alex.dzyoba.com/feed) 。我也在 twitter @AlexDzyoba 上发布[。](https://twitter.com/AlexDzyoba/)

暂时就这样吧，下次再说！