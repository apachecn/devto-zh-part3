# 在开发和生产中使用 Docker for Node.js

> 原文：<https://dev.to/alex_barashkov/using-docker-for-nodejs-in-development-and-production-3cgp>

我目前的主要技术栈是 Node.js/Javascript，像许多团队一样，我将我们的开发和生产环境转移到 Docker 容器中。然而，当我开始学习 Docker 时，我意识到大多数文章关注的是开发或生产环境，而没有发现关于如何组织 Docker 配置以适应这两种情况的文章。

在本文中，我展示了 Node.js Dockerfiles 文件的不同用例及示例，解释了决策过程，并帮助设想了您的流程应该如何使用 Docker。从一个简单的例子开始，我们然后回顾更复杂的场景和解决方法，以保持您的开发体验与 Docker 一致或不一致。

免责声明:本指南篇幅很大，主要面向码头工人技能水平各异的不同受众；在某些时候，说明对你来说是显而易见的，但是我会试着在它们旁边做一些相关的说明，以提供最终设置的完整视图。

### 先决条件

*   [VS 代码](https://code.visualstudio.com/)
*   [码头工人](https://www.docker.com/products/docker-desktop)
*   [VS 代码的 Docker 扩展](https://code.visualstudio.com/docs/azure/docker)

### 描述案例

*   基本 Node.js Dockerfile 和 docker-compose
*   开发中的节点，生产中的节点
*   让生产 Docker 映像远离依赖设备
*   对映像使用多阶段构建需要 node-gyp 支持

### Add。停靠档案

在我们开始配置 Dockerfile 之前，让我们将一个. dockerignore 文件添加到您的应用程序文件夹中。的。dockerignore 文件不包括在复制/添加命令文件期间描述的文件。[此处阅读更多](https://docs.docker.com/engine/reference/builder/#dockerignore-file)

```
node_modules
npm-debug.log
Dockerfile*
docker-compose*
.dockerignore
.git
.gitignore
README.md
LICENSE
.vscode 
```

Enter fullscreen mode Exit fullscreen mode

### 基本节点. js Dockerfile

为了确保理解清楚，我们将从您可以用于简单 Node.js 项目的基本 Dockerfile 开始。简单，我的意思是你的代码没有任何额外的本地依赖或构建逻辑。

```
FROM node:10-alpine

WORKDIR /usr/src/app

COPY package*.json ./
RUN npm install

COPY . .

CMD [ "npm", "start" ] 
```

Enter fullscreen mode Exit fullscreen mode

你会在每一篇 Node.js Docker 文章中发现类似这样的东西。我们简单过一遍。

```
WORKDIR /usr/src/app 
```

Enter fullscreen mode Exit fullscreen mode

workdir 是一种默认目录，用于任何 RUN、CMD、ENTRYPOINT、COPY 和 ADD 指令。在一些文章中，你会看到人们使用 mkdir /app，然后将其设置为 workdir，但这不是最佳实践。使用预先存在的文件夹/usr/src/app 更适合此目的。

```
COPY package*.json ./
RUN npm install 
```

Enter fullscreen mode Exit fullscreen mode

这是另一个最佳实践调整:在将代码复制到容器中之前，复制 package.json 和 package-lock.json。Docker 会将已安装的 node_modules 缓存为一个单独的层，那么，如果你更改了你的 app 代码，执行了 build 命令，如果你没有更改 package.json，node_modules 就不会被再次安装，一般来说，即使你忘记添加那些行，也不会遇到很多问题。通常，只有当 package.json 发生更改时，您才需要运行 docker 构建，这导致您无论如何都要从头开始安装。在其他情况下，在开发环境中的初始构建之后，您不会过于频繁地运行 docker build。

### docker-compose 进来的那一刻

在我们开始在生产中运行我们的应用程序之前，我们必须开发它。编排和运行 docker 环境的最佳方式是使用 [docker-compose](https://docs.docker.com/compose/) 。以易于使用的语法定义要运行的容器/服务列表以及它们的说明，以便在 YAML 文件中进一步运行。

```
version: '3'

services:
  example-service:
    build: .
    volumes:
      - .:/usr/src/app
      - /usr/src/app/node_modules
    ports:
      - 3000:3000
      - 9229:9229
    command: npm start 
```

Enter fullscreen mode Exit fullscreen mode

在上面的基本 docker-compose.yaml 配置示例中，通过使用应用程序文件夹内的 Dockerfile 完成构建，然后将应用程序文件夹挂载到容器，在构建期间安装在容器内的 node_modules 将不会被当前文件夹覆盖。假设您有一个正在运行的 web 服务器，3000 端口对您的本地主机是公开的。9229 用于暴露调试端口。[此处阅读更多内容](https://dev.to/alex_barashkov/how-to-debug-nodejs-in-a-docker-container-bhi)。

现在使用
运行您的应用程序

```
docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

或者使用 VS 代码扩展来达到同样的目的。

[![](img/6fcaef61d7739c40f8db0a14ef35d98c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SFxy_jCV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kolv6ogxzlp7xy1m5cba.png)

使用这个命令，我们将 Docker 化的应用程序的 3000 和 9229 端口暴露给 localhost，然后我们将带有应用程序的当前文件夹挂载到/usr/src/app，并使用 hack 来防止通过 Docker 从本地机器覆盖节点模块。

那么你能在开发和生产中使用 Dockerfile 文件吗？
是也不是

**CMD 的差异**
首先，通常你希望你的开发环境 app 在一个文件改变时重新加载。为此，您可以使用 [nodemon](https://github.com/remy/nodemon) 。但是在生产中，您希望没有它也能运行。这意味着开发和生产环境的 CMD(命令)必须不同。

对此有几种不同的选择:

**1。**用不带 nodemon 运行你的 app 的命令替换 CMD，可以是你的 package.json 文件中单独定义的命令，比如:

```
 "scripts": {
   "start": "nodemon --inspect=0.0.0.0 src/index.js",
   "start:prod": "node src/index.js"
 } 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，您的 docker 文件可能是这样的:

```
FROM node:10-alpine

WORKDIR /usr/src/app

COPY package*.json ./
RUN npm install

COPY . .

CMD [ "npm", “run”, "start:prod" ] 
```

Enter fullscreen mode Exit fullscreen mode

但是，因为您在开发环境中使用 docker-compose 文件，所以我们可以在其中使用不同的命令，就像前面的例子一样:

```
version: '3'

services:
   ### ... previous instructions
    command: npm start 
```

Enter fullscreen mode Exit fullscreen mode

**2。**如果差异较大或者使用 docker-compose 进行开发和生产，可以根据您的差异创建多个 docker-compose 文件或 Dockerfile。如 docker-compose.dev.yml 或 Dockerfile.dev。

**管理软件包安装**
通常最好将生产映像的大小保持得尽可能小，并且不要安装生产中不必要的节点模块依赖项。通过保持一个统一的 docker 文件，仍然有可能解决这个问题。

重新访问 package.json 文件，将 devDependencies 从依赖项中分离出来。[此处阅读更多内容](https://stackoverflow.com/questions/18875674/whats-the-difference-between-dependencies-devdependencies-and-peerdependencies)。简而言之，如果使用- production 标志运行 npm install 或将 NODE_ENV 设置为 production，将不会安装所有的 devDependencies。我们将在 docker 文件中添加额外的行来处理这个问题:

```
FROM node:10-alpine

ARG NODE_ENV=development
ENV NODE_ENV=${NODE_ENV}

WORKDIR /usr/src/app

COPY package*.json ./
RUN npm install

COPY . .

CMD [ "npm", “run”, "start:prod" ] 
```

Enter fullscreen mode Exit fullscreen mode

为了定制行为，我们使用

```
ARG NODE_ENV=development
ENV NODE_ENV=${NODE_ENV} 
```

Enter fullscreen mode Exit fullscreen mode

Docker 支持通过 docker 命令或 docker-compose 传递构建参数。默认情况下将使用 NODE_ENV=development，直到我们用不同的值覆盖它。你可以在这里找到很好的解释。

现在，当您使用 docker-compose 文件构建容器时，所有依赖项都将被安装，并且当您为生产构建容器时，您可以传递 build 参数，因为生产和 dev 依赖项将被忽略。因为我使用 CI 服务来构建容器，所以我只是为它们的配置添加了那个选项。[在此阅读更多内容](https://docs.docker.com/engine/reference/commandline/build/)

**对需要 node-gyp 支持的映像使用多阶段构建**
不是每个你将尝试在 Docker 中运行的应用都会专门使用 JS 依赖项，其中一些需要 node-gyp 和额外的本地安装操作系统库才能使用。

为了帮助解决这个问题，我们可以使用[多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)，它帮助我们在一个单独的容器中安装和构建所有的依赖项，并只将安装的结果移动到最终的容器中，而没有任何垃圾。docker 文件可能是这样的:

```
# The instructions for the first stage
FROM node:10-alpine as builder

ARG NODE_ENV=development
ENV NODE_ENV=${NODE_ENV}

RUN apk --no-cache add python make g++

COPY package*.json ./
RUN npm install

# The instructions for second stage
FROM node:10-alpine

WORKDIR /usr/src/app
COPY --from=builder node_modules node_modules

COPY . .

CMD [ "npm", “run”, "start:prod" ] 
```

Enter fullscreen mode Exit fullscreen mode

在该示例中，我们在第一阶段安装并编译了所有基于环境的依赖项，然后在第二阶段复制了 node_modules，我们将在开发和生产环境中使用它。

行`RUN apk --no-cache add python make g++`可能会因项目而异，可能是因为您需要额外的依赖项。

```
COPY --from=builder node_modules node_modules 
```

Enter fullscreen mode Exit fullscreen mode

在这一行中，我们将第一阶段的 node_modules 文件夹复制到第二阶段的 node_modules 文件夹。因此，在第二阶段，我们将 WORKDIR 设置为/usr/src/app。node _ modules 将被复制到该文件夹。

### 总结

我希望本指南能帮助您理解如何组织 docker 文件，并让它满足您在开发和生产环境中的需求。我们可以把我们的建议总结如下:

*   尝试为开发和生产环境统一 docker 文件；如果不行，就把他们分开。
*   不要为生产版本安装 dev node_modules。
*   不要在最终映像中留下 node-gyp 和节点模块安装所需的本机扩展依赖关系。
*   使用 docker-compose 来编排您的开发设置。
*   这取决于你在生产中选择什么来编排你的 Docker 容器，可以是 docker-compose，Docker Swarm 或者 Kubernetes。