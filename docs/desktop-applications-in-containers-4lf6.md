# 容器中的桌面应用程序

> 原文：<https://dev.to/j6s/desktop-applications-in-containers-4lf6>

在过去的几周里，我一直在玩 docker，将应用程序(包括它们带来的所有依赖项和 cruft)封装到一种“子系统”中的想法，这种“子系统”只与主机有明确定义的共享资源，这不仅仅是我在考虑服务器和开发环境时想到的。我已经看到了现代闭源应用程序的一个趋势:它们都开始为你的包管理器提供自己的库，而不是麻烦官方的。仅仅为了安装 spotify 或 slack 而在你的包管理器中添加一个第三方库是一个信任问题——第三方库的列表应该是最小的。

## 将其归档

因为在 Linux 中，一切都是文件，docker 可以将文件装入容器，所以将应用程序装入容器的想法并不牵强:只需将正确的一组套接字装入容器，容器化的应用程序就能与系统资源对话。

### X11

为了实现图形输出，需要做三件事:

1.  主机必须允许到 X11 的远程连接(因为容器被视为远离 X11)。这可以通过使用`xhost local:root`来完成
2.  X11 插座(位于`/tmp/.X11-unix`下方)需要安装到容器上
3.  `$DISPLAY`环境变量需要传递给容器

为了测试到 X11 的连接是否正常工作，可以执行下面的代码来设置一个包含`xeyes`应用程序的简单容器:

```
#!/bin/bash

docker build -t 'thej6s/xeyes' - << __EOF__
FROM debian
RUN apt-get update && apt-get install -y x11-apps
ENV DISPLAY $DISPLAY
CMD xeyes
__EOF__

XSOCK=/tmp/.X11-unix
xhost local:root
docker run -v $XSOCK --net host 'thej6s/xeyes' 
```

Enter fullscreen mode Exit fullscreen mode

[![xeyes running inside of docker](img/dbf6827a389734ec40885dcc9494dd26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6-xu06Wd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thej6s.com/article-images/xeyes-docker.png)

### 声音:Alsa

桌面应用程序可能要使用的下一个大型硬件设备是声音输入和输出。最简单的方法是让来宾使用 alsa 直接访问音频设备来处理所有与音频相关的任务。这将类似于上面的 X11 插座-但使用的是`/dev/snd`设备。

这是可行的——但是有一个主要的缺点:它将所有对音频的控制都放在了容器中。想象一下，不得不 ssh 到多个容器中来调节您的音量。

### 声音:脉冲音频

大多数发行版和大多数用户都使用 pulseaudio 来配置和管理他们的声音环境。dockerized 应用程序应该播放到全局 pulse 实例中，而不是直接访问音频设备。通过这种方式，所有 dockerized 应用程序仍然可以通过在主机上使用诸如`pavucontrol`这样的工具来管理。

然而，这带来了一些困难:- Pulseaudio 作为用户服务启动，并且绑定到当前机器和用户

为了克服这些障碍，需要采取几个步骤:1 .创建一个被 pulseaudio IPC 接受的环境——在容器中创建一个用户，其 uid 与主机系统上的用户相同——将`/etc/machine-id`挂载到容器 2 中。将脉冲音频插座(`/run/user/${UID}/pulse`)安装到容器中

下面在一个支持声音的 pulseaudio 的容器中启动 Firefox:

```
XSOCK=/tmp/.X11-unix
UID=$(id -u)

docker build -t 'j6s/firefox' - << __EOF__
FROM debian

RUN apt-get update && apt-get install -y firefox-esr

ENV HOME /home/user
RUN useradd -u ${UID} \
        --create-home --home-dir \
        /home/user user && \
    usermod -a -G audio user && \
    chown -R user:user /home/user

USER user
WORKDIR /home/user
CMD firefox-esr
__EOF__

docker run --rm \
    -v $XSOCK:$XSOCK \
    -v /etc/machine-id:/etc/machine-id \
    -v /run/user/${UID}/pulse:/run/user/${UID}/pulse \
    -e "DISPLAY=${DISPLAY}" \
    --name firefox \
    'j6s/firefox' \ 
```

Enter fullscreen mode Exit fullscreen mode

[![Firefox running inside of a container](img/a092b65452b6edae68ee83ed3acd9b88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UjsXDd26--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thej6s.com/article-images/firefox-docker.png)

## Spotify

让我们回顾一下我是如何开始这篇文章的:封装第三方闭源应用程序的想法吸引了我——这就是本文的要点。Spotify 是最简单的例子，因为它只需要 X11 和声音输出。

```
#!/bin/bash

XSOCK=/tmp/.X11-unix
UID=$(id -u)
DIR=$(pwd)

function run {
    echo -e "$ $@"
    eval $@
}

run mkdir -p data/config data/cache
run chown -R ${UID} data/
run chmod -R 755 data/

run docker build -t 'j6s/spotify' - << __EOF__
FROM debian

RUN apt-get update && apt-get install -y gpg
RUN apt-key adv \
        --keyserver hkp://keyserver.ubuntu.com:80 \
        --recv-keys 931FF8E79F0876134EDDBDCCA87FF9DF48BF1C90 && \
    echo 'deb http://repository.spotify.com stable non-free' > /etc/apt/sources.list.d/spotify.list && \
    apt-get update &&\
    apt-get install -y -q --no-install-recommends spotify-client

RUN apt-get install -y -q --no-install-recommends \
        pulseaudio \
        libgl1-mesa-dri \
        libgl1-mesa-glx

ENV HOME /home/user
RUN useradd -u ${UID} --create-home --home-dir /home/user user && \
    usermod -a -G audio user && \
    chown -R user:user /home/user

USER user
WORKDIR /home/user
CMD spotify
__EOF__

run docker run --rm \
    -v $XSOCK:$XSOCK \
    -v /etc/machine-id:/etc/machine-id \
    -v /run/user/${UID}/pulse:/run/user/${UID}/pulse \
    -v ${DIR}/data/config:/home/user/.config \
    -v ${DIR}/data/cache:/home/user/.cache \
    -e "DISPLAY=${DISPLAY}" \
    --name spotify \
    'j6s/spotify' \ 
```

Enter fullscreen mode Exit fullscreen mode

[![Spotify running inside of a docker container](img/3b5604f267a8ea4f054896e2f486dd84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z7ijg4eF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thej6s.com/article-images/spotify-docker.png)

## 进一步阅读

下面的 blogpost(尤其是作者的 github 库)对于运行在容器内部的桌面应用程序来说非常有趣:[https://blog . Jess fraz . com/post/docker-containers-on-the-desktop/](https://blog.jessfraz.com/post/docker-containers-on-the-desktop/)

我写了一篇后续文章:[使用 docker 封装非自由应用程序](https://thej6s.com/articles/2019-02-20__encapsulating-nonfree-applications-using-docker/)