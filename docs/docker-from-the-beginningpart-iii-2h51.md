# 从头开始学习 Docker，第三部分数据库和链接

> 原文：<https://dev.to/azure/docker-from-the-beginningpart-iii-2h51>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> TLDR；这是一个有点长的读数，但我保证麦克风在那里下降。中间有一点痛苦，但我们会战胜一切的:)

本文是系列文章的一部分:

*   [Docker——从第一部分](https://dev.to/softchris/docker---from-the-beginning-part-i-28c6https://dev.to/softchris/docker---from-the-beginning-part-i-28c6)开始，我们将讨论为什么要使用 Docker，以及管理图像和容器等东西所需的基本概念和命令。
*   [Docker——从第二部分](https://dev.to/softchris/docker-from-the-beginning---part-ii-5g8n)开始，这是关于卷以及我们如何使用卷来保存数据，以及我们如何将我们的开发环境转变为卷并使我们的开发体验显著改善
*   Docker —从头开始，第三部分，**我们在这里**
*   [Docker —从第四部分](https://dev.to/softchris/docker-from-the-beginning-partiv-mi6)开始，这就是我们如何使用 Docker Compose 管理多个服务(这是 Docker Compose 的 1/2 部分)
*   [Docker -从第五部分](https://dev.to/softchris/dockerfrom-the-beginning-part-v-n2c)开始，这部分是 Docker Compose 的第二部分，也是最后一部分，我们将讨论卷、环境变量以及如何使用数据库和网络

这是我们系列的第三部分。在这一部分，我们将重点学习如何一起使用数据库和 Docker。我们还将介绍*链接*的概念，因为这与容器化环境中的数据库紧密相关。

在本文中，我们将涵盖以下内容:

*   **介绍一些使用 MySql 的基础知识**，介绍一些管理数据库的基础知识总是有好处的，尤其是 MySql 是本文选择的数据库类型
*   **了解我们为什么需要使用 MySql Docker 图像**，我们还将介绍如何从所述图像创建容器，以及我们需要设置什么环境变量，以使其工作
*   **了解如何使用链接从我们的应用程序容器连接到我们的 MySql 容器**，这是关于如何实现两个容器之间的基本链接，以及如何在定义数据库启动配置时利用这一点
*   **扩展我们关于链接的知识**，通过介绍链接容器的新方法，有两种方式进行链接，一种方式比另一种方式更受欢迎，这种方式已被否决，因此我们将介绍新的工作方式是如何完成的
*   描述一些管理我们数据库的好方法，比如给它一个初始结构和种子

## 资源

使用 Docker 和容器化就是将一个整体分割成多个微服务。在整个系列中，我们将学习掌握 Docker 及其所有命令。迟早您会想要将您的容器带到生产环境中。这个环境通常是云。当你觉得你已经有了足够的 Docker 经验时，看看这些链接，看看 Docker 是如何在云中使用的:

*   [云中的容器](https://docs.microsoft.com/en-gb/azure/containers/?wt.mc_id=academic-0000-chnoring)这是一个很棒的概述页面，展示了关于云中容器的其他信息
*   [在云中部署您的容器](https://docs.microsoft.com/en-gb/azure/container-instances/container-instances-tutorial-prepare-app?wt.mc_id=academic-0000-chnoring)教程展示了利用您现有的 Docker 技能并让您的服务在云中运行是多么容易
*   [创建容器注册表](https://docs.microsoft.com/en-gb/azure/container-instances/container-instances-tutorial-prepare-acr?wt.mc_id=academic-0000-chnoring)您的 Docker 映像可以在 Docker Hub 中，也可以在云中的容器注册表中。将您的图像存储在某个地方，并能够在几分钟内从该注册表创建一个服务，这不是很棒吗？

## 处理数据库，尤其是 MySql

对于一般的数据库，我们希望能够做到以下几点:

*   **read** ，我们希望能够通过使用不同种类的查询来读取数据
*   **改变/创建/删除**，我们需要能够改变数据
*   **添加结构**，我们需要一种方法来创建像表或集合这样的结构，以便我们的数据以特定的格式保存
*   **添加种子/初始数据**，在一个简单的数据库中，这可能根本不需要，但在更复杂的场景中，您将需要一些表格来预填充一些基本数据。在开发阶段有一个种子也很好，因为如果已经有预先存在的数据或者数据处于某种状态，例如购物车有商品，并且您想要测试结帐页面，那么它可以很容易地呈现某些视图或者测试不同的场景。

我们还想对数据库做更多的事情，比如添加索引、添加具有不同访问权限的用户等等，但是让我们把重点放在上面这四点上，作为我们在 Docker 的帮助下应该能够支持的参考。

### 安装并连接 MySql

安装 MySql 的方法有很多，但并不是所有的方法都很快。在 Linux 系统上，最简单的方法之一是键入以下内容:

> 安装 mysql-server

在 Mac 上，您应该使用 brew，然后键入:

> brew 安装 mysql

在其他一些情况下，您可以下载一个安装包并按照向导进行操作。

一旦你完成了 MySql 的安装，你会得到一些类似的信息，当然每个安装包会有所不同:

[![](img/9476865f63fd6979efbfcf9db9923566.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GdC_VSAJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABxTYhoJ_clYeKZhWE44uJg.png)

上面告诉我们，我们还没有一个根密码，呀。我们可以通过运行 mysql-secure-installation 来解决这个问题。现在让我们连接到运行建议的 mysql -uroot 的数据库。

[![](img/43db59ba61dd0cdb70d6ffadc2628715.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZylcYwFl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AE-dnIn17AStRYHGYmGdW6A.png)

不，发生什么事了？我们实际上在上面的大图中获得了这些信息，我们需要通过运行 brew services start mysql 来启动 MySql，这将作为后台服务运行它，或者使用 mysql-server start，这更像是一次性的。好了，让我们进入 brew 服务开始:

[![](img/461819de1092b64c961f87ce6e588ee7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kv4pt4sC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhbnVt0MIAI5i8UDmdJaTtQ.png)

最后一件事是，它说成功启动 mysql:

[![](img/04aef6b37ccceda20f0664b07d7b6c6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vHQ4elCH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/466/1%2A-Zi5Ebc6BPSJlH8YvHbsgQ.gif)

让我们看看马修是不是正确的，我们可以连接吗？

[![](img/f62af7e0ab019e04f08d87354fd67281.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bcceAmGG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHn3sSFf04Dxy6flp2B17YQ.png)

我们在`mysql>`上面得到一个提示，我们在:D

好的，所以我们设法不使用密码连接，我们应该解决这个问题，我们没有创建任何数据库，我们也应该解决这个问题:)

这不完全是真的，我们确实有一些数据库，不是任何一个包含你自己创建的内容的数据库，而是一些我们不应该接触的支持性数据库:

[![](img/cbb30668770ff44439aa1c1736eb28e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fwPmAcoS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/938/1%2AsiqRgXGPjOotjMGfLkw7OQ.png)

接下来，我们将创建并选择新创建的数据库，这样我们就可以从中进行查询:

[![](img/ecb56e74f1f069bfc1f2e52c2367902e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q-QEjtbs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/772/1%2Afu4U70ZWwx8jkNQ2x90tVA.png)

好的，太好了，但是等等，我们没有桌子了？千真万确，我们需要以某种方式创造这些。我们可以在终端中创建它们，但是那样会很痛苦，会有很多很多的多行语句，所以让我们看看是否可以给 MySql 提供一个包含数据库和所有我们想要的表的文件。让我们首先定义一个可以不断添加表的文件:

```
// database.sql

// creates a table `tasks`
CREATE TABLE IF NOT EXISTS tasks (

task_id INT AUTO_INCREMENT,

title VARCHAR(255) NOT NULL,

start_date DATE,

due_date DATE,

status TINYINT NOT NULL,

priority TINYINT NOT NULL,

description TEXT,

PRIMARY KEY (task_id)

);
// add more tables below and indeces etc as our solution grows 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们有了一个带有数据库结构的文件，现在为了获取其中的内容，我们可以像这样使用 source 命令(屏蔽用户名):

[![](img/f07c304030023103ea017c5a58ea4530.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VPLStFbu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACQcfs7-Sn0_leH2FxFHZWw.png)

如果您想要您的文件所在位置的完整路径，只需键入您的文件所在的 PWD，然后键入 source[sql 文件的路径]/database.sql。正如您在上面看到的，我们需要在运行我们的 SQL 文件之前选择一个数据库，以便它针对特定的数据库，然后我们验证该表是否已使用 SHOW TABLES 创建；我们还可以使用相同的命令在数据库中植入数据，我们只需给它一个不同的文件来处理，这个文件包含 INSERT 语句，而不是 CREATE TABLE...

那好吧。我认为现在 MySql 已经足够了，接下来让我们在 Docker 的上下文中讨论 MySql。

## 为什么我们需要一个 MySql Docker 映像，以及如何让它作为一个容器运行

好的，现在让我们假设我们的应用程序需要一个容器。让我们进一步说，我们的解决方案需要一个数据库。在计算机运行的主机上安装 MySql 没有多大意义。我的意思是容器的一个咒语是我们不应该关心容器运行的主机系统。好的，我们需要一个容器中的 MySql，但是哪个容器，应用程序自己的容器还是一个单独的容器？这是一个很好的问题，取决于你的情况，你可以在你的应用程序中安装 MySql 或者在一个单独的容器中运行它。对此的一些观点是:

*   **滚动更新**，当您的应用程序节点单独重启时，您的数据库可以保持在线，您不会经历停机。
*   **可扩展性**，您可以添加节点，以松散耦合的方式进行扩展

有很多支持和反对的理由，只有你知道什么对你最合适——所以你做你自己:)

### MySql 作为独立镜像

让我们来讨论一下下拉 MySql 图片的场景。好的，我们将一步一步来，所以我们要做的第一件事是尝试运行它，正如我们从以前的文章中了解到的，如果我们没有它，图像将被拉下来，所以命令如下:

> 停靠器运行名称=mysql-db mysql

好的，那么输出是什么呢？

[![](img/80199aa25127a62513190e24c619fc83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6pZrqq0L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhrpOZwekpdv1yByOYQp4TQ.png)

拉下图像，很好，AAA 和错误。

我们在做各种各样的错事:(。

数据库未初始化，未指定密码选项等等。让我们看看是否能解决这个问题:

> docker run-name MYSQL-d b-e MYSQL _ ROOT _ PASSWORD = complex PASSWORD-d-p 8001:3306 MYSQL

获胜者是:

[![](img/8a807a2e616e7648fca839cd87633d95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pU7XPeUm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKM2niK5vNNUfVDDSX9SCYA.png)

啊，我们之前启动的容器已经启动并运行了，尽管它抛出了错误消息。好了，让我们把容器拿下来:

> rm mysql-db 停靠器

让我们尝试使用如上设置的数据库再次运行它:

[![](img/ae6505f79755212e9147d16f288e69ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u3CayyFk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYQIXSpKvsdR0NqYG3oNbqQ.png)

好了，我们没有得到一大堆登录到终端的日志，因为我们是在守护模式下运行的，所以让我们运行 docker ps 来检查:

[![](img/ccc4a5032e565ea71be79f5f7db7c26b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sVPoQzu6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ahp8_3EcNM4oyUto2Gcgr6A.png)

此时，我们希望从外部连接到它。我们的端口转发意味着我们需要在 0.0.0.0:8001:

> MySQL-u root-pcomplex password-h 0 . 0 . 0 . 0-P 8001

好的，只是上面的一个注释，我们可以指定密码或者只写-p，下一行会提示我们输入密码。让我们来看看结果:

[![](img/1017a83f52bc0b44ff15fc443b6af608.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C9Zb4CnZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0dC2tjkdlD2byMFsYPj2Ug.png)

好的，我们设法连接到我们的数据库，它是可达的，太好了:)。

但是等等，我们能从另一个容器中访问容器内部的数据库吗？这就是棘手的地方。这是我们需要连接两个容器的地方。

### 从 Node.js 连接数据库

好，让我们首先在我们的应用程序中添加一些代码，尝试连接到我们的数据库。首先，我们需要为 mysql 安装 NPM 包:

> npm 安装 mysql

然后，我们将以下代码添加到 app.js 文件的顶部:

```
// app.js

const mysql = require('mysql');

const con = mysql.createConnection({

host: "localhost",

port: 8001,

user: "root",

password: "complexpassword",

database: 'Customers'

});

con.connect(function (err) {

if (err) throw err;
 console.log("Connected!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

因此，让我们在终端中尝试一下:

[![](img/6cc7f56622d559fefa1f89457e68e850.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6OTEIpVE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJzE2uye8-GxIZyDNmf7izw.png)

痛苦和不幸

所以为什么不行。

> 这是因为 MySQL 8.0 中引入了 caching_sha2_password，但是 Node.js 版本还没有实现。

好吧，那又怎样，我们试试 Postgres 或者其他数据库？实际上，我们可以通过如下方式连接到我们的容器来解决这个问题:

> MySQL-u root-pcomplex password-h 0 . 0 . 0 . 0-P 8001

在 mysql 提示符下，我们可以输入以下内容:

```
mysql> ALTER USER 'root' IDENTIFIED WITH mysql_native_password BY 'complexpassword';
mysql> FLUSH PRIVILEGES; 
```

Enter fullscreen mode Exit fullscreen mode

让我们再次尝试运行 node app.js 命令，这一次我们得到的是:

[![](img/078f78c7963317af5c71d05b6cbf4b83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---oR4J3X1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ad05U7bFAJlnKszKcxsTxRg.png)

终于！

好了，对这个 mysql_native_password 的一些调用似乎可以解决所有问题。让我们深入兔子洞，那是什么？

> MySQL 包含一个 **mysql_native_password** 插件，实现原生认证；也就是说，在引入可插拔身份验证之前，身份验证基于一直在使用的密码哈希方法

好的，这意味着 MySql 8 已经切换到一些新的可插拔认证，这是我们的 Node.js mysql 库无法赶上的。这意味着，我们可以下载 MySql 的早期版本，或者恢复到原生身份验证，随你便:)

### 链接

链接的思想是容器不需要知道数据库在哪个 IP 或端口上运行的任何细节。例如，它应该假设应用程序容器和数据库容器可以互相访问。典型的语法如下所示:

> docker run -d -p 5000:5000 - name 产品-服务**-link mypostgres:postgres**Chris noring/node

让我们把上面的分解一下:

*   **应用程序容器**，我们正在创建一个名为产品服务的应用程序容器
*   - link 我们调用这个命令来链接我们的产品服务容器和现有的 mypostgres 容器
*   **链接别名**，语句- link mypostgres:postgres 的意思是我们指定用 mypostgres 链接什么容器，并给它一个别名`postgres`。当我们试图连接到我们的数据库时，我们将在`product-service`容器内部使用这个别名

好了，我们认为我们已经了解了链接的基本知识，让我们看看这如何应用到我们现有的 my-container，以及我们如何将它链接到我们的数据库容器`mysql-db`。因为我们没有链接就启动了我的-container，所以我们需要把它拆下来，用我们的- link 命令重新启动它，该命令被指定为一个附加参数，如下所示:

> docker kill my-container & docker RM my-container

把集装箱拿下来。在我们提出这个问题之前，我们实际上需要修改一些代码，也就是与连接数据库有关的部分。我们将使用`--link mysql-db:mysql`参数将它与我们的数据库容器链接起来，这意味着我们不再需要 IP 或端口引用，所以我们的连接代码现在看起来像这样:

```
// part of app.js, the rest omitted for brevity

const con = mysql.createConnection({

**host: "mysql",**

user: "root",

password: "complexpassword",

database: 'Customers'

});

// and the rest of the code below 
```

Enter fullscreen mode Exit fullscreen mode

不同之处在于，现在我们的主机不再声明 localhost，端口也不再显式声明`8001`，因为链接的结果是，我们需要关注的是，当我们链接时，我们给了数据库容器什么别名，例如`mysql`。因为我们都修改了代码并添加了另一个库 myql，所以我们需要重新构建映像，如下所示:

> docker 构建- t chrisnoring/node。

现在让我们重新开始，这次有一个链接:

> docker run-d-p 8000:3000-name my-container-link MySQL-db:MySQL Chris noring/node

让我们来看看输出:

[![](img/a4f150e20b81d708aa6d696168250f92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4HNbuSkO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASxqwuggAPju3MpiJwy04sQ.png)

我们使用 docker logs my-container 来查看应用程序容器中的日志，最后一行写着 Connected！它来自回调函数，告诉我们已经成功连接到 MySql。

你知道现在几点了吗？BOOOM！

[![](img/d9215debdb9d6f16b55ded362cda27df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BL5Lu3oF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2APankPb9dXZglAwLMqW1y-Q.gif)

## 扩展我们关于链接的知识——还有另一种方法

好吧，这一切都很好。我们设法将应用程序放在一个容器中，将数据库放在另一个容器中。但是…我们刚刚看到的链接被认为是遗产，这意味着我们需要学习另一种链接方式。等等…没你想的那么差，实际上从语法上看更好。

这种新的方式被称为创建网络或自定义桥接网络。事实上，这只是我们可以创建的一种网络。关键是，他们是你可以说你的容器应该属于的专门团体。一个容器可以存在于多个网络中，如果它有多个组的交叉关系。这听起来很棒，所以给我看一些语法，对吗？

我们需要做的第一件事是创建我们希望应用程序容器和数据库容器所属的网络。我们使用以下命令创建所述网络:

> docker 网络创建-驱动程序桥隔离 _ 网络

现在我们有了自己的网络，我们只需要用新创建的网络作为附加参数来创建每个容器。让我们从应用程序容器开始:

> docker run-d-p 8000:3000**-net isolated _ network**-name my-container Chris noring-node

上面我们正在使用- net 语法来创建一个网络，我们称之为 isolated_network，剩下的只是我们用来创建和运行容器的常用语法。那很简单:)

数据库容器呢？

> docker run-p 8001:3306**-net isolated _ network**-name MYSQL-d B- e MYSQL _ ROOT _ PASSWORD = complex PASSWORD-d MYSQL

正如您在上面看到的，我们只是创建并运行了我们的数据库容器，但是添加了- net isolated_network。

回头看看这种方式，我们不再需要明确地说一个容器需要主动链接到另一个容器，我们只需要将一个容器放置在一个特定的网络中，并且该网络中的容器知道如何相互通信。

关于网络还有很多要学的，有不同的类型和一大堆命令。我认为我们已经抓住了它的要点，例如，它与传统链接有什么不同。查看此链接，了解更多关于网络的[文档](https://docs.docker.com/network/links/)

## 通用数据库管理中的容器设置

好了，我们在本节开始时讨论了如何将数据库结构创建为文件，如何将种子数据创建为文件，以及如何在连接到 MySql 后，在 MySql 提示符下运行这些文件。事情是这样的。我们如何将结构和种子数据放入其中，这有点取决于您，但我会尝试传达一些通用的指导原则和想法:

*   **结构**，你想在某个时候运行这个文件。要进入数据库，您可以使用`mysql`客户端连接，并参考主机外部的文件。或者你可以把这个文件作为你的应用程序报告的一部分，让 Dockerfile 把文件复制到容器中，然后运行它。另一种方法是查看 Knex 或 Sequelize 之类的库，这些库支持迁移并指定正在运行的迁移中的数据库结构。这是一个完全不同的话题，但我只是想告诉你有不同的方法来处理这个问题
*   **种子**，你可以像对待结构一样对待种子。这是我们进入结构后需要做的事情，而且是一次性的。你不希望在每次启动应用程序或数据库时都出现这种情况，根据种子是一些基本的结构数据还是一些测试所需的数据，你很可能希望手动执行，例如，你使用客户端连接到`mysql`并主动运行源命令

## 总结

好了，这篇文章设法涵盖了一些关于 MySql 的一般信息，并讨论了我们如何在其中获得一些结构化的东西，如表，我们还谈到了一个初始/测试种子，以及如何使用独立文件将它放入数据库。

然后，我们讨论了为什么我们应该在 Docker 容器中运行数据库，而不是安装在主机上，并继续展示了如何使用遗留链接技术让两个容器相互通信。我们也花了一些时间咒骂 MySql 8，事实上,`mysql` Node.js 模块不同步，这迫使我们修复它。

一旦我们讨论了传统的链接，我们就不能就此停止，但是我们需要讨论链接容器的新方式，即使用网络。

最后，我们分享了一些关于数据库管理的通用指南，希望你现在已经了解我们如何将数据库添加到我们的应用程序中，并继续开发应用程序。

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

* * *