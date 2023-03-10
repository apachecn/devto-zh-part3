# 如何对 ExpressJS 应用程序进行 Dockerize

> 原文：<https://dev.to/bbenefield89/how-to-dockerize-an-expressjs-application-4k8c>

## 这是什么

*   这是一个关于如何对 ExpressJS 应用程序进行 Dockerize 的简短示例。
*   这篇文章还假设你使用的是基于*NIX 的操作系统，比如 Ubuntu 18.10

## 这不是什么

*   这并不是关于 Docker、NodeJS、ExpressJS 或任何其他可能使用的技术的完整指南。
*   这并不解释或引导您完成 Docker 的安装过程或所使用的任何技术。

* * *

## 创建目录

我们需要创建一个目录。这是我们将要放置所有 Docker 图像文件以及 ExpressJS 文件的地方。

```
foo@foo:~/path/to/current/directory$ mkdir node_docker_project 
```

## 创建 Dockerfile

`CD`放入新目录，创建一个新文件，命名为`Dockerfile`，不带任何扩展名。

```
foo@foo:~/path/to/current/directory$ cd node_docker_project
foo@foo:~/node_docker_project$ touch Dockerfile 
```

将下一段代码放在`Dockerfile`中。

```
# Tell Docker to use the "node" Docker Image at version "10.15.3"
FROM node:10.15.3
# Create our containers WORKDIR and "node_modules" directory.
# Give the user:group "node" ownership of all files/directories in our containers WORKDIR
RUN mkdir -p /home/node/app/node_modules && chown -R node:node /home/node/app
# Tell our container which directory to use as the WORKDIR
WORKDIR /home/node/app
# Copy over our local version of "package.json" and "package-lock.json" into our container
COPY package*.json ./
# Creates a user for our container
USER node
# Installs our NPM packages from the "package.json" file we moved from local in to our container
RUN npm install
# Tells our container who owns the copied content
COPY --chown=node:node . .
# Exposes the port "3000" from our container
# This is also how we can connect to our container from our host machine (the one you're reading this from now)
EXPOSE 3000
# An array of commands our container needs to run when we start it
CMD ["npm", "run", "start"] 
```

## 创建 app.js

```
const express = require('express')
const app = express()
app.get('/', (req, res) => {
  res.send('Hello, world!')
})
app.listen(3000, () => console.log('Server listening @ 3000')) 
```

## 创建 package.json

让我们创建 package.json 文件来保存我们的依赖项。

```
foo@foo:~/node_docker_project$ npm init -y 
```

然后我们需要安装这些依赖项。

```
foo@foo:~/node_docker_project$ npm i express
foo@foo:~/node_docker_project$ npm i --save-dev nodemon 
```

我们需要 Express，因为这将是我们用于服务器的框架，我们需要 Nodemon，这样我们就可以默认自动重启服务器。当您将文件从本地更改到容器时，这将很方便。如果没有 Nodemon，每次进行更改时都需要重新启动容器才能看到更改。

## 创建我们的码头工人形象

把 Docker 图像想象成菜谱，把 Docker 容器想象成饭菜。为了吃这顿饭，我们需要按照食谱来烹饪。

```
foo@foo:~/node_docker_project$ docker build -t node_project
foo@foo:~/node_docker_project$ docker images
REPOSITORY     TAG      IMAGE ID     CREATED          SIZE
node_project   latest   3esadssa     10 Seconds Ago   908MB

foo@foo:~/node_docker_project$ docker run --name myNodeProject -v $(pwd):/home/node/app -d -p 3000:3000 node_project
foo@foo:~/node_docker_project$ docker ps
CONTAINER ID  IMAGE         COMMAND          CREATED
123klasdkj    node_project  "npm run start"  6 seconds ago

STATUS        PORTS                   NAMES
Up 4 seconds  0.0.0.0:3000->3000/tcp  myNodeProject 
```

## 检查你的工作

现在我们已经创建了我们的容器，它已经启动并运行，我们能够通过打开的端口连接到我们的容器。在我们的例子中，我们在 3000 打开了一个端口。

打开您的浏览器并导航到`http://localhost:3000`，您应该会看到我们的`Hello, World!`消息，该消息放在我们之前创建的`app.js`文件中的`app.get('/' ...)`请求中。

## 检查我们是否可以从我们的主机上更改容器文件

继续将我们的`app.js`文件中的响应文本从`Hello, World!`更改为`Docker is amazing!`。

```
const express = require('express')
const app = express()
app.get('/', (req, res) => {
  res.send('Docker is amazing!')  // change this line
})
app.listen(3000, () => console.log('Server listening @ 3000')) 
```

回到您的浏览器，或者用`f5`刷新，或者导航回`http://localhost:3000`，您应该会看到新的响应。

* * *

## 总结

Docker 的基础其实很简单。你需要做的就是:

*   获取 Docker 图像
*   创建 Docker 容器
*   通过打开的端口连接到您的容器

* * *

## 资源

*   [从 GitHub 克隆这个 Docker 镜像](https://github.com/bbenefield89/DockerizedExpress)

*   [从 Travery Media 了解 Docker 的基础知识和更多信息](https://www.youtube.com/watch?v=Kyx2PsuwomE)

*   [坞站中心(坞站图像的 github)](https://hub.docker.com/)