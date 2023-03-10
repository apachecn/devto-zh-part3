# 如何在 Windows 10 版本 1809 中使用 portainer

> 原文：<https://dev.to/will_huang/how-to-use-portainer-in-windows-10-version-1809-46j2>

[![image](img/30c82762730e7fcedeecf45aed8c3b54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YOEUlGU7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1u6gtutwy0z9afgghchp.jpg) 照片由 [chuttersnap](https://unsplash.com/photos/kyCNGGKCvyw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 于 [Unsplash](https://unsplash.com/search/photos/container?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

要在 Windows 容器中运行一个 [portainer](https://www.portainer.io/) 容器，使用下面的命令非常简单。

```
docker volume create portainer_data
docker run -d -p 9000:9000 --name portainer --restart always -v \\.\pipe\docker_engine:\\.\pipe\docker_engine -v portainer_data:C:\data portainer/portainer 
```

但是最没有记录的笔记是:

> 如果您刚刚在 Windows 10 版本 1809 上安装了 Docker Desktop for Windows，您必须重新启动 Docker 服务(`com.docker.service`)才能使 **portainter** 工作！

```
Restart-Service com.docker.service 
```

> 重新启动电脑也一样。

重启后， **portainer** 将按预期工作。