# 面向前端开发人员的 Nginx 超越基础

> 原文：<https://dev.to/ganeshmani/nginx-for-front-end-developers-beyond-the-basics-2ief>

这篇文章是针对前端开发人员的 Nginx 的后续文章。现在，我们将看到面向前端开发人员的 Nginx，它超越了基础

在[之前的文章](https://cloudnweb.dev/2019/05/nginx-for-front-end-developers/)中，我们为一个 web 应用程序设置了一个基本的 nginx 配置。在本文中，我们将看到如何设置 nginx - **代理通道**，即**反向代理**的一些重要特性。

### 什么是代理？

在进入反向代理这个话题之前，我们先来看看代理是什么意思。代理只不过是处理请求并将其来回转发到适当的源/目的地

代理服务器有两种方式，**正向代理**和**反向代理**

### 转发代理服务器

*   ![](img/21a51c8badc801e04556c03f50e24d94.png)

转发代理向客户端或客户端组提供代理服务。当客户端请求服务器时。转发代理服务器接收请求并将其发送到应用服务器。

从应用服务器的角度来看，请求文件的是代理服务器。它不知道哪个客户端请求文件。这就是前向代理服务器的优势。

如您所见，代理可以作为单点访问和控制，使您更容易实施安全策略

### 反向代理服务器

*   ![](img/2fb12013aab319817562c79bebebf631.png)

反向代理与正向代理正好相反。而一个正向代理代表客户端(或请求主机)代理，一个反向代理代表服务器代理。反向代理代表位于其后的服务器接受来自外部客户端的请求

从客户端来看，服务于文件的是反向代理。它不知道哪个服务器提供该文件。

### Nginx 中的反向代理设置

我们将看到如何在 nginx 中设置一个反向代理的代理通道。

最重要的是，确保你的机器上安装了 nginx。要了解如何安装 nginx， [Nginx 安装](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)

之后，您可以在下面的目录中找到 nginx 配置。nginx 配置有两个重要的目录。

*   **sites-available** - folder 用于存储*你的所有【vhost 配置，无论它们当前是否启用。*
*   **sites-enabled** - folder 包含指向 sites-available 文件夹中文件的符号链接。这允许您通过删除符号链接来有选择地禁用 vhosts。

*   ![](img/c81dd99e8f425446f1d1d84e5e8a554f.png)

我们将部署一个 node.js 应用程序，并通过代理将请求传递给具有特定端口的应用程序。

完整源代码: [Nginx 演示](https://github.com/ganeshmani/nginx-demo)

首先，我们需要在没有 nginx(代理通过)的情况下运行应用程序，然后我们将看看如何使用 nginx 运行应用程序(我们不需要每次都提到**端口**)

下载应用程序的源代码，并使用以下命令在本地运行。

*   ![](img/95976b129e964fe661a2a925fd9c0454.png)

同时，如果你在浏览器中运行 **http://localhost:3000** 。您将看到应用程序成功运行。

*   ![](img/c02bd4c284c441a44130d43a3a4f0467.png)

该应用程序现在运行成功。现在，您需要配置 nginx，使应用程序运行时无需提及端口(代理传递)

主要是，我们需要在**站点-可用**和符号链接中添加应用程序配置到**站点-启用**

*   ![](img/c95733d55831e8e4f637e834869d2e95.png)

在 node-app 配置文件中，添加以下代码

```
server {

     listen 80;

      location / {

          proxy_pass "http://127.0.0.1:3000";

       }
}
```

*   ![](img/e3ce30b14363ad3e3deaf65391138a0e.png)

之后，在终端中运行以下命令

```
 $ sudo ln -s /etc/nginx/sites-available/node-app /etc/nginx/sites-enabled/ 
```

*   ![](img/7dad496cb0b5d2c44d8fbbe15c66f7dd.png)

之后，你需要启动 nginx 服务。运行以下命令启动服务器。

```
$  sudo /etc/init.d/nginx start 
```

*   ![](img/4632c4e647bdb9596c0b43d361616bd2.png)

最后，您可以在 **http://localhost** 中运行应用程序..耶！！！！

*   ![](img/91fdd0ed99a54028e71b685f2d7a6700.png)

在下一篇文章中，我们将看到如何在 Nginx 中实现负载平衡。到那时，**编码快乐！！！**

阅读第一部分， [Nginx，面向前端开发人员](https://cloudnweb.dev/2019/05/nginx-for-front-end-developers/)