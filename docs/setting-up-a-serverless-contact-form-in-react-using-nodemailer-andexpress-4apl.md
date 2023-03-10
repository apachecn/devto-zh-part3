# 在 React 中设置无服务器联系人表单——使用 Nodemailer 和 Express

> 原文：<https://dev.to/gmrsagar/setting-up-a-serverless-contact-form-in-react-using-nodemailer-andexpress-4apl>

有一个联系方式总比在我们的网站上只显示一个电子邮件地址要好。联系表格给我们的访问者提供了一个与我们联系的简单方法。在这篇文章中，我们将通过一个简单的方法来使用[节点邮件](https://nodemailer.com)和 [Express](https://expressjs.com) API 建立我们自己的 in [React](https://reactjs.org) 。这篇文章也将带领我们通过 [Zeit](https://zeit.co/now) 部署它。

## 这个 app 会做什么

本教程结束时，您的网站上将会有一个联系表单，它会将邮件直接发送到您的收件箱。

<figure>

[![Gif from Giphy](img/55ced88578f9732acc10625e7407d963.png)](https://i.giphy.com/media/uWWmad9OgSckniKnEd/giphy.gif)

<figcaption>GIF from [Giphy](https://giphy.com/)</figcaption>

</figure>

## 此应用将使用的工具

*   [GitHub](https://github.com)——(用于托管代码，稍后也需要与 Zeit 一起部署)
*   Npm——(用于使用像 create-react-app 这样的 JS 包)
*   [Node JS](https://nodejs.org) 和 [Express JS](https://expressjs.com) (因为我们的 API 将内置在 Express 中)
*   React JS (create-react-app 用于引导标准 React 应用程序并设置我们的表单)
*   [Axios](https://github.com/axios/axios) (用于向我们的远程 API 提交表单数据)
*   [Zeit Now](https://zeit.co/now) (无服务器部署我们的应用程序)

## 步骤

## 1。准备好东西

**GitHub Repos:** —我们首先创建两个 [GitHub](https://github.com) repos，一个托管 React 表单，另一个托管节点 API。我们也可以在单个回购中完成，但是为了更好的可维护性，我们使用了两个回购。

**Node & npm** —从[链接](https://nodejs.org/en)下载 node.js 的最新版本。在本帖中，我们使用的是 11.7.0 版本。节点附带 npm。要确保安装了 node 和 npm，请在终端上使用以下命令检查它们的版本:

```
//for node
node -v

//for npm
npm -v 
```

**React** —我们在 React 应用程序中使用 create-react-app 来构建表单。全局安装 create-react-app 并使用以下命令生成 react 应用:

```
//Install create-react-app globally
npm install create-react-app -g

//Generate a new react app called my-app
create-react-app my-app
cd my-app
npm start 
```

## 2。表单

让我们从设置一个返回 HTML 表单的组件开始。我们在下面的代码中使用了 [Bootstrap](https://getbootstrap.com) 类，所以它们是可选的，你也可以使用自己的 CSS 类。