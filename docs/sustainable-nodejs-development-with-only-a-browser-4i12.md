# 仅使用浏览器的可持续 Node.js 开发

> 原文：<https://dev.to/akosyakov/sustainable-nodejs-development-with-only-a-browser-4i12>

现在，有许多展示 JS 代码片段和框架的协作平台。它们中的大多数运行在你的浏览器中，有些甚至设法神奇地在云中运行 Node.js 应用程序。他们抽象出许多可能性，以便专注于原型和学习。

这种简化(甚至是魔术)最终变成了一堵硬墙。一旦我们离开原型阶段，我们需要能够控制和自由组合不同版本的 frontend 和 Node.js 框架，连接到最适合我们用例的数据库，使用不同的 js 风格和部署服务。

[![](img/64c1f6bd11c798e322c5182600d08a4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JmWhU2Q0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7zkVCKjkjvyutzD5hFn_Tw.jpeg) 

<figcaption>照片由[克拉克扬](https://unsplash.com/photos/fQxMGkYXqFU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)于 [Unsplash](https://unsplash.com/search/photos/%22workshop%22?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

在这篇博客文章中，我将向您展示一种透明的方法，让您轻松地开始使用 Node.js，而无需使用魔法。这种方法提供了一个低门槛，一旦你成长起来，就没有了围墙。您将学习如何在基于浏览器的环境中编写 Node.js 应用程序，该环境具有完整的本地开发环境的所有深度和 VS 代码，但具有友好的 JSFiddle 平台。这些环境可以自由共享，以便一起工作并展示您的应用程序。

开发环境名为 [Gitpod](https://www.gitpod.io/) ，完全免费用于开源开发。

#### 目录

*   [你好世界](#hello-world)
*   [自动化 Node.js 设置](#auto-node-setup)
*   [处理拉取请求](#work-with-prs)
*   [一起工作](#work-together)
*   [开发全栈 Node.js 应用](#dev-full-stack)
*   [总结](#summary)

*注:这是来自 [Gitpod 博客](https://medium.com/gitpod)的交叉帖子。关注以获得更多类似这样的内容！*

### 你好世界

让我们从开发一个简单的“Hello World”应用程序开始，学习 Node.js 开发的基础知识。

#### 开始一个新项目

Gitpod 将任何 GitHub 存储库视为一个可以打开的项目。让我们首先为我们的 Hello World 项目创建一个新的存储库:

*   打开你的 GitHub 仪表盘，选择`New Repository`；
*   输入储存库名称并点击`Create repository`。

现在，我们可以在 Gitpod 中打开创建的项目。你可以通过简单地在存储库的 URL 前加上 **gitpod.io#** 来实现。例如，我已经将我的存储库节点命名为 hello-world，可以用`gitpod.io#https://github.com/akosyakov/node-hello-world`在 Gitpod 中打开它。

第一次，您需要使用您的 GitHub 帐户登录 Gitpod。GitHub 会要求您授权读取您的电子邮件地址。稍后可以在[您的访问控制页面](https://gitpod.io/access-control/)上授予对存储库的写访问权限。

[![](img/26e0cd57cadc872e3a2173b032a050f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rYcYX0OS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AK8cn3G30eQouKmWQb228LQ.png)

Gitpod 启动了一个新的工作空间，这是一个基于 Linux 的云容器，并通过 VS 代码驱动的在线 IDE 提供对它的访问。工作区按需启动，如果您不使用它们，它会自动关闭。它们会被不断复制，永远不会被删除，因此您可以随时从 Gitpod 仪表板重启工作区。

[![](img/4fc705c927d39b6f9d885f956e9a210a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FX_OQVBa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmXM-VDzzmpXCvlAVvQIjVw.gif)

*如果不想从头创建新项目，点击我的工作区的* [*快照链接*](https://gitpod.io/#snapshot/0d82ae08-c328-4cb5-96e5-7233d5de0c11) *试试 Hello World 应用。稍后我将介绍如何制作工作区快照。*

#### 编写 Node.js 应用程序

默认情况下，每个工作区都预先配置了标准工具，如 Node.js、npm、yarn 和 nvm。稍后我们将学习如何更改 Node.js 设置。现在，让我们编写代码并运行它:

*   打开命令面板(`F1`)，选择`New File`命令；
*   创建一个名为`app.js`的新文件，并输入以下内容:

```
const message = 'Hello World';

console.log(message, 'from Gitpod!'); 
```

[![](img/673d01ddae9ed4572274619e358ab94c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ue8AN6K5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ab-63v0Asfa09vfzG) 

<figcaption>当你输入的时候，你可以看到 Gitpod 验证了你的代码。它还为您提供了关于悬停的智能完成和文档。</figcaption>

#### 运行/调试 Node.js 应用程序

为了运行应用程序，在终端中键入`node app.js`。

[![](img/b90ff24b16bcbefdde1d34a3811b918a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ViH4Nl1x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/0%2AKzrRQ41oARG7XoQB)

当然，您的云工作区可以有多个终端。

接下来，让我们看看如何调试应用程序:

*   创建一个调试配置(`Debug` - > `Add Configuration`)，在打开的编辑器的内容助手中选择`Node.js: Launch Program`配置片段；
*   点击左边的槽，在`app.js`文件中添加一个断点；
*   打开调试视图(`View`->-T1)，
*   按绿色的`play`图标开始调试(或按`F5`)。

我们的程序应该运行，当断点命中时，您可以检查调用堆栈、变量，甚至使用调试控制台来计算表达式。

[![](img/7174039190cc579b2510b511c215efa9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zekoh5CC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AVHUqe81CKwdHq7uK)

#### 使用 Git

让我们看看如何通过 Git 分享我们的更改。打开`Git`视图，存放您的更改，输入提交消息并单击`commit`。你也可以通过点击改变的文件打开一个比较编辑器，并用`Git: Push...`命令将新的提交推送到 GitHub。

[![](img/9f3cd124215ff8303943251de75d9f2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fBmT144L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AFqWgZy08qIkj3qVp)

如果您还没有授予对您的存储库的写访问权限，Gitpod 将会要求您这样做。授予权限并尝试再次推送。

[![](img/fc08e362d08095b92e3bb97c8f140e5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XALB_yS5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbHchL7DVfmfdYWqjWsqRaw.png)

### 自动化 Node.js 设置

如上所述，默认情况下，每个工作区都预先配置了标准工具，如 Node.js 8.x。

让我们假设我们同意在我们的项目中使用 Node.js 10.x。通常，所有开发人员都必须更新他们的本地 Node.js 版本，并且经常根据使用的项目或分支在它们之间切换。

有了 Gitpod，就不一样了。您可以通过 Docker 映像为每个分支配置一个带有特定 Node.js 版本的工作区，而无需为任何开发人员更改本地设置。

初始化配置最简单的方法是使用 Gitpod 的 CLI。CLI 将引导您完成最重要的配置选项，并在您的存储库的根目录下创建一个`.gitpod.yml`文件。

在端子中键入`gp init`。

1.  对于第一个选项，选择`docker file`。它将创建一个位于`.gitpod.yml`旁边的`Dockerfile`。在这里，您可以添加开发所需的任何类型的工具和依赖项。当开始一个新的工作空间时，Gitpod 会选择 docker 文件，并基于它提供一个云容器。
2.  现在按回车键直到任务部分。类型`node app.js`。每当工作空间启动时，任务就会自动启动。您可以有多个任务并使用不同的生命周期。每个任务将在单独的终端中打开。

[![](img/9b3193b4368e7e9ae440ae5609cb524d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Hbdh6yh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AyVXQ8QGJEmCnNQ52)

创建的文件将在编辑器中自动打开。让我们更改配置，以便它在不同的终端中启动两个任务:

```
image:

  file: Dockerfile

tasks:

  - init: node --version

  - command: node app.js 
```

并将 Dockerfile 更改为使用 Node.js 10.x 作为基本映像:

```
FROM node:10 
```

为了试用我们的产品，我们需要将配置推回到 GitHub 并打开一个新的工作区。让我们通过一个 pull 请求来实现这一点，这样我们就可以在途中了解 GitHub 集成。

*这里是* [*另一个快照链接*](https://gitpod.io#snapshot/ea0d6704-a513-4c94-b8e7-fe72c3d8e5fb) *试试我们的配置不开拉请求。您可以在这里* *了解更多关于工作区配置* [*。*](https://docs.gitpod.io/40_Configuration.html)

### 处理拉取请求

Gitpod 提供了与 GitHub 特性的无缝集成，比如创建和查看拉请求。

#### 创建新的拉式请求

让我们从 Gitpod 打开一个新的拉请求:

*   使用`Git`视图提交您的更改(见上文)；
*   使用`Git: Checkout`命令或点击状态栏中的分支项目创建一个新分支。以某种方式命名，如`gitpodify`；
*   接下来打开右边的`Pull Request`视图。您还不能创建新的 pull 请求，因为首先新的分支要发布到 GitHub，点击`publish changes`；
*   之后，`Pull Request`视图将被更新，您可以打开一个新的拉取请求。

[![](img/cd73ff9e2eb7a00b16e9f0cbfb90acfc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WamM-xhh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/3082655/50008295-00f43280-ffb4-11e8-9e05-7d88db16200c.gif)

#### 审核拉取请求

正如我在开始时提到的，Gitpod 将每个 GitHub 库视为一个项目。实际上，它甚至更聪明，可以从任何 GitHub URL 中计算出应该打开哪个分支以及如何打开。

让我们转到 GitHub 上的 pull 请求页面，在它的 URL 前面加上 **gitpod.io#** ，就像我们最初做的那样。用新的工作区映像打开 PR 可能需要一些时间，因为 Gitpod 需要构建映像。但是只有在改变的时候才会重建。

[![](img/02d8ac8311eebb74e5ad2786d613174e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i1SbIt77--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/3082655/50009585-9e9d3100-ffb7-11e8-945f-c7ec20cd7505.png)

如您所见，Gitpod 为您的拉请求打开了一个分支。PR 文件的更改显示在左侧栏供查看。我们的 init 和 command 工作区任务是在终端中执行的，Node.js 现在是 10.x，打印`Hello World`。

在 Gitpod 中进行评审时，您可以添加注释、修改代码以解决注释问题、提出新的修改建议等。完成后，也合并来自 Gitpod 的 pull 请求。

[![](img/bbc00a90aefec9c2b302aa50651133e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cMMEOwYV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AKIZkXiyQv3aZzsjA)

### 齐心协力

由于 Gitpod 工作区运行在云中，我们可以像在 Google Docs 中共享文档一样共享它们。

#### 帮助他人进行 Node.js 开发

您可以与同事共享您的运行工作区，以便在完全相同的代码库、开发工具、运行终端和应用程序上进行协作。在一些棘手的配置、依赖性或代码问题上互相帮助尤其有用。

为了分享一个正在运行的工作区:点击你的账号头像，选择`Share Running Workspace`，启用分享，复制一个链接，将复制的链接分享给你的同事。

[![](img/cb71ddd569852def0a1a8c479ead1fe9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ZOczvkv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ayp7USviOYhUm_P40) 

<figcaption>两个用户使用完全相同的工作空间，并在终端中查看共享输出。</figcaption>

#### 展示 Node.js 应用

您可以拍摄工作区的完整快照(克隆)，甚至包括您的 IDE 布局，并将其作为链接共享。想想工作区快照，就像 JSFiddle 上的代码片段。它同样有用:分享关于问题的可重复的例子，堆栈溢出答案，用于教程和研讨会。但同时，您共享完整的开发设置，并以适当的方式呈现它。

为了分享一个工作区快照:点击你的账号头像，选择`Share Workspace Snapshot`，复制一个链接，把复制的链接分享到你想要的地方。

[![](img/65fc836dc174aa2e8f4a1801aa1708be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hMdCHey1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A-n2I6-4fPuKK70_5)

<figcaption>[共享工作区快照](https://gitpod.io#snapshot/14dc69f1-0e8c-4a36-b221-0ab16e91326e)不仅保存了开发设置，还保存了 IDE 布局，甚至是编辑器中的选择。</figcaption>

### 开发全栈 Node.js 应用

到目前为止，我们只看了一个简单的 Node.js 应用程序。如果您想知道它是否可以扩展到复杂的应用程序，您可以尝试以下任何一个存储库:

*   [Node.js 购物车](https://github.com/gitpod-io/nodejs-shopping-cart)是一个参考 Node.js 应用程序，演示了如何使用 Express 框架。你可以在 Gitpod 中用[一键](https://gitpod.io#https://github.com/gitpod-io/nodejs-shopping-cart)打开它，并跟随[这篇博文](https://dev.to/akosyakov/debugging-nodejs-applications-in-theia-c74-temp-slug-7735617)详细解释如何调试应用程序。
*   Gitpod 基于一个开源的 IDE 框架——[忒伊亚](http://theia-ide.org)。它是用很多 JS 技术构建的，包括 Node.js、Express、TypeScript 和 React。当然， [Gitpod 是用来开发忒伊亚](https://github.com/theia-ide/theia/blob/master/doc/Developing.md#run-the-browser-example-with-gitpod)的。
*   你甚至可以在 Gitpod 中开发电子应用程序，比如 VS 代码。克里斯在他的博客文章中展示了这一点。

### 总结

Gitpod 直接从浏览器提供完整的 Node.js 开发体验。它使您能够自动化您的开发设置，快速加入开发人员，简化 GitHub 上的代码审查和测试过程，实时相互帮助，展示您的应用程序和设置。而且，它对于开源开发是完全免费的！

如果您有任何问题，[请加入 Spectrum 上的 Gitpod 社区](https://spectrum.chat/gitpod)！我们正在认真听取所有反馈。

* * *