# Firebase 上的 Google 云功能入门

> 原文：<https://dev.to/bogdaaamn/getting-started-with-google-cloud-functions-on-firebase-3g29>

你知道他们说什么。在一个充满无服务器的世界里，部署...无服务器。所以，我当然会尽我的一份力。在一个只需要*静态页面的小项目中，最大的困难是找到一种从用户那里收集反馈的方法(通过静态表单)。我的第一个想法是构建一个小 API，从表单中获取数据，并将其存储在数据库中。但是网站上的流量并不是很大，所以我没有看到一个每周只为几个请求提供 24/7 服务的 API。*

我遇到的最流行的解决方案当然是无服务器的。有很多方法可以使用[AWSλ](https://medium.com/calyx/serverless-contact-forms-with-aws-lambda-79959cd1a6cd)或 [Netlify](https://medium.com/@marcmintel/how-to-submit-forms-on-a-static-website-with-vuejs-and-netlify-functions-b901f40f0627) 。但是我的静态页面已经部署在托管的 [Firebase 上，所以**我不得不**为 Firebase 尝试](https://firebase.google.com/docs/hosting/)[谷歌云功能](https://firebase.google.com/docs/functions/)。

# 优点

@adnanrahic 很好地解释了无服务器的利弊(**奖励**与容器的比较)。

[![adnanrahic image](img/9fadf8937ec23bd3ce1ac3c7266919d8.png)](/adnanrahic) [## 从 DevOps 的角度看容器与无服务器

### Adnan rahi 9 月 8 日 18 时 12 分阅读

#devops #serverless #kubernetes #aws](/adnanrahic/containers-vs-serverless-from-a-devops-standpoint-e4n)

对于这个特定的项目，无服务器架构是一个完美的匹配:易于编写、部署和维护。不需要关心基础设施，我可以用我最喜欢的语言，用我最喜欢的包来编写它们，我甚至可以在本地测试它们。方便。

# 入门

不需要设置或扩展服务器，我们只需编写功能并将其部署到 Firebase。它们只有在请求被调用时才会被触发。

此时此刻，谷歌云函数可以用 [Node.js](https://nodejs.org/) (v6 或 v8)、 [Python](https://python.org/) (beta)或 [Go](https://golang.org/) (beta)编写。我将进一步使用 Node.js 和一些额外的资源，如 [Express](https://www.npmjs.com/package/express) 和 [CORS](https://www.npmjs.com/package/cors) 。

## 1。安装 Node.js

确保在开始之前已经正确配置了 [Node.js](https://nodejs.org/) 和 [npm](https://www.npmjs.com/get-npm) ，因为我们将在 Node.js 中编写函数

有人会推荐你 [nvm](https://github.com/creationix/nvm) 安装和管理 Node.js 版本。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ nvm-sh ](https://github.com/nvm-sh) / [ nvm](https://github.com/nvm-sh/nvm)

### 节点版本管理器——符合 POSIX 的 bash 脚本，用于管理多个活动的 node.js 版本

<article class="markdown-body entry-content container-lg" itemprop="text">

# 节点版本管理器[![Build Status](img/2ca2f8df1b9e43dc08246a6c63addad8.png)](https://travis-ci.org/nvm-sh/nvm)[![nvm version](img/30a24401f025d62a0f1f289b70d505e4.png)](https://github.com/nvm-sh/nvm/releases/tag/v0.38.0)[![CII Best Practices](img/d185ce3a3c997cac7d75f1a7e9c67ba5.png)](https://bestpractices.coreinfrastructure.org/projects/684)

## 目录

*   [关于](https://raw.githubusercontent.com/nvm-sh/nvm/master/#about)
*   [安装和更新](https://raw.githubusercontent.com/nvm-sh/nvm/master/#installing-and-updating)
    *   [安装&更新脚本](https://raw.githubusercontent.com/nvm-sh/nvm/master/#install--update-script)
        *   [附加说明](https://raw.githubusercontent.com/nvm-sh/nvm/master/#additional-notes)
        *   [Linux 上的故障排除](https://raw.githubusercontent.com/nvm-sh/nvm/master/#troubleshooting-on-linux)
        *   [MAC OS 故障排除](https://raw.githubusercontent.com/nvm-sh/nvm/master/#troubleshooting-on-macos)
        *   [可回答的](https://raw.githubusercontent.com/nvm-sh/nvm/master/#ansible)
    *   [验证安装](https://raw.githubusercontent.com/nvm-sh/nvm/master/#verify-installation)
    *   [重要提示](https://raw.githubusercontent.com/nvm-sh/nvm/master/#important-notes)
    *   [Git 安装](https://raw.githubusercontent.com/nvm-sh/nvm/master/#git-install)
    *   [手动安装](https://raw.githubusercontent.com/nvm-sh/nvm/master/#manual-install)
    *   [手动升级](https://raw.githubusercontent.com/nvm-sh/nvm/master/#manual-upgrade)
*   [用途](https://raw.githubusercontent.com/nvm-sh/nvm/master/#usage)
    *   [长期支持](https://raw.githubusercontent.com/nvm-sh/nvm/master/#long-term-support)
    *   [安装](https://raw.githubusercontent.com/nvm-sh/nvm/master/#migrating-global-packages-while-installing)时迁移全局包
    *   [安装](https://raw.githubusercontent.com/nvm-sh/nvm/master/#default-global-packages-from-file-while-installing)时文件中的默认全局包
    *   [io.js](https://raw.githubusercontent.com/nvm-sh/nvm/master/#iojs)
    *   [节点的系统版本](https://raw.githubusercontent.com/nvm-sh/nvm/master/#system-version-of-node)
    *   [列表版本](https://raw.githubusercontent.com/nvm-sh/nvm/master/#listing-versions)
    *   [设置自定义颜色](https://raw.githubusercontent.com/nvm-sh/nvm/master/#setting-custom-colors)
        *   [持续自定义颜色](https://raw.githubusercontent.com/nvm-sh/nvm/master/#persisting-custom-colors)
        *   [抑制彩色输出](https://raw.githubusercontent.com/nvm-sh/nvm/master/#suppressing-colorized-output)
        *   [恢复路径](https://raw.githubusercontent.com/nvm-sh/nvm/master/#restoring-path)
        *   [设置默认节点版本](https://raw.githubusercontent.com/nvm-sh/nvm/master/#set-default-node-version)
        *   [使用二进制节点的镜像](https://raw.githubusercontent.com/nvm-sh/nvm/master/#use-a-mirror-of-node-binaries)
    *   [。nvmrc〔t1〕](https://raw.githubusercontent.com/nvm-sh/nvm/master/#nvmrc)
    *   [更深层次的外壳整合](https://raw.githubusercontent.com/nvm-sh/nvm/master/#deeper-shell-integration)
        *   [痛击](https://raw.githubusercontent.com/nvm-sh/nvm/master/#bash)
            *   [自动调用`nvm use`](https://raw.githubusercontent.com/nvm-sh/nvm/master/#automatically-call-nvm-use)
        *   [zsh](https://raw.githubusercontent.com/nvm-sh/nvm/master/#zsh)
            *   [在带有`.nvmrc`文件](https://raw.githubusercontent.com/nvm-sh/nvm/master/#calling-nvm-use-automatically-in-a-directory-with-a-nvmrc-file)的目录下自动调用`nvm use`
        *   [鱼](https://raw.githubusercontent.com/nvm-sh/nvm/master/#fish)
            *   [在带有`.nvmrc`文件](https://raw.githubusercontent.com/nvm-sh/nvm/master/#calling-nvm-use-automatically-in-a-directory-with-a-nvmrc-file-1)的目录下自动调用`nvm use`
*   [执照](https://raw.githubusercontent.com/nvm-sh/nvm/master/#license)
*   [运行测试](https://raw.githubusercontent.com/nvm-sh/nvm/master/#running-tests)
*   [环境变量](https://raw.githubusercontent.com/nvm-sh/nvm/master/#environment-variables)
*   [Bash 完成](https://raw.githubusercontent.com/nvm-sh/nvm/master/#bash-completion)
    *   [用途](https://raw.githubusercontent.com/nvm-sh/nvm/master/#usage-1)
*   [兼容性问题](https://raw.githubusercontent.com/nvm-sh/nvm/master/#compatibility-issues)
*   [在 Alpine Linux 上安装 nvm](https://raw.githubusercontent.com/nvm-sh/nvm/master/#installing-nvm-on-alpine-linux)
*   [卸载/移除](https://raw.githubusercontent.com/nvm-sh/nvm/master/#uninstalling--removal)
    *   [手动卸载](https://raw.githubusercontent.com/nvm-sh/nvm/master/#manual-uninstall)
*   [开发环境的 Docker](https://raw.githubusercontent.com/nvm-sh/nvm/master/#docker-for-development-environment)
*   [问题](https://raw.githubusercontent.com/nvm-sh/nvm/master/#problems)
*   [macOS 故障排除](https://raw.githubusercontent.com/nvm-sh/nvm/master/#macos-troubleshooting)

## 关于

nvm 是一个用于 [node.js](https://nodejs.org/en/) 的版本管理器，设计用于按用户安装…

</article>

[View on GitHub](https://github.com/nvm-sh/nvm)

但是如果你也能使用[图形指令](https://nodejs.org/en/download/)的话。

## 2。配置 Firebase

注册或登录到 [Firebase 控制台](https://console.firebase.google.com/)并创建一个新项目。没关系，但我把我的叫做`dev-form-entries`。

现在，在本地设置您的项目。

首先，全局安装 [Firebase CLI](https://github.com/firebase/firebase-tools) 。

```
npm install -g firebase-tools 
```

Enter fullscreen mode Exit fullscreen mode

现在为您的项目创建一个本地文件夹。

```
mkdir dev-form-entries
cd dev-form-entries 
```

Enter fullscreen mode Exit fullscreen mode

在项目文件夹中，登录 Firebase。

```
$ firebase login
Success! Logged in as me@tld.com 
```

Enter fullscreen mode Exit fullscreen mode

让我们初始化我们的第一个 Firebase 项目(您实际上可以运行`firebase init`并在以后添加功能)。

```
firebase init functions 
```

Enter fullscreen mode Exit fullscreen mode

[![Your first Firebase project!!!](img/5c7a742d12410797c20e37f55a39c2b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--21FbeaAr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zvioyw1vwr07712950sn.png)

*   **为此目录选择一个默认的 Firebase 项目:** `dev-form-entries`

*   **您想使用哪种语言？** `JavaScript`
    我们现在就用 Javascript。Typescript 也可以。

*   **要不要用 ESLint 来捕捉可能的 bug？** `No`
    整齐的选项，但是现在不需要。

*   **您想现在安装与 npm 的依赖关系吗？** `Yes`
    运行那个`npm install`给你安装`firebase-functions`和`firebase-admin`。

好吧，让我们看看我们有什么

*   `firebase.json`用于配置 [Firebase 托管](https://firebase.google.com/docs/hosting/full-config)，
*   `.firebaserc`配置多个项目，
*   `functions/index.js`是 Firebase 提供的样板文件。我们将很快回到这个话题。

## 3。配置实时数据库

这里不需要太多的配置，因为它将以编程方式初始化。但我想在为时已晚之前提及它们。

正如我之前提到的，我希望将所有数据存储在一个数据库中。Firebase 有两个很棒的现成数据库可供你使用，分别是[实时数据库](https://firebase.google.com/docs/database/)和[云 Firestore](https://firebase.google.com/docs/firestore/) 。它们都具有高度的可伸缩性和灵活性(我稍后会谈到这一点)，但我选择使用实时数据库，因为它不需要任何类型的预配置，我们将从代码中引用它。

@aurelkurtula 可能会让你一瞥实时数据库的伟大之处。

[![aurelkurtula image](img/e1a1b48b6a401b86d5a91c8729e26a35.png)](/aurelkurtula) [## firebase 实时数据库介绍

### aurel Kurt ula 12 月 9 日 175 分钟阅读

#firebase #javascript #database](/aurelkurtula/introduction-to-firebases-real-time-database-89l)

# 部署到消防基地

## 你好来自火垒

先说 Firebase 的 hello world。编辑`functions/index.js`并保留他们的例子。

```
const functions = require('firebase-functions');

// Create and Deploy Your First Cloud Functions
// https://firebase.google.com/docs/functions/write-firebase-functions

exports.helloWorld = functions.https.onRequest((request, response) => {
    response.send("Hello from Firebase!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

该函数将创建一个路由`/helloWorld`，并对每个请求用`Hello from Firebase!`进行响应。

## 部署一下

现在，你的第一次部署。

```
firebase deploy --only functions 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以只运行`firebase deploy`,因为这个项目目前只包含一个函数。

```
=== Deploying to 'dev-form-entries'...

i  deploying functions
i  functions: ensuring necessary APIs are enabled...
✔  functions: all necessary APIs are enabled
i  functions: preparing functions directory for uploading...
i  functions: packaged functions (42.53 KB) for uploading
✔  functions: functions folder uploaded successfully
i  functions: updating Node.js 6 function helloWorld(us-central1)...
✔  functions[helloWorld(us-central1)]: Successful update operation. 

✔  Deploy complete! 
```

Enter fullscreen mode Exit fullscreen mode

现在您的部署已经完成，您可以转到您的 [Firebase 控制台](https://console.firebase.google.com/)并找到您的功能。

[![My Google Cloud Functions in the Firebase Console](img/aff38d44260bb79a9a6250dfbc9cfece.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QYTEkbsm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a7vr8pnkoj89eqhb6q8u.png)

那是一个整洁的仪表板。您可以检查健康状况并读取您的函数的日志。你可以被重定向到谷歌云平台，以查看完整的细节和配额。

## 测试一下

我将使用 [Postman](https://www.getpostman.com/) 来测试功能。Postman 是测试你的 API 的一个很好的工具，我今天将只涉及超级基础，但是你可以查看[@ harshitrhode](https://dev.to/harshitrathod)的初学者指南，或者通过[用](https://dev.to/ebanx/going-beyond-with-postman-522f) [@jlozovei](https://dev.to/jlozovei) 超越 Postman 来深入了解它。

[![harshitrathod image](img/258353d805fab1e2efd679224d6a0886.png)](/harshitrathod) [## Postman REST 客户端介绍

### harsh it rathod 11 月 13 日 173 分钟阅读

#tutorial #beginners](/harshitrathod/introduction-to-postman-rest-client-748)

如仪表盘所示，我的功能路线是`https://us-central1-dev-form-entries.cloudfunctions.net/helloWorld`。我将它粘贴到 Postman 中，并发出一个`GET`请求。

[![Successful response got in Postman](img/3aa57d6e69b0443c6b8e679b97689064.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OCCWeAS1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m69zer7hr0rjwhydt112.png)

# 编写 API

好了，现在你知道在哪里编写、部署和测试代码了。让我们试着做一些真实的事情。

## 热身

### 快车和 CORS

作为我们伟大目标的小助手，我们将使用 [Express](https://www.npmjs.com/package/express) (用于编写中间件和更好的路由)和 [CORS](https://www.npmjs.com/package/cors) (用于启用所有 CORS 请求，如果你不熟悉，可以看看 [@effingkay](https://dev.to/effingkay) 的一些 CORS 概念)。

[![effingkay image](img/ad84140edb62d1494aed9f9bd2fb2f87.png)](/effingkay) [## CORS，预先请求和选项方法

### 克劳迪亚 7 月 20 日 183 分钟阅读

#learning #beginners #webdev #showdev](/effingkay/cors-preflighted-requests--options-method-3024)

首先，你将需要安装它们，所以进入你的终端

```
npm install --save express cors 
```

Enter fullscreen mode Exit fullscreen mode

并将它们添加到您的`index.js`文件的顶部。

```
const express = require('express');
const cors = require('cors'); 
```

Enter fullscreen mode Exit fullscreen mode

之后，创建一个 Express 实例，并编写接受所有 CORS 请求的中间件。

```
const app = express();
app.use(cors()); 
```

Enter fullscreen mode Exit fullscreen mode

您将使用`app`实例来编写路线，并将它导出为 Google Cloud 函数，就像您对`helloWorld`实例所做的那样。所以在`helloWorld`出口之后写一个新的。

```
exports.entries = functions.https.onRequest(app); 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个`/entries`函数。我们将为`app`实例编写的所有路线都将在`entries`函数中可用。

### 实时数据库

为了使用实时数据库，您需要导入并初始化它。

```
const admin = require('firebase-admin');
admin.initializeApp(); 
```

Enter fullscreen mode Exit fullscreen mode

## 发布条目

我通常会从`GET`路线开始，但是我们在得到它们之前需要这些条目。所以您将编写`POST`路由来将数据推送到数据库。

快速`POST /`路线的一个基本例子是

```
app.post('/', (request, response) {
  // send stuff...
}); 
```

Enter fullscreen mode Exit fullscreen mode

实时数据库的有趣之处在于它非常灵活，所以你不需要预先设计一个完整的结构。因为它将数据存储为一个 JSON 树，所以我们可以推送一个 JSON 结构，这就足够了。当然，如果所有的字段都被推送到数据库中，就需要进行验证，但这是另一个很好的话题。

因此，存储在数据库中的条目将是请求本身的主体。

```
const entry = request.body; 
```

Enter fullscreen mode Exit fullscreen mode

数据推送到数据库的方式是

```
return admin.database().ref('/entries').push(entry); 
```

Enter fullscreen mode Exit fullscreen mode

`/entries`是数据库引用的路径。

`push`函数返回一个承诺，我们将用它来验证和发送响应。完成后，我们将返回推送的条目和`200`状态代码。否则，捕捉并发送错误作为一个`Internal Server Error`。

```
return admin.database().ref('/entries').push(entry)
    .then(() => {
        return response.status(200).send(entry)
    }).catch(error => {
        console.error(error);
        return response.status(500).send('Oh no! Error: ' + error);
    }); 
```

Enter fullscreen mode Exit fullscreen mode

最核心的，就是这样！