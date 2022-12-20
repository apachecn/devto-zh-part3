# 将 Docker 集成到您的个人项目中

> 原文：<https://dev.to/dan_mcm_/integrating-docker-with-your-personal-projects-19cl>

## 为什么？

Docker 自己在他们的[主网站](https://www.docker.com/why-docker)上概述了采用基于容器的基础设施的用例。我可以简单介绍一下我自己的观点，为什么它是一个有用的工具，如何用它来优化你的项目工作流程，以及它如何让你的项目在刚离开大学时脱颖而出。

[![](img/7c6958e5f9c936d953d57ed0e0f5dc78.png)](https://i.giphy.com/media/PA2XJCpgvv1Is/giphy.gif)

## 好处

在这里，我将避免使用船运集装箱的比喻。如果你曾经听说过或遇到过 Docker 或 Kubernetes，这种情况下到处都是这些隐喻。我将尽量使事情简单一些，概述这个工具的一些好处:

*   *依赖验证* : Docker 允许您基于项目的构建创建图像。这意味着您的所有依赖项都是自包含的，允许您在本地将项目构建为 docker 映像，将其作为容器运行，并验证它是否工作以及是否具有所有正确的依赖项。这可以防止您在本地计算机上全局安装的软件包可能出现的奇怪疏忽，这些软件包没有在 projects package.json、sbt 或 dep 文件中定义。如果 Docker 映像在您的 Windows PC 上运行，那么它应该可以在您的 Linux EC2 实例或您计划运行 Docker 映像的其他设备上正常运行。

*   *版本控制*:不要只依赖 GitHub！如上所述，您可以构建 Docker 映像，它本质上是您的项目构建在不同时间点的快照。想象一下这样一个场景，您需要在本地演示您的服务，您从 master 中获取最新版本，构建它，然后它崩溃了...您没有时间回复到旧的提交，构建服务，然后启动它...取而代之的是，你可以提取旧的提交映像并运行它——因为它是完全自包含的，所以构建/运行起来会快得多。这也是一种快速比较旧功能和新功能的便捷方式- >这也可以是一种有趣的方式来查看一个项目在 5 年多前与现在的对比。

*   *CI/CD* :如果您正在使用持续集成/开发解决方案，并且您意外地在管道中部署了一个不完整的构建，那么简单地引用一个早期的 docker 映像，而不是从头开始重新构建项目，会非常节省时间。一种流行的开发方法是将你的项目包装成 docker 镜像，上传到云解决方案(比如公共的 [Docker Hub](https://hub.docker.com/) 或者托管在类似[AWS Elastic Container Registry](https://aws.amazon.com/ecr/)的服务上的更私有的解决方案)，然后在 prod 环境中使用这些镜像。当与 Kubernetes 这样的服务结合使用时，这是一个特别强大的解决方案，这些服务可能跨多个 pods 或 EC2 实例运行服务的副本。使用 docker 映像比三次提取 repo、安装和构建所有依赖项更有效。

*   *在本地模仿产品依赖关系&快速*:在专业环境中工作时，您可能经常会处理前端代码库，需要访问 postgres 数据库来存储一些站点文档，同时还需要访问 Redis 等存储解决方案来管理用户会话存储。由于这些依赖性，设置您的开发环境可能会非常耗时，但是 Docker 有一个有用的工具 [docker-compose](https://docs.docker.com/compose/overview/#features) ，它将允许您使用一个包含不到 10 行代码的 yaml 文件，通过一个命令“docker-compose up -d”来运行 postgres 和 redis 设置。

在这一点上，我将很快承认 Docker 不是最重要的解决方案。Inductor 有一篇有趣的文章[你的系统链接中真的需要 docker 或 kubernetes 吗](https://dev.to/inductor/do-you-really-need-docker-or-kubernetes-in-your-system-11nk)我强烈推荐看看。

作为一名大学毕业生或新的开发人员，如果能够展示出对如何计划使您的项目在生产环境中可伸缩和可部署的兴趣和知识，这将使您在面试中比其他候选人更有优势。在你的个人项目中有一个基本的 Dockerfile 设置会给你另一个独特的谈话点和技巧，供你在面试中参考。

## 将 Docker 与你的初学者项目相结合

我接受了挑战，着手将 Docker 集成到我以前参与的一些项目中，以帮助提供一些关于该工具实际用例的端到端见解，以及添加这一额外功能有多容易。我以前写过关于这些基本项目开发的文章，因此初学者可以通过回顾这些过去的文章，更好地了解一个项目如何随着时间的推移开发新的特性。

你会注意到，所有这三个项目都采用了一种通用模式(这强调了生成这些文件是多么容易)

*   使用基本映像，即 node/openjdk/golang
*   设置维护的
*   设置工作目录并添加相关文件
*   设置任何环境变量，即端口/版本->这样便于以后更新
*   使用“运行”安装依赖项
*   公开您需要的一个或多个端口
*   运行您的主命令，即 npm start

现在让我们看看生成的文件。

### JavaScript:React Portfolio-[代码](https://github.com/daniel40392/react-portfolio) - [文章](https://dev.to/daniel40392/building-a-portfolio-the-painful-way-3im8)

这个项目是从一个简单的创建-反应-应用程序发展而来的。
生成的 Dockerfile 如下所示:

Dockerfile

```
FROM node:8

MAINTAINER Daniel McMahon <daniel40392@gmail.com>

WORKDIR /opt/react-portfolio

ADD . /opt/react-portfolio

ENV PORT 3000

RUN npm install

EXPOSE 3000

CMD npm start 
```

有一个 NPM 软件包可以帮助你在将来自动生成 docker 文件。检查一下 NPM 的 dockerfile-generator 包。

### Scala:灵感 API - [代码](https://github.com/daniel40392/inspiration-api) - [文章](https://dev.to/daniel40392/the-inspiration-api-a-project-built-with-scala--play-framework-195d)

这个项目是从 SBT 构建的一个基本的 Scala/Play 应用程序发展而来的。该服务依赖于 postgres 数据库，可以使用 docker-compose 在本地设置该数据库。

Dockerfile

```
FROM openjdk:8

MAINTAINER Daniel McMahon <daniel40392@gmail.com>

WORKDIR /opt/inspiration-api

ADD . /opt/inspiration-api

ENV SBT_VERSION 0.13.15

# Install sbt
RUN \
  curl -L -o sbt-$SBT_VERSION.deb http://dl.bintray.com/sbt/debian/sbt-$SBT_VERSION.deb && \
  dpkg -i sbt-$SBT_VERSION.deb && \
  rm sbt-$SBT_VERSION.deb && \
  apt-get update && \
  apt-get install sbt && \
  sbt sbtVersion

EXPOSE 9000

CMD sbt run 
```

坞站-复合。yaml

```
inspiration:
  container_name: inspiration
  image: postgres:9.6-alpine
  ports:
    - '5432:5432'
  environment:
    POSTGRES_DB: 'inspiration_db'
    POSTGRES_USER: 'user' 
```

为了让运行服务的 Docker 容器正确地与本地数据库进行通信，您需要使用下面的端口设置来运行它:

```
# setup db dependencies
docker-compose up -d
psql -h localhost -U user inspiration_db -f dbsetup.sql

# build and tag image locally
docker build -t inspiration_api:v1 .

# port forwarding Docker to localhost:9000
# note: this seems to be broken atm - I'll update when fixed!
docker run -ti -p 9000:9000 -p 5432:5432 <docker-image-id>

# publish docker image to docker hub
docker push <docker-repo> 
```

对自动化这一过程感兴趣吗？有几个 Scala 库可以自动为你生成和推送 docker 图片。检查 marcuslonnberg 的 [sbt-docker](https://github.com/marcuslonnberg/sbt-docker) 插件。

### Golang: Lucas - [代号](https://github.com/daniel40392/lucas) -【文章来袭...]

这个项目是一个使用 Go 和 Colly 库构建的基本网络爬虫。该服务依赖于 postgres 数据库，可以使用 docker-compose 在本地设置该数据库。

Dockerfile

```
FROM golang:1.11

MAINTAINER Daniel McMahon <daniel40392@gmail.com>

WORKDIR /opt/lucas

ADD . /opt/lucas

ENV PORT blergh

# installing our golang dependencies
RUN go get -u github.com/gocolly/colly && \
  go get -u github.com/fatih/color && \
  go get -u github.com/lib/pq

EXPOSE 8000

CMD go run lucas.go 
```

坞站-复合。yaml

```
lucas:
  container_name: lucas
  image: postgres:9.6-alpine
  ports:
    - '5432:5432'
  environment:
    POSTGRES_DB: 'lucas_db'
    POSTGRES_USER: 'user' 
```

为了让运行服务的 Docker 容器正确地与本地数据库进行通信，您需要在主机模式下使用网络标志来运行它:

```
# setup db dependencies
docker-compose up -d
psql -h localhost -U user lucas_db -f dbsetup.sql

# build docker image
docker build .

# run docker container and portforward port 3000
# the host network flag will allow your container to speak to your local DB
docker run -ti -p 8000:8000 --network="host" <docker-image-id>

# publish docker image to docker hub
docker push <docker-repo> 
```

## 关闭思绪

正如你从上面看到的，让你的应用程序在 Docker 上运行是相对简单的。当你第一次习惯 Docker 背后的概念时，这可能会有点棘手，但是一旦你掌握了它，它提供的优势是非常棒的。

这个星期我偶然发现了一个超级酷的工具，它可以让你分析 docker 图片的质量，并给你一个优化程度的%评级，也就是说，如果你已经安装了太多的软件包等等。去看看吧——名为' [dive](https://github.com/wagoodman/dive) 的库。

“整理”应用程序的下一步自然是通过某种 CI/CD 解决方案进行部署。我希望尝试使用名为 [kops](https://github.com/kubernetes/kops) 的库建立自己的 Kubernetes 集群，并通过它部署上述服务。

任何反馈/想法/建议都可以在下面随意评论。

下次见！