# 码头工人和舞蹈演员

> 原文：<https://dev.to/davorg/docker-and-dancer-4m82>

我已经开始着手我在[上一篇文章](https://dev.to/davorg/moving-into-the-cloud-4p4/)中提到的项目。我决定，不管我要采取什么方法来将这个网站迁移到云中，我肯定要在 Docker 容器中运行主要应用程序——所以这就是我本周一直关注的事情。

原来我以前也试过这个。有一个现有的 [Github repo](https://github.com/davorg/succession-docker/) 包含一个`Dockerfile`应该为应用程序构建一个图像。看起来好像是我从[这篇博文](https://robn.io/docker-perl/)中抄袭来的。我不记得为什么我停止了这方面的工作，但是让它运行起来很容易。

下面是当前版本的`Dockerfile`:

```
FROM        perl:latest
MAINTAINER  Dave Cross dave@perlhacks.org

RUN apt-get install openssl libssl-dev libz-dev libexpat1-dev
RUN curl -L http://cpanmin.us | perl - App::cpanminus
RUN cpanm Carton Starman

RUN git clone http://github.com/davorg/succession.git
RUN cd succession && carton install --deployment

EXPOSE 1701

WORKDIR succession
CMD carton exec starman --port 1701 Succession/bin/app.psgi 
```

让我们更详细地讨论一下。

```
FROM        perl:latest 
```

Docker 图像倾向于建立在其他图像之上。我选择了一个包含 Perl 最新版本的图像。

```
MAINTAINER  Dave Cross dave@perlhacks.org 
```

当我对这张图片满意时，我会把它上传到一个公共 Docker hub。这条线路将允许人们就此事与我联系。

```
RUN apt-get install openssl libssl-dev libz-dev libexpat1-dev 
```

`RUN`在构建映像时运行命令。在这里，我们正在加载一些相当标准的库，这些库是我稍后将加载的一些 Perl 模块的基础。实际上，我不确定这些是必要的；它们很可能包含在底层图像中。

```
RUN curl -L http://cpanmin.us | perl - App::cpanminus
RUN cpanm Carton Starman 
```

在这里，我正在安装`cpanm`，然后用它来安装`Carton`(一种安装已知版本的 Perl 模块的机制)和`Starman`(我们将用来运行我们的 web 服务的软件)。

```
RUN git clone https://github.com/davorg/succession.git 
```

在这一行，我们克隆了最新版本的 [Github repo，它包含了 web 服务的实际代码](https://github.com/davorg/succession.git)。

```
RUN cd succession && carton install --deployment 
```

在这里，我们切换到新克隆的子目录，并使用`carton`安装我们需要的所有 Perl 模块。

```
EXPOSE 1701 
```

这一行告诉 Docker 容器上的端口 1701 应该对外开放。

```
WORKDIR succession 
```

`WORKDIR`设置我们在容器启动时使用的工作目录。

```
CMD carton exec starman --port 1701 Succession/bin/app.psgi 
```

最后，`CMD`定义了使用新映像启动容器时运行的命令。这里我们使用一个非常标准的命令行来启动 Starman 下运行的 Dancer 应用程序。

创建了我们的`Dockerfile`之后，我们可以通过运行`docker build`命令将它转换成 Docker 映像:

```
$ docker build -t succession . 
```

`-t succession`给图像一个标签，`.`表示“使用当前目录中的`Dockerfile`”。

一旦命令完成，您将有一个新的图像。您可以使用命令`docker image ls`进行确认。

```
$ docker image ls
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
succession                latest              6a6fbfcbebbc        2 hours ago         1.17GB
perl                      latest              9a41aacaefa5        11 days ago         891MB 
```

但是拥有一个码头工人的形象只是成功的一半。我们现在需要创建并运行一个容器。我们可以使用`docker run`来实现这一点。天真的方法应该是这样的:

```
$ docker run succession 
```

这是可行的——但是它没有做任何有用的事情。原因很多。

首先，尽管我们已经要求 Docker 在容器上公开端口 1701(在`Dockerfile`中使用`EXPOSE`命令)，但是我们还没有对该端口做任何有用的事情。我们需要对`docker run`使用`-p`选项，以使该端口在主机系统上可见。所以我们的命令变成了:

```
$ docker run -p 1701:1701 succession 
```

这使得容器上的端口 1701 可用作主机上的端口 1701。

此时，我实际上能够连接到运行在容器上的 Dancer 应用程序(在 [http://localhost:1701/](http://localhost:1701/) )。当然，这是一条错误消息，但至少通讯正常。

这个错误消息很有趣。正如我们所见，`Dockerfile`使用一个名为 Carton 的 Perl 工具来确保安装了所有正确的库。它使用一个名为 [`cpanfile`](https://github.com/davorg/succession/blob/master/cpanfile) 的文件来计算出需要什么库。所有发生的事情就是我的`cpanfile`没有列出所有需要的东西。我承认它花了一些迭代才得到正确的结果！

还有最后一个问题。应用程序需要一个数据库，而`Dockerfile`中没有设置这个数据库。这没关系，因为我不希望数据库在同一个容器中运行。但是应用程序需要知道它的数据库在哪里。它通过查看一些环境变量来解决这个问题，这些变量没有在容器中设置。所以我不得不使用`-e`选项(多次)将主机的环境变量传递给容器。此时，`docker run`命令看起来有点复杂，所以我写了一个 bash 脚本来简化我的工作。

```
docker run --name succession \
  -e SUCC_DB_HOST \
  -e SUCC_DB_NAME \
  -e SUCC_DB_USER \
  -e SUCC_DB_PASS \
  -p 1701:1701 succession 
```

您会看到我还添加了`--name`来给容器命名。

看起来好多了。但是我还没有完全弄清楚所有的网络问题。数据库主机变量(`SUCC_DB_HOST`)被设置为`localhost`。在容器上，这是不对的。快速搜索一下谷歌，我发现选项`--network="host"`将允许容器从主机的角度看到网络，所以我只需将设置更改为`127.0.0.1`，突然间，一切都开始工作了。

这就是我目前的处境。经过上周几个小时的工作，我现在有了一个可以工作的 web 应用程序的容器版本。这让我很开心。我觉得这很可行。

所以，给任何可能正在阅读这篇文章的码头专家。我所做的是明智的还是我错过了一些明显的改进？欢迎所有建议。

**更新:**已经有两个人给了我一些非常有用的更新。看来我借了很多钱的那家银行不是很好。我会在接下来的几天里努力改进它——同时，请不要复制这个版本。