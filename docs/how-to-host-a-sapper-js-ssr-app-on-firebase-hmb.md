# 如何在 Firebase 上托管一个 Sapper.js SSR app？

> 原文：<https://dev.to/eckhardtd/how-to-host-a-sapper-js-ssr-app-on-firebase-hmb>

我花了大约两天时间在网上搜索，试图找到将 Sapper 与 Firebase 集成的最佳方式。这并不像听起来那么简单。

[链接到示例网站](https://sapper-firebase.web.app)

## 什么是工兵连？

Sapper 是一个用于构建高性能 web 应用的框架。它实际上是一个编译器。您的所有代码都已构建并优化，可以投入生产。

它是基于 Svelte.js 构建的。Sapper 有许多很酷的功能，我建议你查看文档。该框架允许 SSR 功能，但也可以用`npm run export`生成一个静态站点。

## 什么是 Firebase

Firebase 是谷歌“快速构建应用，无需管理基础设施”的平台。基本上，只需几个命令，您就可以托管您的静态站点，连接到数据库，并随时进行身份验证。

除了静态网站，Firebase 还通过“功能”探索“无服务器”领域。这些基本上是小块的逻辑，你可以从你的应用程序中调用，或者在身份验证或数据库发生某种更新时调用。

函数适用于:

*   从客户端获取敏感逻辑
*   在将数据插入数据库之前，对数据执行一些操作(如消毒)。
*   **在我们的例子中，帮助我们的代码提供服务器端渲染应用**

## 服务器端的好处

*   SEO:爬虫可以更好地索引你预先生成的标记。
*   性能:你不需要提供臃肿的资产，你可以做一些服务器端缓存。

## 为什么要组合 Firebase x Sapper？

Firebase 开发真的很快很容易，Sapper 也是。我想:“为什么不两全其美呢？”。我可以拥有 Firebase 认证、数据库和 CDN 托管的所有优点——以及开发速度、应用程序大小和 Sapper 的总体魅力。

# 我们开始吧

首先，我们需要安装必要的依赖项。(我假设您的系统上已经安装了 Node.js 版本> = 10.15.3 以及兼容的 NPM 版本)。

## 安装工兵

Sapper 使用了一个叫做 degit 的工具，我们可以通过`npx`来使用它:

```
$ npx degit sveltejs/sapper-template#rollup sapper_firebase

$ cd sapper_firebase && npm install # install dependencies 
```

## 向项目添加 firebase

*   注意:为此您将需要 Firebase CLI 工具:`npm i -g firebase-tools@latest`

**下一步之前:**

你需要去 [Firebase](https://console.firebase.google.com) 并登录一个控制台。在这里，您必须创建一个新的 Firebase 项目。项目创建完成后，在主页上点击 **< / >** 按钮，添加一个新的 web-app。随便你怎么命名。

现在我们可以继续了。

```
# in /sapper_firebase
$ firebase login # make sure to login to your console account

$ firebase init functions hosting 
```

这将返回一个屏幕，您可以在其中选择新项目:

```
Select a default Firebase project for this directory: 
❯ sapper-firebase (sapper-firebase) # select your project 
```

遵循以下步骤:

```
 ? What do you want to use as your public directory? static
? Configure as a single-page app (rewrite all urls to /index.html)? N
? Choose your language: Javascript.
? Do you want to use ESLint to catch probable bugs and enforce style? N
? Do you want to install dependencies with npm now? Y 
```

然后让您的函数依赖项安装。

## 改变我们的 firebase.json

我们希望通过更改 firebase.json 实现的目标是:

1.  通过 Firebase 托管服务我们项目的静态文件。
2.  将每个请求重定向到我们将要创建的函数(我将对此进行详细解释)。

下面是更新后的 firebase.json:

```
{  "hosting":  {  "public":  "static",  "ignore":  [  "firebase.json",  "**/.*",  "**/node_modules/**"  ],  "rewrites":  [{  "source":  "**",  "function":  "ssr"  }]  }  } 
```

> 重写是检查任何路由下的请求，并基本上让名为“ssr”的函数来处理它。

编辑:恐怕还有更多..最近，如果 functions/package.json 中的`firebase-admin`依赖关系大于 7，就会弹出一个错误，所以只需将其设置为:

```
"firebase-admin": "^7.0.0" 
```

请记住:

`cd functions && npm install`

因此，让我们创建“ssr”函数:

`/sapper_firebase/functions/index.js`

```
const functions = require('firebase-functions');

exports.ssr = functions.https.onRequest((_req, res) => res.send('hello from function')); 
```

这只是暂时的，所以我们可以测试我们的功能。我们可以通过运行以下命令来实现:

```
$ firebase serve 
```

如果您收到此消息:

`⚠ Your requested "node" version "8" doesn't match your global version "10"`

您可以前往`functions/package.json`并设置:

```
"engines":  {  "node":  "10"  } 
```

除此之外，在运行`firebase serve`并转到 [http://localhost:5000](http://localhost:5000) 之后，您应该会看到这个:

[![](img/c9cfa35f38f3d522ff530209cf9ddce0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B03SUxfb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mack7ekvjl8y4vl7zakf.png)

## 测试功能

我们看到这个是因为 Firebase 在我们的`static` floder 中创建了一个`index.html`文件。我们可以安全地删除它，我们可以转到 [http://localhost:5000/](http://localhost:5000/) ，现在应该可以看到:

[![](img/b97d1bef3ea41880b665f085028724be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7VKeQbJd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/blvrq5jrqsggbarimnph.png)

## 万岁！

现在我们已经证明了我们的函数可以工作并重定向请求，我们需要对 Sapper 做一些改动，让我们的函数运行我们的服务器。让我们从编辑`src/server.js`文件开始，我们的目标是:

1.  我们希望导出构建时生成的 sapper 中间件。
2.  我们只想在`npm run dev`上运行本地服务器。

`src/server.js`

```
import sirv from 'sirv';
import polka from 'polka';
import compression from 'compression';
import * as sapper from '@sapper/server';

const { PORT, NODE_ENV } = process.env;
const dev = NODE_ENV === 'development';

if (dev) {
  polka() // You can also use Express
    .use(
    compression({ threshold: 0 }),
    sirv('static', { dev }),
    sapper.middleware()
    )
    .listen(PORT, err => {
    if (err) console.log('error', err);
    });
}

export { sapper }; 
```

我们可以通过让它编译并访问 [http://localhost:3000](http://localhost:3000) 来测试`npm run dev`是否工作。它应该显示波拉特(标准的萨帕幽默)的图片。

但这仍然只是我们的工蜂服务器，我们想从我们的 Firebase 功能运行它。首先，我们需要安装 express:

```
$ cd functions
$ npm install express sirv compression polka 
```

> 注意:“sirv”“compression”和“polka”是必需的，因为构建的目录依赖于它们。不过，我们将只在函数中使用 express。但是如果我们排除其他的，我们的功能将在部署时失败。

在安装 express 和其他依赖项之后，我们首先要调整我们的工作流，将项目的副本构建到函数中。我们可以通过编辑 npm 脚本来做到这一点:

> Windows 用户:请使用你的脚本后的指南

```
...  "scripts":  {  "dev":  "sapper dev",  "build":  "sapper build --legacy && cp -R ./__sapper__/build ./functions/__sapper__",  "prebuild":  "rm -rf functions/__sapper__/build && mkdir -p functions/__sapper__/build",  "export":  "sapper export --legacy",  "start":  "npm run build && firebase serve",  "predeploy":  "npm run build",  "deploy":  "firebase deploy",  "cy:run":  "cypress run",  "cy:open":  "cypress open",  "test":  "run-p --race dev cy:run"  },  ... 
```

这将把所有必要的文件复制到 functions 文件夹，然后在本地托管或提供这些文件。

尝试使用:

```
$ npm start # now using firebase 
```

您应该会看到之前的消息！

## 通过函数和 express 为我们的 Sapper 应用程序提供服务

我们可以在我们的功能中插入一个 express 应用程序，在 express 上使用我们进口的 Sapper 中间件，并无缝地提供我们的 SSR 应用程序。静态文件夹也通过非常快速的 Firebase CDN 提供服务。

`functions/index.js`

```
const functions = require('firebase-functions');
const express = require('express');

// We have to import the built version of the server middleware.
const { sapper } = require('./__sapper__/build/server/server');

const app = express().use(sapper.middleware());

exports.ssr = functions.https.onRequest(app); 
```

## 本地托管您的 Sapper Firebase SSR 应用程序

你现在要做的就是:

```
$ npm start 
```

**AAANND...**

# HOORAAAH

[![](img/f46a7b78872593ac1c518f1bd0440f6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BfsV2YUp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rfz8m42n5xiqad5k1bga.png)

如果你在 http://localhost:5000 上看到这张图片，说明你的应用是由一个本地 firebase 函数模拟器提供服务的！

要确认它是 SSR，只需重新加载一个页面并检查页面源代码，所有的标记都应该在初始加载时预呈现。同样检查你的终端，当你浏览你的应用程序时，你会看到各种各样的请求！

## 托管您的应用

由于我们简洁的 NPM 脚本，托管/部署非常简单:

```
$ npm run deploy 
```

这将需要一段时间来启动您的文件和功能。但是这是我的在线版本，像老板一样说话。

## Windows 用户

你将需要做一些额外的事情来让这个工作...

1.  添加一些 npm 包来帮助删除和复制内容:

`npm install -D rimraf mkdirp ncp`

D 标志将它添加到开发依赖项中，因为那是我们需要它的地方。将您的脚本更改为:

```
...
"build": "sapper build --legacy && ncp ./__sapper__/build ./functions/__sapper__/build",
"prebuild": "rimraf - functions/__sapper__/build && mkdirp functions/__sapper__/build",
... 
```

这些解决方案很粗糙，而且这个帖子可能会在未来咬我一口，因为工兵连和 firebase 的东西变化很快。感谢 Diogio Marques 在评论中提出这一要求。

## 谢谢

这是我的第一篇帖子，我真的觉得可以帮助别人，因为我花了几个小时才弄明白(也许我只是反应慢)。但是我把它打了出来，因为时间问题，我不能在我的 Youtube 频道上制作视频。

但是如果你喜欢这个，或者有问题，可以在 twitter 上和我聊天。下次见。