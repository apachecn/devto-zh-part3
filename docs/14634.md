# 使用树莓作为 gitlab runner(带 docker)

> 原文：<https://dev.to/konstantin/using-raspberry-as-gitlab-runnerwith-docker-13ei>

通过向 Raspberry 引入 [USB boot](https://www.raspberrypi.org/documentation/hardware/raspberrypi/bootmodes/msd.md) 选项，您可以将它与外部 SSD 驱动器配合使用，因此您不需要不时切换损坏的 SD 卡并重新配置整个系统。

随着 SSD 的安装，raspberry 成为一个有趣的选择，可以用作外部 [`gitlab runner`](https://docs.gitlab.com/runner/#install-gitlab-runner) 。

现在，作为遗嘱执行人，你可能会选择`docker`,因为它允许你运行任何你想运行的软件，而不需要把它安装在你的树莓上。

## 但有一条军规

由于 Raspberry 没有 x64 CPU，而是 ARMvX，你会发现你的一些 gitlab 任务会失败，并显示如下日志:

```
standard_init_linux.go:190: exec user process caused "exec format error"
standard_init_linux.go:190: exec user process caused "exec format error"
ERROR: Job failed: exit code 1 
```

Enter fullscreen mode Exit fullscreen mode

这意味着你必须去你的树莓网站上建立你的本地形象，就像这样

```
docker build -t node . 
```

Enter fullscreen mode Exit fullscreen mode

前提是你准备了`Dockerfile`:

```
FROM node:8 
```

Enter fullscreen mode Exit fullscreen mode

然后你需要告诉你的 docker 执行器使用本地图像，而不是来自 Dockerhub:

```
[[runners]]
  name = "raspberrypi"
  ...
  executor = "docker"
  [runners.docker]
    ...
    pull_policy = "if-not-present" 
```

Enter fullscreen mode Exit fullscreen mode