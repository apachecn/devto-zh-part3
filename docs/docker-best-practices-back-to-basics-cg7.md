# Docker 最佳实践，回归基础。

> 原文：<https://dev.to/louhdetech/docker-best-practices-back-to-basics-cg7>

你是 Docker 的新人？我也是。每个人都想用 Docker 构建令人敬畏的项目，构建一些图像并启动容器。太酷了！Docker 很酷。但是你忽略了一点，使用它是有一些规则的。

# 规则一！保持简单小巧。

Docker 图像应该很小。越小越好。Docker 是一个上下文系统，而不是虚拟机。不要在上面复制你所有的 GNU/Linux 发行版。

为了有一个轻量级的形象，你可以先看看阿尔卑斯山版本。Alpine 是一个基于 MUSL 和 BusyBox 而不是基本 GNU 的 Linux。如果你想使用 Debian 的基本镜像，不要忘记在安装包后清理 apt。

# 选择一个正式的 Docker 形象，你必须。

Docker Hub 上有大量 Docker 图片，但它们并不都是安全的。你必须选择一个官方形象，并添加你所需要的东西。例如，不要使用在 Hub 上发现的不知名的人制作的 php 图像，而要使用带有修改过的标签的 PHP 官方图像。

如果你真的需要使用非官方的图像，看看 Dockerfile。大多数图像提供了与图像相关的 Dockerfile 文件。如果基础图片是基于其他非官方图片，搜索它！越深入越好。最后，避免没有提供 Dockerfile 文件的关闭图像，这可能是不安全的。

# 你是说图像标记？不要用最新的。

当您构建 Dockerfile 文件时，每个人都已经在图像上添加了一个带有最新标签的 FROM 语句。各位，不要说谎。所以不要那么做！原因很简单，最新的标签是最新的，而不是更稳定的。如果最新的标签变化到一个更高的版本，它可以打破你所有的系统。您应该考虑选择一个版本，而不管您使用的基础映像是什么。

# 开发环境不同于生产环境。

对于生产中的使用，有更多的规则。
首先，不要在制作上使用 docker-compose。更喜欢使用像 Swarm 或 Kubernetes 这样的管弦乐。如果你必须在一个项目中使用 docker-compose，你可以切换到 orchestrator。一点也不过分。

其次，容器应该是无状态的。例如，不要将数据库上下文化。

最后，一个容器，一个任务。一个容器应该只做一件事，比如运行一个 web 服务器，执行一个脚本，运行一个 ElasticSearch 或者 Redis 或者 RabbitMQ 甚至一个 GitLab，但不是全部。

# TL；速度三角形定位法(dead reckoning)

总结一下:

*   制作小而简单的图像
*   尽可能使用官方的 Docker Hub 图片
*   不要使用最新的标签
*   小心你在生产中所做的事情

# 有用的链接

[https://docs.docker.com/develop/dev-best-practices/](https://docs.docker.com/develop/dev-best-practices/)
[https://docs . docker . com/develop/develop-images/Docker file _ best-practices/](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
以及所有 Docker 文档

欢迎在评论中添加您的最佳实践！