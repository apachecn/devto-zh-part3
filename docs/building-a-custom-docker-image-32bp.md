# 构建自定义 Docker 映像

> 原文：<https://dev.to/ianknighton/building-a-custom-docker-image-32bp>

[![Building a Custom Docker Image](img/dde86687ba0301ee2f25c7665952c548.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3dK4bDb4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1536686155419-e5b11eb5d515%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

我打算用一个关于 Docker 图像的快速点击来打破我的记录目录帖子。最近我不得不经常这样做，我努力寻找一个快速、简洁的帖子。

## 问题

我需要一个 docker 镜像，上面安装了 Node、NPM、Go 和 Git。我可以组合图像，用那种方式变一些魔术，但是我更希望有我自己的图像，它有我需要的一切，并且以我知道的方式配置。

## 解

我先以 Ubuntu 18.04 镜像作为我的基础。这是一个相当精简的系统，但我很熟悉。

```
docker run -it --name base ubuntu:latest 
```

Enter fullscreen mode Exit fullscreen mode

这将提取图像，并通过 shell 访问 Ubuntu 图像。

```
root@66894d551112:/# 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经在我们的外壳中，我们可以更新和安装我们需要的部分。

```
apt update && \
apt upgrade && \
apt install -y git && \
apt install -y nodejs && \
apt install -y npm && \
apt install -y golang-go 
```

Enter fullscreen mode Exit fullscreen mode

这将需要一两分钟来运行。完成后，退出 shell。这将停止容器运行。这也是创建您可能需要的任何环境变量或文件夹的好机会。

现在，您可以将容器的状态提交给图像。

让我们检查以确保我们有容器的名称和 ID:

```
docker ps -a
CONTAINER ID IMAGE         COMMAND     CREATED       STATUS                   NAMES
66894d551112 ubuntu:latest "/bin/bash" 5 minutes ago Exited (0) 2 seconds ago base 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以使用 commit 命令。

```
docker commit <containerName or containerID> <imagename:<tag>> 
```

Enter fullscreen mode Exit fullscreen mode

所以在这种情况下应该是:

```
docker commit 66894d551112 baseimage:latest 
```

Enter fullscreen mode Exit fullscreen mode

此时，您的机器上有了一个可以使用的基本映像。如果您需要它对您的团队或 CI 渠道可用，您可以使用`docker tag`和`docker push`将其放入 docker hub。

```
docker tag baseimage:latest probablynotian/baseimage:190314.0
docker push probablynotian/baseimage:190314.0 
```

Enter fullscreen mode Exit fullscreen mode

今后，您可以在任何地方引用此图像。这让你的`Dockerfile`工作起来容易多了。

我希望这有所帮助！

*(如果你想使用这张图片，我已经把它放在我的[公共 docker hub 账户](https://hub.docker.com/r/probablynotian/baseimage)上了。)*