# 用 Node.js、Vue.js、Cloudinary 和 MySQL 构建文件共享应用程序——第 2 部分

> 原文：<https://dev.to/polymathwhiz/building-a-file-sharing-app-with-nodejs-vuejs-cloudinary-and-mysql---part-2-32nl>

在本教程中，我们将创建一个应用程序，让个人上传图像。上传后，系统生成一个唯一的可下载链接，3 小时后自动消失。

# 入门

我们将使用 [NPM](https://www.npmjs.com) 来设置我们的 Node.js API，然后安装一些包，这些包将使我们能够完成一些任务，而不必重新构建轮子。

## 项目结构

让我们设置我们的节点应用程序
[![alt text](img/a97f8f6191034fd692c746d017e42b3b.png "Generating our node app")](https://res.cloudinary.com/practicaldev/image/fetch/s--o9bXSMDu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kmfe1l0qf8dawgu0nruo.png)

## 应用文件夹

这就是我们的应用程序文件夹将如何像
[![alt text](img/32643fd9539dbf090ed3ff38762094b0.png "Current folder structure")](https://res.cloudinary.com/practicaldev/image/fetch/s--yQNhwtXz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vt8d5osms0qtrs1q2ded.png)

## 应用包

此时，您应该已经创建了项目文件夹来存放所有的源代码。这个应用程序依赖于几个包，我们将使用 npm 来安装它们。导航到您刚刚创建的项目目录，您应该已经看到`npm init`命令自动创建了一个 package.json 文件，其内容如下。

[![alt text](img/b55a5b37345a866ed2bd2eb7714a8958.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DfkJC8sK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wupkchdlf4pp442el0hm.png)

## 安装附加依赖项

我们需要安装以下依赖项，以使我们的工作易于实现

1.  `dotenv` -从加载环境变量。nodejs 项目的 env。

2.  Express 是一个最小且灵活的 Node.js web 应用程序框架，为 web 和移动应用程序提供了一组强大的功能。

3.  Cloudinary 是一种云服务，为 web 应用程序的整个图像管理管道提供解决方案。

4.  `cors` - CORS 是一个 node.js 包，用于提供一个连接/快速中间件，该中间件可用于支持具有各种选项的 CORS。

5.  `multer` - Multer 是一个 node.js 中间件，用于处理 multipart/form-data，主要用于上传文件。为了最大效率，它被写在勤杂工的上面。

6.  `mysql2` -快速 node-mysql 兼容的 mysql 驱动程序，用于 node.js

7.  监控 node.js 应用程序中的任何变化，并自动重启服务器——非常适合开发

8.  `request` -简化的 HTTP 请求客户端。

现在让我们运行`npm install`命令，简称为`npm i`

[![alt text](img/6ab88f6b69b507076b654061ca9eb18e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YJ-391UZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0d5yml47b3md0rv1wq7n.png)

## 结论

在本文中，我们经历了生成节点应用程序和下载应用程序中所需的必要依赖项的过程。在接下来的系列中，我们将充分利用下载的依赖项，并编写我们的自定义函数。我们还将设计我们的数据库来处理我们的数据。

下一篇文章再见！