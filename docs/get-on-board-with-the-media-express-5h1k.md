# 搭乘媒体快车

> 原文：<https://dev.to/musebe/get-on-board-with-the-media-express-5h1k>

[![Alt Image Uploads $ Retrievals](img/385010e796f430b5f99dae513fc0179f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6EfKJ9d0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k18xm2azna0k21llj24g.png)

在当今的网络世界，你的电子商务网站和移动应用的交付速度是用户体验中最重要的因素。如果你的客户对你的网站漫长的加载时间感到沮丧，他们可能会停止访问它，使你的在线存在处于危险之中。事实是，人们在亲自联系你之前，通过他们与你的网络和移动应用的互动程度来判断你的品牌。

对于 web 开发人员来说，管理数字资产，如视频、照片、音乐和其他多媒体，是必须要做的事情。这个任务处理不好可能会导致你的应用程序运行缓慢，导致用户体验不佳。

别担心， [Cloudinary](https://cloudinary.com/) 是来救援的。

Cloudinary 是一个基于云的平台，用于管理图像和视频，使企业和开发人员能够专注于他们的专业。事实上，数字资产的管理是 Cloudinary 的核心业务。

本文将引导您完成开发一个简单的基于 web 的应用程序的过程，该应用程序可以从 Cloudinary 服务器上创建、读取和删除媒体。

## 安装 NPM

首先，从 [`https://nodejs.org`](%7B%%20raw%20%%7D%60https://nodejs.org%60%7B%%20endraw%20%%7D) 下载 Node.js 二进制文件的最稳定版本。然后，按照适用于您的平台的页面上的过程，将它们安装到您的计算机上。

要确认安装，请键入以下两个命令:

`node -v`
T1】

输出显示 Node.js 和节点包管理器(NPM)的版本号，后者是一个用于安装、卸载和维护应用程序包模块的工具。

## 初始化新应用

一个基本的节点应用程序包含一个`.js`文件和一个`package.json`文件，它们执行以下操作:

*   指定项目所依赖的包，
*   根据 [`semantic versioning`](%7B%%20raw%20%%7D%60https://docs.npmjs.com/about-semantic-versioning%60%7B%%20endraw%20%%7D) 规则，列出您的项目可以使用的包的版本。
*   使您的构建可重复，因此更容易与其他开发人员共享。

要为您的应用程序生成一个`package.json`文件，请在终端中导航到您的项目文件夹，或者对于 Windows 用户，在 Git Bash 中导航到您的项目文件夹，并键入以下命令:

`npm init -y`

运行上面不带标志`-y`的命令会产生问题，提示您回答，这将清楚地描述应用程序及其依赖关系。

## 安装所需的依赖项

运行此命令安装所需的依赖项:
`npm install express body-parser multer express-handlebars moment dotenv cloudinary --save`

以下是依赖关系:

*   **`express`**:node . js 的 web-app 框架
*   **`body-parser`** :处理`express`中 post 请求的中间件。
*   **`multer`** :处理多方`FormData`的 Node.js 中间件。
*   **`express-handlebars`** :车把视图——发动机为`express`。
*   **`moment`** :一个轻量级的 JavaScript 数据库，用于解析、验证、操作和格式化日期。

*   **`dotenv`** :将环境变量从`.env`文件加载到`process.env`属性的模块。

*   **`cloudinary`**:node . js 包装器，使您能够与 Cloudinary 服务器通信。
    一旦依赖包就位，编辑`package.json`文件如下所示:

[![Alt package.json](img/dabaed31089ce1b031809b2e8e83823e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lA0ruFW1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j8lfpp11wvvyh7b3dl26.png)

正如文件中所指定的，应用程序的入口点是`server.js`文件。在那里，您将记录应用程序所依赖的大部分逻辑。

## 启动服务器

接下来，通过将以下代码添加到`server.js`文件来启动服务器:

`const express = require("express");
const app = express();
app.get("/", (req, res) => res.send("Hello Cloudy"));
const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
console.log(`运行在${PORT}上的服务器`);
});`

之后，通过运行这个命令启动你的应用程序:
`node server.js`

现在转到浏览器上的`localhost:5000`来验证字符串`Hello Cloudy`是否已显示。

## 构建项目结构

`express-handlebars`是你默认的前端模板引擎。要使用它，请执行以下操作:

1.  通过将以下代码添加到您的`server.js`文件中来注册模板引擎的包:

`const exphbs = require('express-handlebars');
app.engine('handlebars', exphbs({defaultLayout: 'main'}));
app.set('view engine', 'handlebars');`

2.将所有前端文件封装在一个名为`views`的文件夹中。

1.  在`views`文件夹中，创建另一个文件夹并命名为`layouts`，其中存放着`main.handlebars`文件，它包含了你的应用程序的前端结构。

你的 app 的项目结构现在看起来是这样的:
[![Alt file_structure](img/150a68238990f7c5b8918b39f176af26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GDxl4eeQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8pdm8kkg6bht1fqx6uw0.png)

## 向`main.js`和`index.handlebars`文件添加内容

将以下代码添加到`main.js`文件中: