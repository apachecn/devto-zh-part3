# 如何快速部署 Node Js App 到 Heroku

> 原文：<https://dev.to/3sanket3/how-to-quickly-deploy-node-js-app-to-heroku-58k4>

本教程将介绍如何将 Node.js 项目部署到 Heroku——云应用程序平台。我们将通过运行几个命令来启动我们的应用程序。

假设您有任何想要发布的 node.js 应用程序。如果没有，我已经在[本教程](https://3sanket3.com/posts/how-to-setup-basic-node-server)中列出了步骤。codesandbox 同样是[这里](https://codesandbox.io/embed/79wz0k0v1)用来设置基本的 app。

## 先决条件

*   饭桶

    *   要使用 Huroku CLI，我们需要在机器上安装 Git，所以请确保您已经安装了 Git。

您可以使用
检查它是否已经安装

```
 $ git --version
    git version 2.18.0.windows.1 
```

*   节点 8 以上

```
 $ node --version
  v8.12.0 
```

*   在[https://heroku.com](https://heroku.com)的注册账户
    *   如果还没有，请注册并验证您的电子邮件地址。

## 初始化项目的 Git(如果不是)

如果你的项目是 git 库本身，那么转到[安装 heroku](#install-heroku)

```
$ git init
Initialized empty Git repository in your-project-path/.git/ 
```

创建一个`.gitignore`文件来忽略`node_modules`

```
$ echo node_modules > .gitignore 
```

将文件提交到 git 存储库

```
$ git add -A

$ git commit -m 'initial commit'
[master (root-commit) e79168d] initial commit
 4 files changed, 2047 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 index.js
 create mode 100644 package.json
 create mode 100644 yarn.lock 
```

## 安装 Heroku

根据您的平台下载并安装 Heroku CLI。

通过检查版本来检查它是否已安装。

```
 $ heroku --version
  heroku/7.24.3 win32-x64 node-v11.14.0 
```

## 登录 Heroku

命令会要求我们按任意键打开浏览器。然后你可以登录浏览器，终端会捕捉到它。是不是很酷！

```
$ heroku login
heroku: Press any key to open up the browser to login or q to exit:
Opening browser to https://cli-auth.heroku.com/auth/browser/06c81181-c988-457f-b415-5789e7abd758
Logging in... done Logged in as 3sanket3@gmail.com 
```

## 创建 Heroku app

使用命令`heroku create <optional-app-name>`
创建应用程序

```
$ heroku create nodejs-tutorial-one-3sanket3

Creating ⬢ nodejs-tutorial-one-3sanket3... done https://nodejs-tutorial-one-3sanket3.herokuapp.com/ | https://git.heroku.com/nodejs-tutorial-one-3sanket3.git 
```

## 确保你的项目的`package.json`有一个`start`命令

```
...  "scripts":  {  "dev":  "nodemon index.js",  <--  for  local  development  purpose  "start":  "node .",  <--  Heroku  will  use  to  start  the  application  "test":  "echo \"Error: no test specified\" && exit 1"  },  ... 
```

> 注意:如果你想使用`start`命令来完成其他任务，你可以创建一个`Procfile`。它会告诉 Heroku 在启动应用程序时运行哪个命令，正如这里的[所解释的](https://devcenter.heroku.com/articles/getting-started-with-nodejs#define-a-procfile)。

## 部署代码

```
$ git push heroku master 
```

如果一切正常，您应该会看到命令输出的最后一部分，如下所示。它包含部署应用程序的 URL。

[![heroku deployment output](img/ffbc6fdfe8482ff3aed1ddd8e6542754.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2wRxP5eU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oavbqxgphz9tx0lh396e.PNG)

## 查看部署日志

使用`heroku logs --tail`命令，您可以检查部署日志。它会实时更新，所以你可以在部署时保持打开，在另一个终端标签/窗口保持你的眼睛在它上面。

ref:[https://dev center . heroku . com/articles/getting-started-with-nodejs](https://devcenter.heroku.com/articles/getting-started-with-nodejs)
封面照片由 [Rakicevic Nenad 从 Pexels](https://www.pexels.com/photo/silhouette-photo-of-man-throw-paper-plane-1262304/)