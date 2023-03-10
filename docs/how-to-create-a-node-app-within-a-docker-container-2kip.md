# 如何在 Docker 容器中创建节点应用程序

> 原文：<https://dev.to/wchr/how-to-create-a-node-app-within-a-docker-container-2kip>

# 设置

## 要求

在您阅读这个有趣的教程之前，请确保:

*   你至少了解了 *javascript* 和*终端*命令。
*   你应该知道什么是`Docker`以及理论上它是如何工作的。
*   你的电脑上已经安装了 should `node`和`npm`。您可以通过在终端中键入以下内容来完成此操作。

```
$ node --version && node --version 
```

Enter fullscreen mode Exit fullscreen mode

*   此外，既然我们谈论容器，你需要安装`docker`。

```
$ docker --version 
```

Enter fullscreen mode Exit fullscreen mode

## 创建新项目

创建我们的项目文件夹，我们的代码库将存放在这里

```
$ mkdir docker_nodejs_app 
```

Enter fullscreen mode Exit fullscreen mode

让我们将目录更改为我们的应用程序文件夹。

```
$ cd docker_nodejs_app 
```

Enter fullscreen mode Exit fullscreen mode

由于这是一个`node`项目，我们需要一个`package.json`文件来跟踪我们的项目依赖关系。

要快速创建一个，在你的终端输入这个。

```
$ npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用`express`作为我们默认的`node` web 框架。

```
$ npm install express --save # Introduce the save flag to track it in the package.json file 
```

Enter fullscreen mode Exit fullscreen mode

```
{  "name":  "docker_node_app",  "version":  "1.0.0",  "description":  "nodejs image demo",  "author":  "your name",  "license":  "MIT",  "main":  "app.js",  "keywords":  [],  "scripts":  {  "start":"node app.js"  },  "dependencies":  {  "express":  "^4.16.4"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 创建并运行我们的服务器

我们将创建一个简单的`express`服务器。让我们创建一个保存服务器代码的文件。

您可以使用*终端*创建文件

```
$ touch app.js # Creates the file from the terminal 
```

Enter fullscreen mode Exit fullscreen mode

或者本地安装的代码编辑器。

让我们编写我们的*服务器代码*。

```
"use strict"; // Ensures our code is compiled in strict mode

// Lets import our web framework
var express = require("express");

// Initialise our app
const app = express();

// Lets set our port
/**
 * The default port number is `3000`
 * Take note on that as we will come to that.
 */
app.set("port", 3000);

/**
 * To ensure works as it should we will create a
 * simple endpoint to return a json response
 */

// Define our json response
const data = {
  blog_name: "docker_nodejs_app",
  blog_author: "wachira (tesh254)",
  blog_author_twitter: "@wachira_dev"
};

// Define out GET request endpoint
app.get("/", (req, res) => {
  res.status(200).json(data);
});

// Initialize our server
app.listen(app.get("port"), () => {
  console.log(`Server listening on port ${app.get("port")}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们*运行* it，这是一个简单的服务器，意思是它的`bug-free`。

```
$ node app.js 
```

Enter fullscreen mode Exit fullscreen mode

您应该在终端上看到相同的文本。

[![Screenshot 2019-05-22 at 4.07.38 PM.png](img/f968f8ea2e4cb6cc1ea68dda78b0e487.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6_FBCSye--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1558530469391/FeZtCTI77.png)

让我们在浏览器上测试我们的端点。

[![Screenshot 2019-05-22 at 5.27.21 PM.png](img/49ad4223a91d353ae7455b82cd1bafda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xhhrVUnp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1558535254616/q95_Lw9Mv.png)

## 终于知道博客是关于什么的了.....码头工人

要在容器中运行服务器，您需要做几件事情:

*   Dockerfile: *定义了容器内部环境中发生的事情。*
*   `docker-compose.yml` : *不是必须的，但如果你打算增加像`database`* 这样的服务，这是很方便的
*   依赖文件:*包含成功运行应用程序所需的包，例如节点的`package.json`文件或 python 的`requirements.txt`。*
*   `.dockerignore` : *这不是必须的，但是它允许您从上下文中排除文件，就像`.gitignore`文件允许您从 git 存储库中排除文件一样。*

让我们创建并编写我们的`Dockerfile`

```
$ touch Dockerfile 
```

Enter fullscreen mode Exit fullscreen mode

您可以将配置复制并粘贴到 docker 文件中。

```
# Define the image we will use and version
# latest just means we need the latest nodejs image available
FROM node:8

# Create an app directory to hold the application code
WORKDIR /usr/docker_nodejs_app/src/app

# Duplicate the dependency file to the container's project root directory.
COPY package*.json ./

# Install app dependencies
RUN npm install

# Bundle app source inside the docker image
COPY . .

# Expose our app port inside the app and 
EXPOSE 3000:3000

# Define commands that will run the app
CMD ["npm", "start"] 
```

Enter fullscreen mode Exit fullscreen mode

转到您的终端，并建立您的容器。

```
$ docker build -t docker_nodejs_app . 
```

Enter fullscreen mode Exit fullscreen mode

构建完成后，您应该会在终端上看到类似这样的内容。

[![Screenshot 2019-05-22 at 5.30.55 PM.png](img/9e2a2f3d90e4b2a4133b5f4776c012e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GZ1PpyOm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1558535472185/Zrw0EPn7g.png)

让我们从 docker
运行我们的应用程序

```
$ docker run -it docker_nodejs_app 
```

Enter fullscreen mode Exit fullscreen mode

如果你做了本教程中的所有事情，那么你应该会看到类似下面的截图。

[![Screenshot 2019-05-22 at 5.42.09 PM.png](img/2c69c581d9cd02d2fac1998f351dd6e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MWDUPaek--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1558536144722/vat0_Qvcj.png)

在浏览器上测试一下，结果和预期的一样。

本教程将是一个系列，这是第一部分。这些部分如下:

*   向我们的应用程序介绍服务，*剧透警报*，`MongoDB`。
*   在`Heroku`托管我们的 docker 容器。
*   把我们的回购推送到码头仓库。
*   Docker 的其他命令让您的体验物有所值。

## 加贺

*   指向 repo 的链接[https://github . com/werickblog/docker _ nodejs _ app](https://github.com/werickblog/docker_nodejs_app)

*   链接到 https://nodejs.org/en/download/的下载节点

*   链接下载 Docker[https://www.docker.com/get-started](https://www.docker.com/get-started)

*   了解什么是码头工人[https://docs.docker.com/engine/docker-overview/](https://docs.docker.com/engine/docker-overview/)