# 如何在 Docker 容器中调试 Node.js

> 原文：<https://dev.to/alex_barashkov/how-to-debug-nodejs-in-a-docker-container-bhi>

越来越多的团队将他们的开发环境转移到 Docker 容器。它带来了很多优势，比如所有开发人员共享的统一环境、新成员更快的入职流程以及可预测的部署。例如，在我之前的文章[“微服务与整体架构”](https://dev.to/alex_barashkov/microservices-vs-monolith-architecture-4l1m)中，我指出，对于微服务，你必须使用 Docker，因为否则你将在本地机器上启动多个微服务，开发将成为巨大的痛苦。当你有甚至 5-10 个微服务时，你通过你的终端一个接一个地运行它们，并且必须确保你有所有的依赖项、数据库、弹性搜索等等。，已安装。或者，您可以使用 docker-compose 命令让它运行，这是一种更好的方法。

但是这种方法要求您理解 Docker，并且不要错过没有它时的功能或体验。需要理解的一件事是如何在 Docker 容器中调试。在本文中，我们将浏览一些与在 docker 容器中调试
Node.js 应用程序相关的用例。

### 先决条件

*   [VS 代码](https://code.visualstudio.com/)
*   [码头工人](https://www.docker.com/products/docker-desktop)
*   [VS 代码的 Docker 扩展](https://code.visualstudio.com/docs/azure/docker)

### 例

*   Node.js，Docker，不带 Nodemon
*   Node.js、docker、Nodemon
*   Node.js，Docker 与 docker-compose

### Node.js，Docker，不带 Nodemon

如果您的 docker 文件中已经有 Node.js 应用程序，它可能是这样的:

```
FROM node:10-alpine

WORKDIR /usr/src/app

COPY package*.json ./
RUN npm install

COPY . .

CMD [ "npm", "start" ] 
```

Enter fullscreen mode Exit fullscreen mode

为了继续，我们需要建立我们的 order 文件。我建议使用 VS Code Docker 扩展，并开始构建，如下所示:

[![](img/81d2a79d44a21bc4bacaf44a107ca433.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nvxEM8Pf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ntsgl1prone9gtimgg63.png)

为了在 Node.js 中启用调试器，我们需要使用 **- inspect** 或者 [- inspect-brk](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_attaching-to-nodejs) ，但是因为我们的 app 将在 Docker 内部启动，我们还需要通过传递 **0.0.0.0** 来允许外部网络访问我们的调试器。

```
 "scripts": {
    "start": "node --inspect=0.0.0.0 index.js"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您执行`npm start`时，它将在一个单独的端口(默认为 9229)上运行节点调试器，然后您可以将调试器工具连接到该端口。要访问调试器，您还必须向您的主机公开 9229 端口。您可以使用下面的命令来完成这个任务:

```
docker run --rm -d -p 3000:3000 -p 9229:9229 -v ${PWD}:/usr/src/app -v /usr/src/app/node_modules example:latest 
```

Enter fullscreen mode Exit fullscreen mode

使用该命令，我们将 Docker 化应用程序的 3000 和 9229 端口暴露给 localhost，然后将应用程序的当前文件夹挂载到/usr/src/app，并使用 hack 来防止通过 Docker 从本地机器覆盖节点模块。

现在，我们可以使用 VS 代码向导调试启动任务进行配置。按 CMD(Ctrl)+Shift+P(命令调板)，找到“调试:打开 launch.json”:

[![](img/040c00cfe79d6f8b6c07ad7b1145646b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l73JohJ4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ol6r4zt6271nbi9wn48r.png) 
然后选择 Docker: Node.js:

[![](img/763612dae07e002d57c5fefa18e3f308.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JhlyFFha--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rrq40l0dpp8oydyknr1a.png) 
这会生成一个 launch.json 文件，内容如下:

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Docker: Attach to Node",
            "type": "node",
            "request": "attach",
            "port": 9229,
            "address": "localhost",
            "localRoot": "${workspaceFolder}",
            "remoteRoot": "/usr/src/app",
            "protocol": "inspector"
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

Docker 的配置是手动连接到调试器端口，并将本地根文件夹映射到远程，以便保持断点定义工作。

转到 VS code 的调试页面，按下“Play”按钮，享受 Docker 中的调试。

### Node.js，Docker，带 Nodemon

当我们想将调试器与 nodemon 一起使用时，就出现了微小的差别。首先，package.json 中的脚本应该是这样的:

```
"start": "nodemon --inspect=0.0.0.0 src/index.js", 
```

Enter fullscreen mode Exit fullscreen mode

然后，因为 nodemon 将在每次更改时重启您的应用程序，所以您的调试器将丢失一个连接。当这种情况发生时，有一个选项“restart: true”，这将使您在每次重新启动后尝试重新连接到调试器。

所以你的 launch.json 应该是这样的:

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Docker: Attach to Node",
            "type": "node",
            "request": "attach",
            "port": 9229,
            "address": "localhost",
            "localRoot": "${workspaceFolder}",
            "remoteRoot": "/usr/src/app",
            "protocol": "inspector",
            "restart": true
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

转到 VS code 的调试页面，按下“Play”按钮，像以前一样，享受在 Docker 中调试的乐趣。

[![](img/62ae5a6ca0ade82afac15d44256cc9a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xmMXBzRq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a2j51zn3r2svz6c0e29p.png)

### Node.js，Docker，带 docker-compose

第三个选择是用 docker-compose 运行 docker 映像，如果您的服务还需要一个数据库或其他可以用 docker 运行的依赖项，这是一个好方法。

在你的 app 文件夹中创建一个 docker-compose.yaml，内容如下:

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

我们使用了与非 docker-compose 解决方案基本相同的指令，只是将它们转换为 yaml 格式。现在，您可以使用 nodemon 或 node.js 运行选项继续处理 launch.json 文件，并使用前面几节中描述的调试器。