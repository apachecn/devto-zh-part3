# 在 Windows 中使用 docker compose 实现容器间的通信

> 原文：<https://dev.to/michaeljolley/communication-between-containers-using-docker-compose-in-windows-3hpf>

这一定要放在“以后记住这个”下面。

我最近一直在做一个项目，其中包括使用 Angular 的 aspnetcore SPA。它使用一个流行的部署工具被部署到多个 Raspberry Pi 上。然而，随着客户使用的设备数量的增长，部署工具的成本变得令人望而却步。在探索其他选项时，我们登陆了 Azure 物联网中心。

在生产中，Pi 上的应用程序与位于客户主办公室的 Restful API 进行通信。然而，在调试时，我们需要并行运行它们。所以，docker-compose 来拯救(我认为。)

## 设置 Dockerfiles

我们的大多数开发人员都在使用 VS 2017 或 2019，因此我们使用内置功能将预构建的 Dockerfile 添加到物联网应用程序中。

一旦我们开始调试，我们发现我们基于 docker 的物联网应用程序无法与 API 加载到的 localhost:port 通信。这完全有道理，因为 docker 容器认为它是本地主机，而不是主机。所以我们决定将我们的 API 加载到一个容器中。相同的步骤，使用 Visual Studio 添加 Dockerfile 文件。

## 好戏开始了(或者还没开始)

有了这两个 docker 文件，我们设置一个 docker-compose 来启动它们。在查看了解释允许两个容器通信的不同方法的多个站点后，我们最终找到了正确的解决方案。所以，为了我们未来的提醒，也可能是一个帮助他人的机会:

## 如何设置 docker-compose 文件以允许 Windows 上两个或多个容器之间的通信

虽然这篇文章的结尾是巨大的，但实际上让 docker-compose 正确是相当简单的。

首先，在 docker 中添加两个服务，如下所示:

```
version 3.4

services:
  iotapp:
    image: ${DOCKER_REGISTRY-}iotapp
    build:
      context: .
      dockerfile: *path to your app Dockerfile here*
    ports:
      - {external port}:{internal port}
    links:
      - api
  api:
    image: ${DOCKER_REGISTRY-}api
    build:
      context: .
      dockerfile: *path to your api Dockerfile here*
    ports:
       - {external port}:{internal port} 
```

Enter fullscreen mode Exit fullscreen mode

注意 iotapp 服务中的**链接**标志。这让 Docker 知道 iotapp 将需要能够与 api 项目通信。

很简单，对吧？

对我们来说，缺少的一块拼图是如何在两者之间进行交流。

例如，在我们的 iotapp 代码中，它被硬编码为与`http://localhost:{port}/api`通信。然而，这是不行的，因为每个 api & iotapp 容器都有自己的“本地主机”。经过多次试验和几次错误的 StackOverflow & Google 响应后，我们发现必须调用 docker-compose 中指定的服务名称。因此，我们 iotapp 中对`http://localhost:{port}/api`的任何调用都需要更改为`http://api:{port}/api`。

看看需要什么和“如何做”，它完全有意义，但这一个难倒了我几个小时。如果你正在读这篇文章，希望谷歌已经发给你，并为你节省了时间。

我错过了什么或者我们可以做得更好？请在下面给我留言。