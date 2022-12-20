# 用 Docker 在几分钟内在 Windows 上运行 MongoDB

> 原文：<https://dev.to/azure/mongodb-on-windows-in-minutes-with-docker-1cdi>

#### 在您的本地机器上快速、简单地启动并运行 NoSQL。

最近，我正在为即将到来的一系列演讲制作一个演示，需要一个 [MongoDB](https://www.mongodb.com/) 的(最好是本地)实例。如果您不熟悉 MongoDB，它是一个非常流行和成熟的文档数据库。MongoDB 的 API 由 [Azure Cosmos DB](https://jlik.me/evo) 支持，演示说明了如何从 MongoDB 的本地实例迁移到 Cosmos DB 的基于云的生产实例。只有一个问题:我不想在我的 Windows 10 机器上安装 MongoDB！

> **注意**:虽然我关注的是基于 Windows 的步骤，但同样的步骤在 macOS 和 Linux 上应该也能很好地工作。

幸运的是，这正是 Docker 旨在解决的场景类型。使用容器，您可以快速启动并运行任意数量的预定义图像和服务。Mongo 维护了一组官方 Docker 图片。

[![](img/d79e430378fe9f47cf87153513697f31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ByDLwU1C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/668/1%2Ao9xpeVkGgZRfaF2n8nNonw.png)

我假设您已经安装了 Docker，并且正在使用 Linux(不是 Windows)容器。我运行的是 Windows 专用版的社区桌面。

[![](img/c86b121f95f64fcc6f7c1c4c4f00ae09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v7vqHkvL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/905/1%2AMMF0537QLSHkAyKxN9zEbg.png)

既然先决条件已经解决，启动和运行 MongoDB 有两个步骤。首先，创建一个卷来保存运行之间的数据。如果跳过这一步，当容器停止运行时，您所做的任何更改都将消失。

```
docker volume create --name=mongodata 
```

名字可以是你喜欢的任何东西。如果数据库映像不存在，下一步将提取它，然后使用挂载的卷启动一个正在运行的实例。

```
docker run --name mongodb -v mongodata:/data/db -d -p 27017:27017 mongo 
```

您可以给运行容器起任何您喜欢的名字。第一次可能感觉像 npm 安装，因为下载了多个层，但后续运行应该会很快。

就是这样。你完了。您的机器上运行了一个功能完整的 MongoDB 版本！(这里是我的运行，映射到不同的端口)。

[![](img/c58caca11fba41c12488f81dfbab4cea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LjXhmySL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Azeo1DY-SghAKI4p7jvh6Mw.png)

当然，你可能想稍微调整一下。默认情况下，它运行时没有身份验证。要设置身份验证，您需要创建一个登录帐户，然后使用“身份验证”开关重新启动服务。

首先，登录到正在运行的(未经身份验证的)版本。

```
winpty docker exec -it mongodb bash 
```

(典型的 Windows 命令行需要 winpty。您可以省略它，从其他终端运行。mongodb 没有什么特别之处，除了它是我在上一步中给容器起的名字)。

打开 MongoDB 终端:

```
mongo 
```

假设您的数据库名为“mydatabase ”,您想要设置一个名为“myuser”的用户，密码为“secret”。这两个步骤会帮你解决这个问题(数据库*还没有*存在):

```
use mydatabase

db.createUser({user:"myuser", pwd:"secret", roles:[{role:"readWrite", db: "mydatabase"}]}); 
```

之后，您可以退出 MongoDB 终端和正在运行的 bash shell。接下来，停止并删除现有实例，启动一个新实例，并激活身份验证:

```
docker stop mongodb

docker rm mongodb

docker run --name mongodb -v mongodata:/data/db -d -p 27017:27017 mongo --auth 
```

现在，您可以使用连接字符串进行身份验证:

```
mongodb://myuser:secret@localhost:27017/mydatabase 
```

就是这样！希望这些简单的步骤有助于您在机器上启动和运行 MongoDB。

问候，

[![](img/97adde39a566ae152324f60361115585.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u6zXBAsP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/197/1%2AKwXn3ElS7vnWIk8lNVhBsA.gif)

* * *