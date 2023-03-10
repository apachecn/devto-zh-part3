# 用 Express.js 和 Firebase 免费创建自己的模拟 API 服务器！

> 原文：<https://dev.to/hossain/create-your-own-mock-api-server-with-express-js-and-firebase-for-free-bol>

最近我不得不为我过去工作的一个应用程序准备一个完整的模拟服务器。需要模仿的原因是，应用程序的不同功能可以通过触发不同 UI 和功能的模仿响应的变化来展示。

然而，市场上可能有不同的解决方案，因为 Firebase 有一个免费层，允许基于 Node.js 的 [Express](https://expressjs.com/) web 框架更容易根据需要进行定制。

Google 有很多关于云函数的例子，展示了上百个用例，然而，我只是想把重点放在使用 Express 来模拟 API 响应上。

### 本指南将涉及哪些内容？

*   如何创建 Firebase 项目
*   如何在 Firebase 项目上部署
*   为模拟配置 Firebase 项目
*   如何添加模仿您想要的新 API
*   如何使用火焰风暴进行模拟回应

### 设置&部署火力基地

首先，进入[https://console.firebase.google.com/](https://console.firebase.google.com/)，点击“添加项目”并命名，完成项目创建步骤。

[![](img/14e70a3b58b91486e2cb43973fc62ddb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s7IsXSLA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcNtjrmCpEk-WIaT12O7IqA.png) 

<figcaption>创建项目。注意:项目 ID 将在模拟中使用的 URL 中可见。</figcaption>

如果您没有安装“Firebase CLI ”,请按照[https://firebase.google.com/docs/cli/](https://firebase.google.com/docs/cli/)中的步骤安装 Firebase CLI。这将允许您更新代码以将模拟服务器部署到 Firebase。

在终端中运行`firebase list`命令，验证您刚刚创建的项目是否出现在列表中。

[![](img/af12050cb6e0dae698bd4b858b27f6c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6t9vpz7B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AtLq8Nb67WZaB8S4naHK4mQ.png)

现在，转到一个新目录，通过运行以下命令初始化一个新的 firebase 项目:

```
firebase init 
```

[![](img/cc967fe1776ca7f50421f8028efff215.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EWCnsyoW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXy89RPx9KAuBap0CXkypVg.png) 

<figcaption>上下选择“功能”和“主机”，按空格键打开选项。然后按回车。注意“托管”不是必需的，但是，如果你想托管模拟图像或视频，你将需要托管。</figcaption>

使用 firebase 初始化向导的默认选项。我们稍后将更新一些配置。初始化完成后，使用刚刚创建的项目 ID 执行以下命令，将新创建的 firebase 项目与当前目录项目链接起来:

```
// “ **your-project-id-23d6x** ” is the ID from “firebase list” command.

firebase use --add your-project-id-23d6x 
```

现在，尝试部署准系统项目，通过运行`firebase deploy`来查看一切正常。项目部署后，您将看到一个公共 URL，使用它来验证 Firebase 的默认 index.html 内容是否已显示。

### 用明示来嘲讽

既然默认项目已经启动并运行，那么是时候设置 Express 了，这样我们就可以定义模拟我们想要的 API。

首先，update **重写了`/firebase.json`的**规则，将所有请求转发给 Firebase Cloud 函数。请参考 [github-sample](https://github.com/amardeshbd/firebase-mock-api-server/blob/master/firebase.json#L17) 。

现在，更新/functions/package.json 文件，在“dependencies”下包含 express。请参考 [github-sample](https://github.com/amardeshbd/firebase-mock-api-server/blob/master/functions/package.json#L14) 。

完成后，您需要更新 npm 模块，以便可以使用 Express。使用以下命令来更新它。

```
cd functions
npm install
cd .. 
```

现在，您已经准备好在`/functions/index.js`中使用 express。打开`index.js`并删除任何先前的内容，并应用以下内容: