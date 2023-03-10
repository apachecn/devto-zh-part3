# 面向 R 用户的 Docker 介绍

> 原文：<https://dev.to/colinfay/an-introduction-to-docker-for-r-users-28ad>

快速介绍如何使用 Docker 实现 r 中的再现性。

> 声明:这篇博文是针对初学者的 Docker 介绍，会走一些捷径；)

## Docker 是什么？

Docker 是“一种执行操作系统级虚拟化的计算机程序，也称为‘容器化’”[维基百科](https://en.wikipedia.org/wiki/Docker_%5C(software%5C))。正如维基百科关于科技文章的第一行一样，这句话对于不熟悉文章内容的人来说是晦涩难懂的。

所以，更简单的说， **Docker 是一个允许在你的机器(你的机器将被称为主机)**上操纵(启动和停止)多个操作系统(称为容器)的程序。想象一下，有 10 个装有不同风格的 Linux 的 RaspberryPi，每个都专注于做一件简单的事情，你可以随时打开和关闭它；但是所有这些都发生在你的电脑上。

## 为什么 Docker & R？

Docker 设计用于将环境封装在一个图像/容器中。例如，这允许在 Macbook 上安装 Linux 机器，或者当您的主计算机安装了 R 3.5 时，安装 R 3.3 的机器。这也意味着**你可以使用旧版本的包来完成特定的任务，同时仍然保持你机器上的包是最新的**。

这样，您可以“解决”依赖性问题:如果您担心当包更新时依赖性会破坏您的分析，那么构建一个容器，它将**总是**具有您想要的软件版本:无论是 Linux、R 还是任何包。

## Docker 图片 vs Docker 容器

在你的机器上，你需要两样东西:图像和容器。**镜像是操作系统的定义，而容器是镜像的实际运行实例**。您只需要安装一次映像，而容器将在您需要这个实例时启动。当然，相同图像的多个容器可以同时运行。

和 R 相比，这和安装 vs 加载一个包是一样的原理:一个包要下载一次，而你每次需要的时候都要启动。并且可以很容易地在几个 R 会话中同时启动一个包。

继续这个比喻:当我们`install.packages()`时，我们**建立**一个形象，当我们`library()`时，我们**运行**这个形象。

## Dockerfile

Docker 映像是从`Dockerfile`构建的。这个文件是配置文件，它描述了几件事情:从以前的 docker 映像构建这个文件，如何配置操作系统，以及当你使用容器时会发生什么。在某种意义上，这有点像 R 包的`DESCRIPTION` + `NAMESPACE`文件，它描述了哪些是对你的包的依赖，给出了元信息，并且声明了哪些功能和数据对使用这个包的用户`library()`是可用的。

所以，让我们为 R 建立一个非常基本的**`Dockerfile`，重点是再现性。想法是这样的:我今天有一个有效的分析(例如包含在一个`.R`文件中)，并且我想确定这个分析在将来会一直有效，不管所使用的包有什么更新。**

 **首先，为您的分析创建一个文件夹，以及一个 Dockerfile:

```
mkdir ~/mydocker
cd ~/mydocker
touch Dockerfile 
```

假设这是我们想要运行的分析内容，名为`myscript.R`，位于`~/mydocker`文件夹中:

```
library(tidystringdist)
df <- tidy_comb_all(iris, Species)
p <- tidy_stringdist(df)
write.csv(p, "p.csv") 
```

### `FROM`

每个`Dockerfile`都以一个`FROM`开始，它描述了我们正在从什么样的图像构建我们的图像。有很多官方的图像，你也可以从本地的构建。

这个`FROM`在某种程度上描述了你的图像的依赖性；就像在 R 中一样，当构建一个包时，您总是依赖于另一个包(仅仅是`{base}`包)。

如果你想要一个基于 R 的图像，Dirk Eddelbuettel 和 Carl Boettiger 正在维护 [rocker](https://hub.docker.com/u/rocker) ，这是一个你可以使用的 R 的 Docker 图像集合。基本的映像是`rocker/r-base`，但是我们想要的是我们的映像是可复制的:也就是说在我们运行它的任何时候以完全相同的方式重新运行。为此，我们将使用`rocker/r-ver`，这是包含 R 的固定版本(回到 3.1.0)的 Docker 映像，并且您可以像从特定日期开始一样运行(感谢 [Dirk Eddelbuettel](http://dirk.eddelbuettel.com/) 为我指出这一点)。

所以我们要做的是查找对应于我们想要的 R 版本的图像。你可以通过
获得你当前的 R 版本

```
R.Version()$version.string

## [1] "R version 3.4.4 (2018-03-15)" 
```

所以，让我们从
开始`Dockerfile`

```
FROM rocker/r-ver:3.4.4 
```

### `RUN`

一旦我们得到了这些，我们将添加一些`RUN`语句:这些是模仿命令行命令的命令。我们将创建一个目录来接收我们的分析。

```
FROM rocker/r-ver:3.4.4

RUN mkdir /home/analysis 
```

### 安装我们的软件包

让 R 执行某个东西的命令，来自终端，是`R -e "my
code"`。让我们用它来安装我们的脚本依赖项，但是从一个特定的日期开始。我们将模仿从特定日期构建时`rocker/r-ver`的工作方式:将`options("repos")`设置为这个特定日期，使用 MRAN 映像:换句话说，使用类似`https://mran.microsoft.com/snapshot/1979-01-01`的回购 url。

```
FROM rocker/r-ver:3.4.4

RUN mkdir /home/analysis

RUN R -e "options(repos = \
  list(CRAN = 'http://mran.revolutionanalytics.com/snapshot/2019-01-06/')); \
  install.packages('tidystringdist')" 
```

### 先不说:用`ARG`让它更可编程

在我们最后的`Dockerfile`中，日期不能在构建时修改——如果我们使用一个`ARG`变量，我们可以改变它，这个变量将在我们使用`--build-arg WHEN=`
执行`docker build`时设置

```
FROM rocker/r-ver:3.4.4

ARG WHEN

RUN mkdir /home/analysis

RUN R -e "options(repos = \
  list(CRAN = 'http://mran.revolutionanalytics.com/snapshot/${WHEN}')); \
  install.packages('tidystringdist')" 
```

这里，将被安装到机器中的`{tidystringdist}`将是我们在构建容器时指定的日期，即使我们在一年、两年或四年内构建这个映像。

### `COPY`

现在，我需要将我的分析脚本从我的机器(主机)中获取到容器中。为此，我们需要使用`COPY localfile
pathinthecontainer`。注意，在这里，`myscript.R`必须和你电脑上的`Dockerfile`在同一个文件夹中。

```
FROM rocker/r-ver:3.4.4

ARG WHEN

RUN mkdir /home/analysis

RUN R -e "options(repos = \
  list(CRAN = 'http://mran.revolutionanalytics.com/snapshot/${WHEN}')); \
  install.packages('tidystringdist')"

COPY myscript.R /home/analysis/myscript.R 
```

### `CMD`

现在，`CMD`，每次启动 docker 时要运行的命令。我们想要的是`myscript.R`被采购。

```
FROM rocker/r-ver:3.4.4

ARG WHEN

RUN mkdir /home/analysis

RUN R -e "options(repos = \
  list(CRAN = 'http://mran.revolutionanalytics.com/snapshot/${WHEN}')); \
  install.packages('tidystringdist')"

COPY myscript.R /home/analysis/myscript.R

CMD R -e "source('/home/analysis/myscript.R')" 
```

## 构建并运行

### 打造

记住我们想要的:一个像我们今天仍然存在一样运行分析的图像。为此，我们将使用`docker build`的`--build-arg WHEN=`参数。在`=`之后，输入您希望分析开始的日期。

现在，去建立你的形象吧。从您的终端，在 Dockerfile 所在的目录中，运行:

```
docker build --build-arg WHEN=2019-01-06 -t analysis . 
```

`-t name`是镜像的名称(这里是解析)，`.`表示将在当前工作目录下构建`Dockerfile`。

### `run`

然后，只需启动:

```
docker run analysis 
```

你的分析将会运行🎉！

## 导出容器内容

现在要做的一件事是:你想在你的容器之外访问你的分析(这里是`p.csv`)所创建的东西；即在主机上。因为是的，就目前而言，容器中发生的一切都停留在容器中。**所以我们需要的是让 docker 容器和主机共享一个文件夹。为此，我们将使用所谓的卷，这是(粗略地说)，一种告诉 Docker 容器使用来自主机的文件夹作为容器内的文件夹**的方式。

这样，容器在文件夹中创建的所有内容都将在容器关闭后保留。为此，我们将在运行容器时使用-v 标志，带有`path/from/host:/path/in/container`。此外，创建一个文件夹来接收两者中的结果:

```
FROM rocker/r-ver:3.4.4

ARG WHEN

RUN mkdir /home/analysis

RUN R -e "options(repos = \
  list(CRAN = 'http://mran.revolutionanalytics.com/snapshot/${WHEN}')); \
  install.packages('tidystringdist')"

COPY myscript.R /home/analysis/myscript.R

CMD cd /home/analysis \
  && R -e "source('myscript.R')" \
  && mv /home/analysis/p.csv /home/results/p.csv

mkdir ~/mydocker/results 
docker run -v ~/mydocker/results:/home/results analysis 
```

等待计算完成，然后…

```
ls ~/mydocker/results  
p.csv 
```

🤘

## 下一步怎么办？

所以现在，每次你启动这个 Docker 图像，分析将被执行，你将得到结果。没有依赖的问题:软件包将总是从你想要的那一天开始安装。不过，这可能会运行得有点长，因为每次运行容器时都会安装包。但是正如我在免责声明中所说的，这是对 Docker、R 和可再现性的基本介绍，所以目标更多的是让初学者使用 Docker:)

您可以做的其他事情包括:

*   如果您希望您的分析基于包版本而不是基于时间的安装，请使用`remotes::install_version()`。<！-结束列表- >

```
FROM rocker/r-ver:3.4.4

RUN R -e "install.packages('remotes'); \
  remotes::install_version('tidystringdist', '0.1.2')"

... 
```

*   使用 Volume 技巧将数据**放入容器**中，这样任何数据都将在完全相同的环境中进行分析。

和其他很酷的东西，但那是另一篇博文；)

## 了解更多 Docker

一些资源:

*   [摇杆官网](https://www.rocker-project.org/)
*   [Rocker 简介:R 的 Docker 容器](https://journal.r-project.org/archive/2017/RJ-2017-065/index.html)
*   [用户的诺姆·罗斯-多克尔](https://github.com/noamross/nyhackr-docker-talk)
*   [生产就绪 R:开始使用 R 和 docker](https://www.youtube.com/watch?v=lfG8cTqRRNA)
*   [带 R 和 Docker 的应用](https://www.youtube.com/watch?v=JOIKy_89c-o)
*   [初学者 Docker](https://docker-curriculum.com/)**