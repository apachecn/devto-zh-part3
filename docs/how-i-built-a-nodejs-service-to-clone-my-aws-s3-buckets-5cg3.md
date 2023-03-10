# 我如何构建 Node.js 服务来克隆我的 AWS S3 存储桶

> 原文：<https://dev.to/rajeshbabu1991/how-i-built-a-nodejs-service-to-clone-my-aws-s3-buckets-5cg3>

[![](img/e7c8659c26fb97a562837655d58551ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XJnKh2Yn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2A7pNMMZbTGFovZSlL9b10ew.jpeg)

由[马尔特·温根](https://unsplash.com/photos/4AG-k7KeNOg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/node-js?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

**TL；博士**——*直奔主题！！！*

[**rajeshdavidbabu/Node-Clone-S3-Bucket**

_ 递归和本地克隆 S3 Bucket 或其任何目录。—rajeshdavidbabu/Node-Clone-S3-Bucket _ github . com](https://github.com/rajeshdavidbabu/Node-Clone-S3-Bucket)

### 简介

#### Node.js

Node.js 已经在市场上存在很长时间了，作为一名 Javascript 开发人员，现在您应该了解它是如何工作的了。与流行的前端开发人员的想法相反，Node.js 不是一个库，相反，它是一个生态系统，更像是一个可以运行 Javascript 的平台，最好的部分是你可以访问各种 I/O 操作。

> 所有前端 javascript 在 V8 和浏览器环境本身的帮助下在浏览器上运行，同样，Node.js 涉及 V8 引擎和 Libuv。
> 
> 如果你是 Node.js 的绝对入门者，请通过这篇文字来全面了解一下 [***为什么以及如何到 Node.js***](https://codeburst.io/the-only-nodejs-introduction-youll-ever-need-d969a47ef219) ***？*T9】**

因为这篇文章是关于构建一个功能完整的 Node.js 应用程序，所以我们不会深入研究基础知识，而是快速触及需要注意的关键点以及对应用程序本身的一点了解。

#### AWS(亚马逊网络服务)

> AWS 是一个基于云的生态系统，您可以在其中管理应用程序的整个基础架构，而不需要任何物理硬件或服务器。

简而言之，AWS 为寻求存储数据、访问数据、运行服务器、扩展现有服务等的公司提供了广泛的解决方案。在这些服务中，最重要和最简单的服务之一是 S3(简单存储服务)。S3 不仅提供了块存储空间，这意味着您可以在其上存储任何文件格式，而且还附带了一组 REST APIs，通过它们您可以进行 CRUD 操作。

当 Node.js 应用程序需要随机存储和检索文件、存储可执行文件或任何其他不受 ACID 兼容数据库(如 SQL DBs)支持的数据格式时，上述两种技术在行业中形成了强大的组合，适用于各种用例。简而言之，它是一个文件存储系统，你可以在其中存储任何类型的数据并轻松访问它们。_

### 这个项目是关于什么的？

> 这个项目基本上是构建一个 Node.js 应用程序来本地递归克隆你的 S3 桶。T3】

TBH，这个项目是使用 AWS-CLI 的一行程序。是的，你没听错。那么我们为什么要这么做呢？

***“为什么所有的问题必须总是只有一个解？我只是喜欢不止一个。*无论好坏**

我们先来看看现有的解决方案。安装 AWS-CLI 并运行以下命令:

aws s3 cp s3://my-s3-bucket/。/ -递归

> 令人惊讶的是，除了使用 AWS CLI 之外，我没有找到任何合适的 Node.js 脚本或应用程序可以使用 AWS-SDK 为中型到大型的桶执行此操作。我在 Node.js 在线帖子上找到的答案有几个问题，包括半生不熟的脚本，试图同步创建文件的脚本，不知道何时完成，如果有空文件夹，也会忽略克隆。基本上，它没有把工作做好。因此，我决定自己写一个，适当的。

我确信这将使您更好地理解 Node.js 应用程序的外观和感觉，不管它的大小和操作如何。

### 你对这篇文章有什么期待？

正如我上面所说的，我不会一行一行地解释代码，因为我会把整个基础贴出来。相反，我将谈谈我是如何设计这个应用程序的，对核心逻辑和关键特性有一些了解。让我列出你可以期待的，并在这篇文章结束时得到一个想法。

*   ***我们申请的项目结构***
*   ***核心逻辑围绕应用***
*   ***Streams in Node.js 下载文件***
*   ***使用 AWS-SDK 访问 S3 API***
*   ***整个代码库***

### 出发前

在我们草率行事之前，让我们问一下为什么我们称之为服务而不是服务器。我称之为服务，因为它只做一项工作，没有外部实体向我们正在运行的服务发送请求，如果它们向我们的应用程序发送请求，那么我可能会称之为服务器，如果它正在侦听端口的话。

> 当我还是一名初级前端开发人员时，我一直认为 Node.js 应用程序只由一个服务器文件组成，并认为代码总是混乱的，虽然在某种程度上这是正确的，但我后来知道这不应该是这样的。作为一个负责任的开发人员，我们应该始终致力于保持文件尽可能小，并适当地分离关注点。这是开发可伸缩 Node.js 应用程序的关键。

### 项目结构

构建项目结构没有固执己见的方法，它可以根据用例从一个项目到另一个项目而变化。就我个人而言，我把它们分成了更小的独立模块。一个模块执行一种类型的任务，并且只执行一种类型的任务。

> 比方说，如果我有一个***network . js***file ***，** *它只做网络操作，不修改文件结构或创建新文件。

例如，让我们看看我们的项目结构，

[![](img/fcf38fb680b03db4d4dab2e295b34319.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FlgVp4SF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A00Gb_2AuNJuQe_nJ2tK_Nw.png)

项目结构

正如我之前说过的，没有什么特别的方法来组织你的项目，但是理想的方法是选择一个主题，然后将你所有的文件归到这个主题下。对我来说，它是活动， ***“文件处理什么，如何处理？”。*T3】**

让我们从根源开始，一步一步来。

#### 应用依赖

这些是项目依赖项，对于开发和部署是必不可少的。大多数人都很容易理解:

*   *package.json*
*   *index.js*
*   *git 忽略/ESL config*
*   许可证、自述文件等。
*   *节点 _ 模块*

然后是 ***config*** 文件， ***config*** 文件包含你所有的应用配置，api_keys，bucket 名称，目标目录，第三方链接等等。通常我们会有两个配置文件，一个用于*生产*，一个用于*开发*环境。

#### 核心实体

一旦我们用应用程序依赖关系准备好了应用程序的框架，那么我们就有了核心实体。在我们的应用程序中，核心实体包括处理程序、服务和存储。

> ***处理程序*** 是我们粘合整个应用程序、创建服务、存储、注入所需的依赖项并公开一个 API 供 index.js 处理的地方。
> 
> ***服务*** 是我们应用程序的核心逻辑所在，所有其他依赖项的工作都从这里委托。
> 
> ***存储*** 是我们所有存储操作发生的地方。在我们的应用程序中，S3 是我们从中检索文件和数据的外部存储，因此 AWS-SDK 操作只发生在这个存储模块内部。

#### 助手和工具

当服务开始运行时，它需要同时执行所有预期的任务。例如，在我们的应用程序中，一旦我们获得了目录下的内容列表，我们需要开始在本地创建/克隆内容。这个操作委托给 ***cloner.js*** ，一个只负责克隆文件和文件夹的助手。反过来，克隆者需要访问 ***fileOps.js*** 模块来创建目录和文件。

> 在 helpers 下，我们有以下文件在有或没有其他助手的帮助下完成它们各自的杂务。
> 
> ***克隆器*** ，借助 ***fileOps*** 模块处理文件和目录的克隆。
> 
> ***数据处理器*** ，将来自 S3 桶的数据映射并解析成服务可用或可消费的数据。
> 
> ***下载器*** ，仅从 S3 桶下载文件，并将操作管道化为写流，或者简单地说，负责异步下载文件。
> 
> ***fileOps*** 顾名思义，使用 Node 的 **fs** 模块来创建文件流和目录。
> 
> ***filePath*** 为整个应用程序提供目标文件夹，用于克隆 S3 存储桶的文件和目录，并返回 S3 上的目标存储桶和目标根目录。
> 
> ***Logger****utils 内部返回一个 Bunyan logger 的实例，该实例可用于向 Kibana 这样的第三方发送日志。*

### 围绕应用的核心逻辑

现在我们已经完成了项目设置，让我们看看 ***服务*** 模块的核心逻辑。它包括以下一系列动作:

*   ***从桶和目标前缀中取出键的列表。*** *(检查 AWS-SDK Javascript API)*
*   ***把文件和目录分开，因为我们克隆目录，下载文件。*T3】**
*   ***先克隆所有目录，然后继续下载文件。*T3】**
*   ***通过 streams 下载文件，分别记录成功和失败。*** *(AWS-SDK ListKeys API 响应有时会忽略给出目录密钥，因此我们需要检查目录是否存在，如果不存在，我们会在下载包含的文件之前创建一个目录)*

> 此外，该服务不仅克隆整个 bucket，而且还克隆特定的文件夹，而不会丢失文件夹树结构，这是基于我们的前缀配置，此处指定[为用于克隆的根目录。](https://github.com/rajeshdavidbabu/Node-Clone-S3-Bucket#configuration)

### 使用流下载文件

围绕 Node.js 的另一个重要概念是使用流从外部源上传和检索数据。在我们的项目中，外部来源是 AWS S3。

> 当下载文件时，我们从 AWS SDK getObject 方法创建一个读取流，并通过管道将其传输到 writeStream，一旦所有数据都被写入，write stream 将自动关闭。使用流的优点是内存消耗，应用程序不关心下载文件的大小。

下面所示的 ***存储*** 模块中的代码使用流来异步下载数据，而不会阻塞事件循环。

[![](img/4f69f589d10a326c1ad275e96c14d7c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TTmzN2CC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ALiO9-odlUOi1HgG-wcSd6w.png)

带有 AWS getObject 的 Node.js 流

要更深入地挖掘 Node.js 流，请参考本文[这里的](https://medium.freecodecamp.org/node-js-streams-everything-you-need-to-know-c9141306be93)。

### 使用 AWS SDK 访问 S3

这是整个应用程序中最直接的主题，在这里安装 ***AWS-SDK*** 并开始访问其中的方法。查看一下 ***存储*** 文件会让您更好地理解如何在相同的上导入和调用方法。

### 应用的代码库。

在这里你可以找到这个应用程序的全部代码，而不仅仅是阅读，动手操作会提供大量的信息并帮助你理解这个应用程序的核心概念。随意叉它，玩它，如果你喜欢它，在回购上留下一个星星。

[**rajeshdavidbabu/Node-Clone-S3-Bucket**

_ 递归和本地克隆 S3 Bucket 或其任何目录。—rajeshdavidbabu/Node-Clone-S3-Bucket _ github . com](https://github.com/rajeshdavidbabu/Node-Clone-S3-Bucket)

### 结论

这标志着这篇文章的结束，希望它能更好地理解如何在 AWS 这样的平台上实时规划、构建和运行 Node.js 服务。感谢阅读，并随时评论！