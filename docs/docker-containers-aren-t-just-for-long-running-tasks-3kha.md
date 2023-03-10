# Docker 容器不仅仅用于长时间运行的任务

> 原文：<https://dev.to/staff0rd/docker-containers-aren-t-just-for-long-running-tasks-3kha>

Docker 可以轻松运行长期运行的服务，如 [sql server](https://hub.docker.com/_/microsoft-mssql-server) 或 [nzbget](https://hub.docker.com/r/linuxserver/nzbget/) ，但是您知道吗，docker 也是执行短期工作负载的绝佳选择？这可能是[编译一个 dotnet 核心程序](https://hub.docker.com/_/microsoft-dotnet-core)而没有安装 sdk，或者[构建你的 jekyll 博客](https://hub.docker.com/r/jekyll/jekyll/)而没有安装 ruby。

为了演示一个简短的任务用例以及创建自己的 docker 容器的简易性，在这篇文章中，我将构建一个容器，让你和我通过命令行在 pastebin.com 推送日志文件或任何其他文本内容。

## 从任何虚拟机发布日志

Pastebin 允许您快速在线发布和共享文本文件。他们提供了一个 [API](https://pastebin.com/api) ，所以你可以自动化这个过程，并且有用 PHP 编写的[使用它的例子。在我的用例中，我需要在 Azure 中发布在瞬态虚拟机上创建的日志，但我不想安装 PHP 或其依赖项，也不想安装发布日志所需的脚本。这些虚拟机已经有 docker 可用，所以我的意图是将 Pastebin 的 PHP 代码打包到一个容器中，并将该容器推送到 docker hub。](https://pastebin.com/api#2)

这种方法将允许我在任何虚拟机上使用`docker run staff0rd/pastebin`，并且容器的下载和执行将被自动处理。

## 现在就试试吧

现在，我已经构建并发布了这个容器，您也可以访问这个功能，而不必进行下面的任何工作。如果你已经安装了 docker，你现在就可以试试这个:

```
docker run -it staff0rd/pastebin -k <yourDevApiKey> "post this content to pastebin" 
```

如果你愿意，你可以扩展、修改和使用源代码来创建你自己的，或者，[打开一个 PR 并在这个上合作](https://github.com/staff0rd/pastebin/pulls)。[图像在 dockerhub](https://hub.docker.com/r/staff0rd/pastebin) 上，[源在 github](https://www.github.com/staff0rd/pastebin) 上。

## 创建 Dockerfile

[Dockerfile](https://github.com/staff0rd/pastebin/blob/master/Dockerfile) 代表我们将要去`docker build`的图像，并且是一个很好的例子，说明利用别人已经为你做的艰苦工作来创建你自己的容器是多么微不足道。如果我想托管或运行一个 php 脚本，我将把我的`Dockerfile`基于[官方 PHP 镜像](https://hub.docker.com/_/php)。在这种情况下，正如我们将在下面看到的，我最终引入了一个 php 依赖管理器，所以我将把我的图像建立在那个图像的基础上，而那个图像又是基于 php 图像的。

```
FROM composer:1.8
COPY ./src /app
WORKDIR /app
RUN composer install
ENTRYPOINT [ "php", "./pastebin.php" ] 
```

在上面的五行中，我已经为容器设置了基本映像，复制了脚本并设置了工作目录，执行了依赖项管理器的 install 命令，并告诉容器在启动时运行脚本。我可以运行下面的命令，容器就可以在我构建它的机器上使用了。

```
docker build -t staff0rd/pastebin . 
```

然后我可以运行这个命令，这个容器可以从互联网上的任何一台机器
被`pull` ed 或者`run`

```
docker push staff0rd/pastebin 
```

## 构建脚本

这个练习的大部分是构建在容器中复制的脚本[。对于实际的执行，我将只使用由 pastebin](https://github.com/staff0rd/pastebin/blob/master/src/pastebin.php) 举例的[代码，但是因为这个脚本将被封装在容器内，它需要的参数在容器外并不明显。为了解决这个问题，我将使用](https://pastebin.com/api#2)[突击队](https://github.com/nategood/commando)来构建一个不错的 CLI 助手。作为一个依赖项，可以通过 php 依赖管理器 [Composer](https://getcomposer.org/) 安装 commando，因此我将 Dockerfile 基于 Composer 而不是 php。

至少，突击队给了我一个很好的`--help`输出，所以脚本在容器中实现的选项不再是秘密:

```
λ docker run -it staff0rd/pastebin --help

Examples:

docker run -it staff0rd/pastebin -k <devKey> "paste this text to pastebin!"
    Paste the given text to pastebin

cat myfile.log | docker run -i staff0rd/pastebin -k <devKey>
    Paste the contents of myfile.log to pastebin

docker run -it staff0rd/pastebin -k <devKey> -u <userName> --password <password>
    Retrieve a userKey to use associate pastes with your user

arg 0
     Content to to paste

-k/--devkey <argument>
     Required. Your api developer key

--help
     Show the help page for this command.

-j/--userkey <argument>
     Your user key

-n/--name <argument>
     Name or title of your paste

-p/--public
     Make this paste public.  Default is unlisted.

--password <argument>
     Your pastebin password

-u/--username <argument>
     Your pastebin user name 
```

## 包装完毕

我已经将[映像](https://hub.docker.com/r/staff0rd/pastebin)推送到 docker hub 并启用了自动构建，因此对 [git repo](https://github.com/staff0rd/pastebin) 的任何更新都将导致在 docker hub 上构建一个新映像。

否则，我可以(你也可以，因为 docker)在任何支持 docker 的主机上将任何文件发送到 pastebin，如下所示:

```
cat sendThisFile.txt | docker run -i staff0rd/pastebin -k thisIsMyDevKey 
```

我希望这篇文章既说明了创建自己的 docker 容器是多么容易，又说明了 docker 对于执行和分发/部署短期运行的任务来说是一个多么好的工具。