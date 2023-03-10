# 像网络开发人员一样思考

> 原文：<https://dev.to/eckhardtd/thinking-like-a-web-developer-586j>

[![](img/d1a0b411d73646d4bf366dc9d5aa3131.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dFr69klh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7bkc9wp1fza6nsw293f2.jpg)

<small>*Unsplash 上 Fatos Bytyqi 的照片*</small>

像 web 开发人员一样思考意味着什么？在你的职业生涯中，你会遇到哪些事情？本质上，关于帕累托-什么是网络开发中的 80/20 法则。

# 堆栈

栈并不真的特定于 web 本身，但是你会经常听到 web 开发人员说“我是一个全栈开发人员”或者“我在前端工作”或者“我是一个后端(有时是 API)开发人员”。他们的意思是:

#### 前端

你花很多时间把设计翻译成 HTML 和 CSS。你也会经常深入到像 [React](https://reactjs.org/) 或 [Vue](https://vuejs.org/) 这样的框架，像 [Webpack](https://webpack.js.org/) 这样的捆绑器，以及像 [Babel](https://babeljs.io/) 这样的传输器。

#### 后端

作为一个后端开发人员，你不会花太多时间在视觉方面。你会看很多代码，逻辑会是你最好的朋友。您的任务范围将从数据库管理到 RESTful API 的测试端点的定期！

作为一个有偏见的 Javascript 开发人员，我想在这里提到这些工具，但是最终服务器并不像浏览器那样受限，许多语言和框架都可以用来实现这一点。

# Javascript 框架

你会经常看到关于一天内创建的 JS 库和框架数量的模因和笑话。节点的软件包管理器，将是一个日常工具，你可以用它来安装有用的软件包到你的应用程序中。

一些较大的安装如 React 和 Vue 被认为是框架。不像库——框架要大得多，调用你的代码，你可能要花几个星期去学习它们。好的一面是，一旦你认识了一个，你会变得更有竞争力，你的生产力也会提高。

# trans file vs . bundle？

这是您在前端(有时甚至是后端)开发中经常会遇到的事情。首先，它们是什么？

#### 运输文件

Transpiling 是获取您编写的代码并对其进行更改的过程。有时它意味着要翻译成其他语言，但在 Javascript 的情况下，您通常会从较老的浏览器尚不支持的较新的语言特性转换成它们支持的版本。例如:

```
const theArray = [2, 10];
const [amount, total] = theArray;

// Transpiled to
var theArray = [2, 10];

var amount = theArray[0];
var total = theArray[1]; 
```

巴别塔在这方面很棒。

#### 捆绑

当您开始前端开发时，您通常会创建一个 html 文件。例如:

```
<!DOCTYPE HTML>
<html>
  <head>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <script src="app.js"></script>
  </body>
</html> 
```

在这种情况下，你通常只需将所有的 JavaScript 写在 **app.js** 文件中，将 css 写在 **style.css** 文件中。如果您在几个文件中编写 JavaScript 和 CSS，并简单地将它们包含在您的主 html 文件中，这种方法非常有效。

但是当应用程序增长并且你的文件列表真的很疯狂的时候(如果你想到所有那些包)，使用 bundler 变得很聪明，它会神奇地为你构建所有的代码，你只需要包含一两个捆绑的文件。

# 关系与非关系

在数据库的世界里——保存网站或 web 应用程序数据的地方。

您会经常听说像 SQL 这样的关系数据库，它具有类似表格的结构。它们通常以相互引用的方式存在，并且有行和列以及特定的语法来操作数据。

非关系数据库更加灵活。它们没有固定的结构，你可以存储任何数据结构，通常是在“文档”中。 [MongoDb](https://mongodb.com) 是 Node.js 世界的常用服务。

出于安全原因，这些服务通常是在服务器上运行的 T2。但是也有客户端选项可用。除了浏览器内置的`localStorage`——你也可以使用类似 [Firebase](https://firebase.google.com/) 的服务。

它允许您直接从浏览器使用 Firestore 查询连接非关系数据库并执行操作。根据 Firestore 规则，安全将在 firebase 控制台上执行。

`example of Firestore rules`

```
// Allow read/write access on all documents to any user signed in to the application
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth.uid != null;
    }
  }
} 
```

数据库是后端开发的重要组成部分，您将花费大量时间来确保数据库的安全和高效。

# 提高你的逻辑

总的来说，编程的很大一部分是改进你的代码，这一点不能被忽略。毫无疑问，随着时间的推移，你会看到前一年的代码，并感到奇怪。漂亮、高效的代码是没有限制的，实现它只需要简单的实践和经验。

#### 改进代码的具体方法

这里有一些实用的方法可以确保你的代码得到改进。一夜之间不会有所改善，但可能会有所帮助。

*   从可靠的来源学习数据结构和算法。
*   通过参加类似[代码大战](https://www.codewars.com)的挑战来练习。
*   让有经验的开发人员审查你的代码。
*   定期查看您自己的旧代码，并尝试用新鲜的眼光理解它。
*   应用 DRY 原则——只重复那些重复比可重用更有意义的东西。
*   建造东西。
*   看看其他代码，无论是来自经验丰富的开发人员的文章和视频，还是来自 Github 仓库——看看结构和原理，并与您自己的进行比较。

# 去吧

Git 对任何软件开发人员都很重要，不能被忽略。GitHub 是一个版本控制托管服务，允许你使用 git 命令提交和推送你的代码。它跟踪版本，以便于维护。在 git 中您想知道的一些事情:

*   分支
*   分支
*   命令行界面
*   拉/取
*   贡献的

事实证明 Git 非常有用，几乎是任何项目协作的必备工具。

# 剩余 API

#### 休息

REST 是作为创建 Web 服务的一种手段而开发的一种原则。基本上，它是客户端和服务器通信来操纵资源的方式。通信是一篇独立的文章，但一般来说，它是到达`GET`、`POST`(或发送)、`PUT`等的一种方式。服务器和客户端之间的数据。

#### HTTP

HTTP 是万维网上使用的传输协议。“超文本”文档以请求-响应模式从服务器传输到客户端。每当我们在浏览器(客户端)中输入一个网址时，它就向服务器发送一个请求(有时请求只针对某些数据)。如果请求的资源存在，并且通过了其他检查，服务器将使用该资源进行响应。

#### JSON

Javascript 对象符号是数据的一种形式(结构)——它的结构类似于 Javascript 对象，因此得名。它看起来像这样:

```
{
  "people": [
    {
      "name": "John",
      "email": "john@doe.com"
    },
    {
      "name": "Jane",
      "email": "jane@doe.com"
    }
  ]
} 
```

今天的许多 HTTP 请求都是针对一堆 JSON 数据，这些数据在客户机(浏览器)上被处理和呈现。

作为一名 web 开发人员，理解 REST、HTTP 和 JSON 非常重要。在开发 web 服务时，您会经常用到这些原则。

#### 设计 API

为了把它带回家，API 的开发使用了所有这些原则。Node.js 的“应用编程接口”(或 API)通常是用类似于 [Express](https://expressjs.com) 、MongoDb 和其他助手库的服务开发的。

路由被定义在 URL 的末尾，如`/home`或`/about`，每个路由都为请求者服务。获取、发布、上传、删除等。可以在发出请求时用来指定请求者希望从服务器得到什么，服务器会及时响应——使用您从所有实践中辛苦编写的代码。

# 托管

所有这些代码，无论是前端还是后端，都需要托管在互联网上。

有许多主机提供商可用。对于静态前端 HTML、CSS 和 Javascript，你可以选择其中任何一种(有些比其他的好)。但是很多提供商仍然只提供 PHP 服务器，如果你正在编写 PHP，这是非常好的。

您只需构建您的静态文件，通过 FTP 连接到您的服务器，并将文件发送到域名服务器。瞧啊。

#### 托管 Javascript 服务

Javascript(节点)服务器有点不同。没有多少提供者允许在他们的服务器上进行 root 访问，您可以在那里安装和运行节点服务器。

为此，我们有一些其他类似容器的服务，如 [Docker](https://www.docker.com) 、 [Now](https://zeit.com/now) 或 [Heroku](https://www.heroku.com) 。另外，AWS 和谷歌云也提供服务。

像 Meteor 和 Nuxt SSR 这样的 Javascript 框架需要托管在允许 Node.js 服务的服务器上。

Web 开发有很多面。从找到自己在堆栈中的位置，到掌握框架——有很多东西需要思考。

# 那么一个 web 开发者是怎么想的呢？

这篇文章不应该告诉你一些突破性的思维导图。它也不意味着教你任何栈，框架或语言。写这篇文章是为了让你开始像网络开发者一样思考。我们有很多东西要思考，作为一个初学者，有很多东西要学。这是促使你潜得更深的宽广的笔触或火花，也许不是...

这是一种复杂而快节奏的生活方式，但如果是为了你，你会从第一次`Hello World`就知道。

这篇文章严重偏向于 Javascript 和 Node.js，还有许多其他语言和服务可以用来构建你的架构。但是如果我要推动 web 开发，我会用我爱上它的方式去做。在推特上给我留言@埃克哈特德雷尔 T3】