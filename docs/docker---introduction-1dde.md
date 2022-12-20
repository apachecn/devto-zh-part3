# Docker -简介

> 原文：<https://dev.to/johannestegner/docker---introduction-1dde>

## docker 是什么？

码头工人自己说码头工人是

> 世界领先的软件容器平台。

现在，通过阅读 docker 提供的关于他们平台的信息，我确信了解
容器的人会很好地理解这个主题，但是以前从未使用过这样的东西的人，可能有点难以理解。

Docker 是一个运行容器的软件。容器是一个“独立的包”,包括一个软件。容器不包括完整的操作系统，而只是运行软件本身所需的部分。

这使得一个容器比传统的虚拟机小得多，资源消耗也少得多。

“解释”docker 最简单的方法就是说它很像一个虚拟机。这并不完全正确，但我们还是接受了，因为这样任何习惯于 VM 的人都会感到安全！

现在，容器最好的一点是，它在所有运行它的计算机上都是完全一样的，不管是什么操作系统！这意味着它可以在 linux 计算机上运行，就像在 windows 或 mac 上一样！

## 使用 docker

首先，你需要安装 docker。我不打算解释这一步，但我认为你可以自己管理！

您运行的每个容器都将使用一个`docker image`。图像是容器将基于的预构建包。图像可以在本地构建，也可以使用托管的替代方案，如 [docker hub](https://hub.docker.com/) 。

构建你自己的映像比使用预构建的映像更能让你定制容器，但是在第一部分我们将只使用来自 docker hub 的
映像，而不是马上开始编写 docker 文件！

*注意:我将在本系列的后续文章中介绍 Dockerfile 和`docker build`命令。*

### 检索预建图像

在 docker hub 上，你可以找到你想要的大多数东西的图片。在我们的第一个例子中，
我们将使用名为 [`hello-world`](https://hub.docker.com/_/hello-world/) 的官方 docker 教程图像。我们要做的第一件事是将图像提取到计算机和您当地的 docker 服务中。

不！这不是通过使用`curl`完成的，它已经内置在 docker 引擎中了！

使用`docker pull`命令提取图像:

```
docker pull hello-world 
```

Enter fullscreen mode Exit fullscreen mode

*注意:也可以只运行图像，docker 会获取它，但是拉是一个好的开始。*

在网上拉一个 docker 图像可能需要一些时间，但是这个图像应该是速度较快的(它的大小不到一 kb)。

当我们得到图像的时候我们可以启动一个基于图像的容器。

### 运行图像

第一次启动一个容器是通过使用`docker run`命令来完成的，有几个重要的参数可以或者甚至应该应用。

```
docker run --rm --name hello hello-world 
```

Enter fullscreen mode Exit fullscreen mode

当像这样运行命令时，我们将启动一个我们命名为`hello`的容器，当容器由于某种原因停止时，它将被删除
我们已经通过`--rm`参数确保了这一点。如果您希望容器在停止后继续存在(以便可以再次启动)，只需省略`--rm`参数！

### 进一步深入兔子洞

运行这样一个 hello-world 应用程序可能不是很有趣，所以我们可以继续做一些更有用的事情。

在 composer 项目中安装依赖关系怎么样？我打赌那会很有用！

你不需要在本地安装 composer 或者任何东西，所有的工作都在容器中完成。

首先我们需要一个 composer 文件，原因就是这个项目的“基础”。

示例:

```
{  "name":  "my/project",  "description":  "Just testing docker!",  "require":  {  "jitesoft/exceptions":  "2.2.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

将文件放在一个空目录中，并在那里打开终端。

创建作曲家文件后，我们需要找到一个我们喜欢的作曲家图像。我个人一直用自己做的一个可以用`docker pull jitesoft/composer`取来的。

当我们得到作曲家的形象，我们将希望运行的形象...但是我们需要以某种方式将文件添加到图像中...Docker 和 volumes(文件系统)是一种特殊的东西，有很多东西需要学习，但在这种情况下，我们将采用简单的方法，只共享一个卷！

与容器共享卷是在容器启动时完成的，命令如下:

```
docker run --rm -v $(pwd):/app jitesoft/composer install --no-interaction 
```

Enter fullscreen mode Exit fullscreen mode

这将启动一个基于`jitesoft/composer`映像的容器，该映像将当前目录(`$(pwd)`将把它放入命令)到容器中的`/app`目录，
它将运行`composer install --no-interaction`并安装所需的依赖项，然后它将停止并清理自己(由于`--rm`而删除)。

### 更多运行参数！

在`docker run`命令中还有一些我认为非常有用的参数:

**超然:**

```
-d 
```

Enter fullscreen mode Exit fullscreen mode

`-d`参数告诉 docker 容器你想从它分离。这在运行预期寿命较长的容器时会很有用。

**港口:**T2】

```
-p <local-port>:<container-port>
--port=<local-port>:<container-port> 
```

Enter fullscreen mode Exit fullscreen mode

使用-p 参数，您可以将一个端口从容器映射到本地计算机。比方说，您想要打开容器端口 80(标准 http 端口)到您的本地机器端口 8080，您可以写如下内容:

```
docker run -p 8080:80  nginx 
```

Enter fullscreen mode Exit fullscreen mode

**记忆:**

```
--memory=<size> 
```

Enter fullscreen mode Exit fullscreen mode

使用这个参数，您可以限制容器允许使用的内存量。我总是试图设置这个参数，以确保如果我犯了非常严重的错误，计算机不会耗尽内存！(是的，它已经发生了，在一个活动的服务器上！).

你可以在[官方文档](https://docs.docker.com/)找到所有的论据。

*这篇文章是从我的博客交叉发布的，可以在这里找到:[https://jite.eu/2017/9/1/docker-intro/](https://jite.eu/2017/9/1/docker-intro/)(包括一堆其他的文章！).*