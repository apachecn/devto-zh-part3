# 从 Visual Studio 代码运行 VueJS 应用程序的 3 种方式

> 原文：<https://dev.to/therealdanvega/3-ways-to-run-vuejs-applications-from-visual-studio-code-35nl>

在本教程中，我将向您展示从 Visual Studio 代码运行 VueJS 应用程序的 3 种方法。这也适用于任何有`package.json`的 NPM 项目，但是在本文中我将主要关注 Vue。我发现对 NPM 和 Vue 都不熟悉的开发人员在开始时有一些困难，所以希望这将有所帮助。

[![](img/c361befcbc35b0303fea61737557653c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--STWaiQK4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sqr3razqo0u79s0l1bx0.png)

## 节点，NPM & package.json

如果你是 Node & NPM 的新用户，好消息是，你不需要知道启动和运行的一切。每当你看到一个`package.json`就知道这个文件在那里帮助你管理你的项目。在这个文件中，您将找到关于项目的元数据、这个项目所依赖的包以及可以运行的脚本。这里是一个使用 [Vue CLI](https://cli.vuejs.org/) 创建的项目的简单`package.json`。

```
{
  "name": "hello-world",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint",
    "test:e2e": "vue-cli-service test:e2e",
    "test:unit": "vue-cli-service test:unit"
  },
  "dependencies": {
    "core-js": "^2.6.5",
    "vue": "^2.6.10"
  },
  "devDependencies": {
    "@vue/cli-plugin-babel": "^3.7.0",
    "@vue/cli-plugin-e2e-cypress": "^3.7.0",
    "@vue/cli-plugin-eslint": "^3.7.0",
    "@vue/cli-plugin-unit-mocha": "^3.7.0",
    "@vue/cli-service": "^3.7.0",
    "@vue/test-utils": "1.0.0-beta.29",
    "babel-eslint": "^10.0.1",
    "chai": "^4.1.2",
    "eslint": "^5.16.0",
    "eslint-plugin-vue": "^5.0.0",
    "vue-template-compiler": "^2.5.21"
  }
} 
```

## Visual Studio 代码

既然您已经简要介绍了`package.json`包含的信息，那么是时候学习如何从 Visual Studio 代码运行我们的 VueJS 应用程序了。

### 使用综合终端

从 Visual Studio 代码中运行 VueJS 应用程序的第一种方法您可能已经了解过，那就是使用集成终端。如果你打开一个新的终端/命令提示符来运行你的项目，这将加快速度，把一切都带回 Visual Studio 代码。

在 Visual Studio 代码中打开项目，点击键盘快捷键(cmd/ctrl) +反勾。反勾号位于键盘上 tab 键的正上方。这将打开集成的终端，从那里您可以为您的项目运行任何脚本。

### 可以运行哪些命令？

一般来说，对于刚接触 VueJS 和 NPM 的人来说，这常常是一个困惑点。我键入什么命令来启动我的应用程序？我键入什么命令来运行我的测试？当你在 Vue 中工作了一段时间后，这些将成为你的第二天性，但是有些情况下你可能会继承一个有自定义脚本的项目。

找出可用脚本的最简单方法是打开`package.json`并查看脚本块。

```
{
  "name": "hello-world",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint",
    "test:e2e": "vue-cli-service test:e2e",
    "test:unit": "vue-cli-service test:unit"
  },
} 
```

看到这里，我可以马上告诉你，我可以使用的 5 个脚本是:

*   服务
*   建设
*   线头
*   测试:e2e
*   测试:单位

因此，如果我想运行其中的任何一个，我只需键入`npm run serve`或您想要运行的脚本的名称。serve 脚本是在开发模式下启动应用程序的脚本。当您使用 Vue CLI 创建一个典型的 VueJS 项目并选择两个单元&进行端到端测试时，上面的脚本块就是这个项目的样子。

如果你在一个现有的项目上工作，有可能会有一堆自定义脚本。[在最近的一篇文章](https://www.danvega.dev/blog/2019/04/23/gridsome-blog-post-generator)中，我记录了我用来创建新的帖子生成器的过程，我可以运行该生成器来添加新的博客帖子。在这种情况下，我有一个`newpost`脚本要运行，我只需运行命令`npm run newpost`。

```
{
  "name": "danvega-dev",
  "private": true,
  "scripts": {
    "build": "gridsome build",
    "develop": "gridsome develop",
    "explore": "gridsome explore",
    "newpost": "node ./scripts/newpost.js"
  },
} 
```

### 任务:运行任务

这就是我们从命令行运行脚本的方式，但并不是每个人都喜欢在每次想要运行项目时输入命令。使用菜单项`View > Command Palette`或使用键盘快捷键`Shift + CMD + P`或`Shift + CTRL + P`打开命令面板。在那里输入`Tasks`并点击运行任务命令。

[![](img/5c7a76bf58adba63d48cb556a96b8bdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hd6Uj7wl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ehfvxtnu02is5zlksd2t.png)

这将检查您的项目，并为您提供可运行的脚本列表。

[![](img/89d2096682259b37db7c5d39a4954c3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8U9N1pMt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qdoves0fvjpqx88bhct5.png)

你可以点击`npm:serve`或者你可以开始输入单词 serve，并在选择不使用鼠标运行它时按回车键。如果你看到以下选项，你可以点击[了解更多关于扫描任务输出](https://code.visualstudio.com/docs/editor/tasks#vscode)的信息。

[![](img/543f89cf10eb0ddd90d4d69d6ef29cdc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4ElgqMAw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6bmmtrjc8mi1hu5ktvf3.png)

如果不扫描任务输出就点击继续，Visual Studio 代码将运行您的任务。这将为您打开一个终端并运行脚本。

[![](img/991668b993a7e7ee4eab5c8499ea9dce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OagluwRB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bl1j1mpxxpepyv69uftu.png)

*下一次运行任务时，您不会被要求扫描输出，这个过程变得非常快*。

### NPM 脚本资源管理器

如果你在推特上关注我，我会发布这条推特，承诺给你一个你可能不知道的提示。

> ![unknown tweet media content](img/e47b1064d53544d58df22d3c3ed2d2b0.png)![Dan Vega profile image](img/8a7b9c3e52eccb542ecf55e408a0676a.png)丹维加[@ therealdanvega](https://dev.to/therealdanvega)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)这些你可能一个都不知道。我们称之为预告片😉[# vuejs](https://twitter.com/hashtag/vuejs)[# NPM](https://twitter.com/hashtag/npm)[# vs code](https://twitter.com/hashtag/vscode)2019 年 5 月 15 日上午 11:48[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1128628100898619392)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1128628100898619392)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1128628100898619392)14

要启用此功能，请进入您的 Visual Studio 代码设置并添加以下设置

```
"npm.enableScriptExplorer": true, 
```

随着您的项目打开，您现在将在侧栏中有一个 NPM 脚本资源管理器。如果你点击脚本名称旁边的播放图标，它会为你运行脚本，多棒啊！

[![](img/5c2003eeb2095a7fa943566cd9ca5bb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hdMiSw6O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/93ssl3rqdwzvzofc0ehe.png)

## 结论

我希望这篇文章有助于从 Visual Studio 代码中识别和运行您的 VueJS 应用程序。完成一项任务通常有不止一种方法，而且并非所有人都有相同的偏好。一如既往的朋友...

快乐编码
丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*