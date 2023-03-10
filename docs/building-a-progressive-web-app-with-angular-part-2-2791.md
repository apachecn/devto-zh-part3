# 用 Angular 构建渐进式 Web 应用程序—第 2 部分

> 原文：<https://dev.to/enniob/building-a-progressive-web-app-with-angular-part-2-2791>

这是我用 Angular 构建渐进式 Web 应用程序教程的第 2 部分。在本教程中，我将解释如何在 Firebase 主机上托管您的 web 应用程序，以及如何在您的 web 应用程序有更新时通知用户。

如果您没有 Firebase 帐户，您需要创建一个。要做到这一点，请访问 https://firebase.google.com/；点击登录并按照提示进行操作。

如果你已经有一个帐户，点击**进入控制台**，它会带你到**欢迎使用 Firebase！**屏幕。在这里，您可以创建一个新项目或使用现有项目。

[![](img/25a5bae6c369e5102dfe65360c432407.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z-SPNNr5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wgbhrx9z4mvv05bxt0k4.png)

打开命令行，键入以下命令。

`npm install -g firebase-tools`

一旦完成，你应该能够使用`firebase`命令。要将本地计算机连接到 Firebase 帐户，请运行以下命令:

`firebase login`

你应该在命令提示符下得到一些问题，你将被重定向到一个谷歌认证页面，在那里你将需要登录并给予访问。

现在让我们初始化我们的 Firebase 项目。

`firebase init`

你会得到一系列问题。出于本教程的目的，我们只对托管选项感兴趣，所以请确保您选择了:**()托管:配置和部署 Firebase 托管站点**并按下**键进入**。

接下来将询问您是要选择一个现有项目还是创建一个新项目。选择一个适合你的。对于本教程，我选择了**创建一个新项目**选项。

在**主机设置**部分，您需要指定输出目录。因为我们使用的是 Angular CLI，所以一旦构建了项目，输出文件将位于 dist 文件夹中。确保为该选项选择**距离**。

[![](img/f29d5963c5e9cbfbdf656d6694d1a1e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k-y13eEl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jfc3cosxfqv6l7gpilws.png)

对于单页应用程序，选择**是**即可。您的项目现在设置为使用 Firebase 托管。

## 让我们做一些编码

打开 VS 代码或者你喜欢的编辑器。

首先让我们更改我们的构建目录，打开`angular.json`文件并将输出路径更改为:`"outputPath": "dist"`Angular CLI 默认为`dist/[PROJECTNAME]`，因为我们告诉 Firebase CLI 我们的项目将只在`dist`文件夹中，所以我们需要对我们的`angular.json`文件做一个小的更改。

[![](img/0f8e9b473087a50e3fc81935733a1e3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A-dcrGxz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gf5zuwi7eziuyvebwzx6.png)
[![](img/5036e7299a3153d25e7ff07891f31bf4.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--8EYL0oDY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nqb9r3yg8lnjwlm2mkuc.png)

现在，让我们构建我们的应用程序并上传到 Firebase。打开命令行，键入以下命令:

`ng build --prod`

一旦申请完成，打开 **dist** 文件夹你会看到你的申请文件。要将应用程序上传到 Firebase，我们需要运行以下命令:

`firebase deploy`

部署应该需要几秒钟，您应该能够看到命令行上发生了什么。一旦完成，你会得到一个托管 URL，如果你在浏览器中打开这个 URL，你会看到默认的 Angular 应用程序。

## 让我们对我们的应用程序进行一些更改

我们将做一些 HTML 的改变，这样我们就可以显示一个欢迎信息，并通知用户我们已经改变了网站，所以他们知道要重新加载来查看我们网站的最新版本。我们将使用角状材料来显示变更信息，所以让我们安装角状材料。

`ng add @angular/material`

一旦安装完成，我们需要将 MatSnackBarModule 添加到我们的`app.modules.ts`文件中。