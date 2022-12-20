# 从容器发展而来

> 原文：<https://dev.to/aghost7/developing-from-containers-42fp>

使用容器进行开发已经成为一种普遍的做法。开发中常见的用例是运行应用程序所需的服务。比如安装 Redis，MongoDB，甚至 Elasticsearch 之类的东西。大多数时候，开发人员依靠`docker-compose`来定义应用程序所需的整套服务。

我通过用给定的语言创建包含我需要开发的所有东西的容器图像，做了进一步的工作。这包括(但不限于):

*   代码编辑器
*   调试器
*   仿形铣床
*   跟踪工具
*   包管理器
*   构建工具

## 动机

### OS 独立

即使您使用的是 Docker for Mac，您仍然有一个基于 Linux 的开发环境。这对熟悉 Linux 很有好处，然后可以应用它来创建真正的生产容器映像(以及许多其他东西)。

### 安装速度

与使用安装程序脚本相比，容器映像(尤其是 docker 映像)在新机器上运行起来非常快。这是因为您只需要下载带有预编译二进制文件的映像。例如，我必须从源代码中为我的一个图像编译`cquery`服务器。如果我使用的是安装程序脚本，我必须要么自己托管编译好的二进制文件，要么在每台机器上编译它，这需要花费相当长的时间(因为你需要下载 clang 和所有这些东西)。

### 便携性

如果使用安装程序脚本，在不同的机器上可能无法工作，因为某些包是以不同的方式安装的。例如，有人可能使用了一个 [PPA](https://askubuntu.com/questions/4983/what-are-ppas-and-how-do-i-use-them) 安装在一台机器上，而在另一台机器上，它被安装为`.deb`档案或使用标准软件源。相比之下，一旦构建了容器映像，它将*在几乎任何机器上*工作。

### 再现性

形象一旦建立起来，就不会凭空冒出来。这与安装程序脚本不同；当我只是使用 git 存储库来存储配置和脚本时，我遇到过许多情况，它会根据安装程序运行的时间而中断。

## 告诫

### 不是 Docker 的常见用例

docker 对这种工作流程的支持并不是很好。docker 的一些问题花了几年时间[到](https://github.com/moby/moby/issues/2259) [修复](https://github.com/moby/moby/issues/8755)。

### 还需要一些胶水代码

有几样东西需要从主机映射到容器中。我写了一个工具来简化这个过程，叫做 [slipway](https://github.com/AGhost-7/slipway) 。在我编写这个工具之前，我会使用一个 shell 脚本来初始化容器。

如果您没有使用 Linux 作为主机操作系统，事情会变得更加复杂。

## 剖析容器化的开发环境

### 0。选择一个基础图像

对于外行来说，构建 docker 映像需要一个配置文件(一个 [Dockerfile](https://docs.docker.com/engine/reference/builder/) )来定义要在容器中执行的步骤。在所有这些步骤运行之后，容器作为一个新的映像被“提交”。

大多数图像是基于另一个图像。我建议从 Ubuntu 开始，因为它很受欢迎。

在空目录
中添加一个名为`Dockerfile`的文件

```
FROM ubuntu:bionic 
```

Enter fullscreen mode Exit fullscreen mode

### 1。设置您的用户

该映像仅由根用户提供。我们需要创建一个具有适当权限的用户，以便在容器内部运行。

```
# Feel free to change this to whatever your want
ENV DOCKER_USER developer

# Create user with passwordless sudo. This is only acceptable as it is a
# private development environment not exposed to the outside world. Do 
# NOT do this on your host machine or otherwise.
RUN apt-get update && \
    apt-get install -y sudo && \
    adduser --disabled-password --gecos '' "$DOCKER_USER" && \
    adduser "$DOCKER_USER" sudo && \
    echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
    touch /home/$DOCKER_USER/.sudo_as_admin_successful && \
    rm -rf /var/lib/apt/lists/*

USER "$DOCKER_USER"

WORKDIR "/home/$DOCKER_USER" 
```

Enter fullscreen mode Exit fullscreen mode

### 2。基本工具

Ubuntu core(这是我们的映像所基于的)并不包含太多内容，因为它是为“现成的”应用程序而构建的。让我们添加一些基本的开发包:

```
RUN yes | sudo unminimize && \
    sudo apt-get install -y man-db bash-completion build-essential curl openssh-client && \
    sudo rm -rf /var/lib/apt/lists/* 
```

Enter fullscreen mode Exit fullscreen mode

### 3。多路复用(可选)

如果您没有使用像 VSCode 或 Webstorm 这样的 GUI 编辑器，您可能会希望有一个程序能够接受一个 shell 会话并将其分成多个会话。这被称为[终端多路复用器](https://en.wikipedia.org/wiki/Terminal_multiplexer)。我更喜欢 [tmux](https://github.com/tmux/tmux) 。

```
RUN sudo apt-get update && \
    sudo apt-get install -y tmux && \
    sudo rm -rf /var/lib/apt/lists/* 
```

Enter fullscreen mode Exit fullscreen mode

我可以在这里讨论很多定制选项，但是为了使教程简短，我将跳过这一步。[请随意查看我的项目](https://github.com/AGhost-7/docker-dev)以获得开发环境的想法。

### 4。安装您选择的编辑器

现在我们需要一些东西来编辑源代码。我可以推荐 Neovim，但是任何编辑器都可以。

```
RUN sudo apt-get update && \
    sudo apt-get install -y neovim && \
    sudo rm -rf var/lib/apt/lists/* 
```

Enter fullscreen mode Exit fullscreen mode

如上所述，我跳过定制选项。

### 5。安装运行时

在本例中，我们将安装 NodeJs，因为网站上的大多数开发人员都在使用它。如果您在这一步使用 Python 或其他语言，您可以安装它。

```
RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
ENV NVM_DIR /home/$DOCKER_USER/.nvm

RUN . "$NVM_DIR/nvm.sh" && \
    nvm install --lts && \
    nvm alias default stable 
```

Enter fullscreen mode Exit fullscreen mode

在这一步，为您选择的语言安装任何附加工具。

### 建筑，然后运行

您需要做的就是在运行之前构建映像。

在您的终端中，运行:

```
docker build -t development-environment . 
```

Enter fullscreen mode Exit fullscreen mode

运行您的环境的最后一个命令可能会根据您要转移的内容而有所不同。下面是一个命令示例:

```
docker run --rm -ti \
    -v $HOME/workspace:/home/developer/workspace \
    development-environment bash 
```

Enter fullscreen mode Exit fullscreen mode

关于本教程的更详细版本，请参见我的存储库的[教程](https://github.com/AGhost-7/docker-dev/tree/master/tutorial)部分。