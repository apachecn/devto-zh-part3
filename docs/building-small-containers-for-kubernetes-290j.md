# 为 Kubernetes 建造小容器

> 原文：<https://dev.to/codehakase/building-small-containers-for-kubernetes-290j>

> 这是一个十字柱。原帖可以在我的[博客](https://hakaselogs.me)上找到

将任何应用程序部署到 Kubernetes 的第一步是将应用程序捆绑到一个容器中。对于各种语言和发行版，有几个官方和社区支持的容器映像，其中大多数容器可能非常大，或者有时包含您的应用程序可能永远不需要/使用的开销。

由于有了 [Docker](https://docker.io) ，只需几个步骤就可以轻松创建容器图像；指定基础映像，添加应用程序特定的更改，并构建容器。

```
FROM golang:alpine

WORKDIR /app

ADD . /app

EXPOSE 8080

ENTRYPOINT ["/app/run"] 
```

我们指定了一个基本映像(本例中是 Linux alpine)，设置了要在容器中使用的工作目录，公开了一个网络端口和一个入口点，这将在容器中启动应用程序。有了 Dockerfile 集，我们就可以构建容器了。

```
$ docker build myapp . 
```

虽然上述过程非常简单，但仍有一些问题需要考虑。使用默认映像会导致大型容器映像、安全漏洞和内存开销。

#### 让我们来充实一个示例应用程序

我们将在 Go 中编写一个简单的应用程序，它公开一个 HTTP 路由，当点击时返回一个字符串。我们将从它建立一个 Docker 图像。

```
package main

import (
    "fmt"
    "log"
    "net/http"
    "time"
)

func main() {
    r := http.NewServeMux()
    r.HandleFunc("/api/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello From Go!")
    })
    s := &http.Server{
        Addr:        ":8080",
        Handler:     r,
        ReadTimeout: 10 * time.Second,
    }
    fmt.Println("Starting server on port 8080")
    log.Fatal(s.ListenAndServe())
} 
```

让我们用我们的应用程序构建 Docker 图像。首先，我们需要创建一个 Dockerfile 文件。

```
FROM golang:latest

RUN mkdir /app
ADD . /app/
WORKDIR /app
RUN go build -o myapp .

CMD ["/app/myapp"] 
```

建立形象。

> PS:用任意选项替换标签:/appname

```
$ docker build -t codehakase/goapp . 
```

就是这样！我们刚刚编写了一个简单的 Go 应用程序。让我们来看看我们刚刚构建的图像。
[![docker images list](img/709a3e5ba519ecf1bf695d9050e950b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yscuQi8s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hakase-labs/image/upload/v1543632543/Screen_Shot_2018-12-01_at_3.47.19_AM_wgpi3m.png)

对于一个简单的 Go app 来说，图像超过 700 兆。Go 二进制文件本身的大小
可能有几兆字节，额外的开销是浪费空间，
也可能是错误和安全漏洞的藏身之处。

什么东西占了这么多空间？在这个场景中，容器需要 Go
安装，以及 Go 依赖的所有依赖项，所有这些都位于 Debian 或 Linux 发行版的
之上。

有两种方法可以减小容器图像的大小，实际上有三种方法第三种在 Go 社区中更常用:

1.  使用小型基础图像
2.  构建器模式
3.  使用空图像

使用小的基本图像是减小容器图像大小的最简单的方法。正在使用的
栈/语言可能提供了一个比默认图像小得多的官方图像。

让我们更新 Dockerfile 文件以使用一个小的基本映像。在这种情况下，我们将使用
`golang:alpine`。

```
FROM golang:alpine

RUN mkdir /app
ADD . /app/
WORKDIR /app
RUN go build -o myapp .

CMD ["/app/myapp"] 
```

重建图像。

```
$ docker build -t codehakase/goapp . 
```

随着 docker 文件的更新，我们的图像现在比之前的
图像要小。
[![using small base images](img/659e57086529154d60fa1f723a6a266e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zs9tiFEz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hakase-labs/image/upload/v1543633738/Screen_Shot_2018-12-01_at_4.07.28_AM_wm920i.png)

这个图像尺寸仍然很大，我们甚至可以使用
Builder 模式缩小。因为我们在这个例子中使用了编译语言(Go ),在
构建器模式中，我们应该注意到编译语言经常需要工具
,而这些工具并不一定是运行代码所需要的。这些工具主要用于
构建和编译成二进制文件。有了构建器模式，我们可以在最终容器中移除
这些工具。

为了在我们现有的例子中使用构建器模式，我们将在一个
容器中编译我们的代码，然后使用编译后的代码打包最终的容器，而不需要
所有需要的工具。

```
FROM golang:alpine AS main-env
RUN mkdir /app
ADD . /app/
WORKDIR /app
RUN cd /app && go build -o myapp

FROM alpine
WORKDIR /app
COPY --from=main-env /app/myapp /app
EXPOSE 8080

ENTRYPOINT ./myapp 
```

我们更新了 Dockerfile 文件以使用 builder 模式。首先，它在`alpine`容器中构建并
编译应用程序，并将步骤命名为`main-env`，然后
将前一步骤中的二进制文件复制到新容器中。

重建多级 Dockerfile 文件。

```
$ docker build -t codehakase/goapp . 
```

构建的结果是一个刚刚超过 10
兆字节的新容器。
[![builder pattern](img/80e6f13337ee0475c54ddbb23fcf0468.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---iKEL1vl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hakase-labs/image/upload/v1543634782/Screen_Shot_2018-12-01_at_4.24.48_AM_c6fnis.png)

还记得我们构建的第一个超过 700 兆字节的图像吗？我们已经能够使用 builder 模式将其减少到 10.7 兆字节。

我们仍然可以通过使用 scratch (empty)
图像来减少这个数字。什么是划痕图像？这是一个空的特殊 docker 图像。为了使用
,我们需要首先在 docker 环境外构建我们的应用程序，并将
编译的二进制文件添加到容器中。

```
$ go build -o myapp . 
```

我们将更新 Dockerfile 文件，将二进制文件添加到暂存映像中。

```
FROM scratch
ADD myapp /
CMD ["/myapp"] 
```

让我们建立这个图像，看看它有多大。
[![scratch image](img/a3d8fa69abe1e27b4b870e330742acac.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gc7TsVEV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hakase-labs/image/upload/v1543635780/Screen_Shot_2018-12-01_at_4.36.33_AM_n2pane.png)

我们把它降到了 6.5 兆字节，酷！让我们试着运行我们的容器来测试我们的应用程序。

```
$ docker run -it codehakase/goapp 
```

你可能会得到这样一个错误:
[![](img/b04b454bda3d9e822921521dfd331a20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LlCYxrgp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hakase-labs/image/upload/v1543635780/Screen_Shot_2018-12-01_at_4.40.08_AM_pvjj3k.png)

这个错误的原因是，Go 二进制文件正在运行它的操作系统上查找库，因为暂存映像是空的，所以没有库可以查找。
我们需要修改构建命令来静态编译我们的 Go 应用:

```
$ GO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o myapp . 
```

用`docker build -t codehakase/goapp`重新构建，并再次运行我们的容器
，将容器上的端口转发到我们的机器:

```
$ docker run -it -p 8080:8080 codehakase/goapp
  Starting server on port 8080 
```

导航至`http://localhost:8080/api`以测试应用程序的响应。

### 结论

本文的目标是解释如何专门为 Go 应用程序减少容器大小
。使用更小的容器，您可以获得更高的性能，因为
在 CI 环境中构建您的容器会更快，将
您构建的映像推送到容器注册表会花费更少的时间，而
最重要的是将这些容器拉到您的分布式 kubernetes
集群会更快，因为更小的容器不太可能延迟新集群的
部署。

如果您有任何建议或意见，请在下方留言或 ping
[@codehakase](https://twitter.com/codehakase)