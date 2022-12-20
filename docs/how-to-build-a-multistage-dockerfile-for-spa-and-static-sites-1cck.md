# 如何为 SPA 和静态站点构建多级 docker 文件

> 原文：<https://dev.to/maximrouiller/how-to-build-a-multistage-dockerfile-for-spa-and-static-sites-1cck>

当你是一名顾问时，你的目标是考虑如何为你的客户省钱。他们不会付钱给我们，因为我们会编码。他们付钱是因为我们可以从他们的账单中扣除几美元(或几百美元)。

我们经常遇到的一种情况是构建单页面应用程序(SPA)。客户希望动态驱动的应用程序不会刷新整个页面，SPA 通常是他们的最佳选择。在用于构建 SPA 的众多工具中，我们发现 Angular、Vue 和 React。

我发现用容器交付网站是确保跨环境兼容性的通用方法，不管是不是云环境。它还避免了开发人员的环境必须安装 25 种不同的工具/语言/SDK。

它使事情简洁高效。

如果你想[了解更多关于 Docker 容器](https://docs.microsoft.com/dotnet/standard/containerized-lifecycle-architecture/what-is-docker?WT.mc_id=maximerouiller-blog-marouill)的信息，特别是花几分钟，阅读一下关于[的术语](https://docs.microsoft.com/dotnet/standard/containerized-lifecycle-architecture/docker-terminology?WT.mc_id=maximerouiller-blog-marouill)。

问题是我们只需要 Node.js 来构建应用程序，而不是运行它。那么，容器将如何解决我们的问题呢？Docker 中有一个概念叫做[多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)，在这里你可以将构建过程与执行过程分开。

这里有一个模板，可以用来用 Node.js 构建 SPA。

## node . js 的 Dockerfile 模板

> 注意:这是取自 VueJS 文档的一个模板。

```
#build stage for a Node.js application
FROM node:lts-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

#production stage
FROM nginx:stable-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"] 
```

这里有很多东西要打开。我们分开来看两个阶段。

## 构建阶段(Node.js)

多级 docker 构建允许我们以两种方式拆分容器。让我们看看构建阶段。

第一句台词很经典。我们从一个预先安装了 Node.js 的 Alpine 映像开始。

> 注意:Alpine Linux 是基于 musl libc 和 busybox 的面向安全的轻量级 Linux 发行版。它的主要特点是它可以在任何地方运行，而且非常小，大约 5MB。

我们正在将`/app`配置为工作目录。然后，我们做一些不寻常的事情。我们先复制我们的`package*.json`文件，然后再复制其他文件。

为什么？docker 文件中的每一行代表一个层。构建图层时，如果图层已经在本地存在，则从缓存中检索而不是重新构建。通过在一个单独的步骤中复制和安装我们的包，我们避免了在最初没有改变的依赖项上运行`npm install`。由于`npm install`可能需要一段时间来安装，我们节省了一些时间。

最后，我们复制应用程序的其余部分，并运行 npm `build`任务。如果你的应用程序没有一个`build`任务，把名字改成任何任务生成一个输出文件夹，像`dist`。

结果呢？我们在`/app/dist`中有一个正确构建的 Node.js 应用程序。

## 生产阶段

我们已经用 Node.js 生成了我们的 SPA 或静态站点，但是...我们的应用程序没有使用 Node.js，而是使用 HTML/CSS/JS。我们不需要 Node.js 映像来将我们的应用程序投入生产。相反，我们只需要一个 HTTP 服务器。让我们用 [NGINX Docker 镜像](https://hub.docker.com/_/nginx)作为主机。

我们将输出从我们之前定义的`build-stage` `/app/dist`文件夹复制到 NGINX 定义的文件夹`/usr/share/nginx/html`中，正如他们的文档中提到的[。](https://github.com/docker-library/docs/tree/master/nginx#how-to-use-this-image)

在公开端口 80 之后，我们需要使用`daemon off;`选项运行 NGINX，让它在前台运行并防止容器关闭。

## 构建 Dockerfile

这一步很容易。在包含`Dockerfile`的文件夹中运行以下命令。

```
docker build -t mydockerapp:latest . 
```

## 本地运行 Docker 容器

在您的机器上运行应用程序当然只是一个简单的命令。

```
docker run -it -p 8080:80 mydockerapp:latest 
```

这个命令做两件事。首先，它使用`-i`标志在交互模式下运行容器。这个标志将允许我们看到 NGINX 运行时的输出。其次，它将本地机器的端口 8080 映射到容器的端口 80。

打开浏览器进入`http://localhost:8080`将显示您的网站。

## 结论

我越来越多地使用 Docker 做任何事情。我正在用当前的技术构建单一用途的应用程序。Docker 使我能够用旧版本的框架、运行时、语言运行应用程序，而不会给我的机器带来工具版本问题。

虽然技术可能会继续发展，但我从不担心我的 Docker 容器不再工作。事物在时间中停滞不前，哪怕只是一瞬间。

这意味着我不必升级 AngularJS 1。X app 保持酷炫。如果成功了，就成功了。

你正在以不寻常的方式使用 Docker 吗？在 Twitter 上与我分享它们[！](https://twitter.com/MaximRouiller)