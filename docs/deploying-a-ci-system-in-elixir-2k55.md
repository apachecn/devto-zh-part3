# 在 Docker 中部署酏剂和 Docker

> 原文：<https://dev.to/bakenator/deploying-a-ci-system-in-elixir-2k55>

最近我一直在用 elixir 构建一个 CI(持续集成)应用。

SaaS 有很多好的选择，我为什么要这么做？为了好玩！

一路上，我发现了一些有趣的事情，我想我可以分享。这篇文章将只关注**药剂释放**和**停靠在**停靠栏。我将发布另一篇关于 elixir 应用程序本身的文章。

## CI App 是干什么的？

这是一个简单的 CI 应用程序。它等待 Github webhook 通知，下载并测试新代码，然后将结果发送回 Github。

使用 github 的细节在这里:[https://developer . github . com/v3/guides/building-a-ci-server/](https://developer.github.com/v3/guides/building-a-ci-server/)

## 它是如何工作的？

这就是事情开始变得棘手的地方。因为我们想要隔离被测试的代码，所以我们需要在 docker 容器中运行测试。

而且为了 CI 应用程序的可移植性，我们的 elixir 代码也需要在 docker 容器中运行。

这就给我们带来了终极的头脑风暴: **Docker in Docker！**

让我们直接开始吧。

## 记录一种药剂的应用

将 elixir 应用程序投入生产的最佳实践是使用内置的 Erlang 版本概念。对于 elixir 应用程序，这可以通过 Distillery 库轻松完成。[https://elixirschool.com/en/lessons/libraries/distillery/](https://elixirschool.com/en/lessons/libraries/distillery/)

一个小问题是，这个版本需要建立在一个与将要部署的操作系统相匹配的操作系统上。Docker 可以在这里提供帮助，因为它允许您在 docker 中构建您的 elixir 版本，以保证部署的操作系统与构建的操作系统完全相同。

关于这个主题的一个好帖子在这里:[https://medium . com/@ pentacent/getting-started-with-elixir-docker-982 e2a 16213 c](https://medium.com/@pentacent/getting-started-with-elixir-docker-982e2a16213c)
官方推荐的 docker 文件示例在这里:[https://hex docs . pm/distillery/guides/working _ with _ docker . html](https://hexdocs.pm/distillery/guides/working_with_docker.html)

## Docker in 仙丹 in Docker

请记住，我们希望带有 elixir 应用程序的操作系统包含 docker，以便我们可以启动容器进行测试。上面的指南展示了如何在 docker 中构建一个 elixir 版本，但没有展示 docker 中 docker 所需的操作系统。

那个 OS 叫“Docker 中的 Docker”！

官方形象是`docker:dind`，但 dockerhub 上有几个变种。我们将使用一个特定的版本`docker:18.09.0-dind`,这样我们就能确保它与用于构建该版本的`elixir:1.7.2-alpine`操作系统相匹配

在 hexdocs.pm 示例中，创建一个新的 Dockerfile，并将行
`FROM alpine:${ALPINE_VERSION}`
改为
`FROM docker:18.09.0-dind`

### 码头式 CMD Bug

通过反复试验，我发现行
`CMD trap 'exit' INT; /opt/app/bin/${APP_NAME} foreground`
覆盖了来自`docker:18.09.0-dind`图像的 CMD。
这会导致 docker 守护进程无法启动，从而使映像变得无用。

为了解决这个问题，我找到了什么是`docker:18.09.0-dind` CMD，并把它放在一个 shell 脚本中。

因此，在顶层项目目录中添加下面的文件`start.sh`
sleep 命令允许 docker 守护进程在 elixir 应用程序启动之前启动。

```
#!/bin/sh
dockerd-entrypoint.sh & 
sleep 10
/opt/app/bin/ci foreground 
```

然后将 DockerFile 的结尾更新为

```
COPY ./start.sh .
RUN ["chmod", "+x", "start.sh"]
ENTRYPOINT /start.sh 
```

## 内坞形象

我们已经在一张`docker:dind`图片上整理出了运行 elixir 应用程序的容器。现在让我们准备测试将要运行的 docker 映像。

在`test_runner`
创建一个文件夹，在这个文件夹中我的 Dockerfile 看起来像这样

```
FROM node:11.12.0-alpine
RUN apk update && apk upgrade && apk add --no-cache bash git openssh
COPY . .
RUN ["chmod", "+x", "start.sh"]
ENTRYPOINT /start.sh 
```

而`start.sh`文件看起来是这样的(个人资料被替换)

```
#!/bin/sh
git clone --depth 1 -b branch_name https://$TOKEN@github.com/$USER_NAME/$PROJECT.git
cd $PROJECT_NAME
npm install npm run test 
```

你内心的 docker 形象可以是你想要的任何样子。注意它不需要任何特殊的操作系统来运行。

## 将图像放入您的应用程序

所以我们已经制作了测试映像，但是现在我们需要 elixir 应用程序在`docker:dind`系统中访问它。

我们可以使用`docker save`和`docker load`命令来完成这项工作。这些允许我们将 docker 图像保存到一个文件中，然后再加载回来。

如果我们在 test_runner 文件夹中运行`docker save test_runner > test_runner.tar`，我们将得到一个新的 test_runner.tar 文件。

然后在我们的主。/Dockerfile 我们可以添加一行(就在 ENTRYPOINT 之前)
`COPY ./test_runner/test_runner.tar .`
这将文件带入我们的 Elixir 映像。

最后，我们将这一行添加到 main。/start.sh 文件
`docker load < ./test_runner.tar`
这将图像添加到我们的 docker 应用程序，以便可以从 elixir 应用程序启动它。

最终的`./start.sh`文件应该是这样的

```
#!/bin/sh
dockerd-entrypoint.sh & 
sleep 10
docker load < ./test_runner.tar
/opt/app/bin/ci foreground 
```

## 启动容器中的仙丹

既然我们已经为应用程序设置了所有的基础设施，我们就可以在 elixir 应用程序中使用容器了。

在任何需要的地方，你都可以使用
`System.cmd("docker", ["run", "test_runner"])`
来运行 docker 镜像，并以元组的形式返回输出和退出状态。

不要忘记，我们也可以通过传入环境变量来动态定制测试运行程序。

## 结论

谢谢你能走到这一步。没人说 docker 中的 docker 会轻松！

我意识到酏剂部署方法可能会引起争议，尤其是 docker in docker。我欢迎任何关于我们可以在这里使用的替代/更好的系统的评论。

这是我编写的一个构建脚本，在您设置了 elixir 项目、Dockerfiles 和 start.sh 脚本之后，它可以自动完成所有的构建步骤。

最后注意，您必须使用- privileged 标志来使用 docker 中的 docker。

```
#!/bin/sh
cd test_runner
docker build -t test_runner .
docker save test_runner > test_runner.tar
docker image rm test_runner
cd ../
docker build -t elixir_app .
rm test_runner/test_runner.tar
docker run -p $OUTER_PORT:$INNER_PORT --privileged -d elixir_app 
```