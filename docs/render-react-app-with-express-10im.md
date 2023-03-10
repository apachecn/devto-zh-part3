# 使用 Express 渲染 React 应用程序

> 原文：<https://dev.to/httpjunkie/render-react-app-with-express-10im>

所以我从一个非常简单的任务开始，我有一个 Node JS Express 应用程序，它为我的 React 应用程序使用的一些端点提供服务。每个项目、Express 应用程序和 React 应用程序都位于不同的仓库中。

我希望能够运行一个进程(API)并让它自动服务于 React 应用程序。

所以我的想法是找一个快速应用程序做这件事的例子，这很简单。如果您需要提供的只是一个文件，比如 React 应用程序，那么 Express 绝对可以作为一个简单的开发服务器。

因此，我在我最喜欢的 VPS 主机提供商那里创建了一个 Ubuntu 服务器，并创建了一个用户 SSH，阻止 root 访问，允许我的应用程序在端口 80 上运行，这些说明就在那里。

因此，现在我有一个服务器准备了大部分，我想开始玩建筑和新的 API 和反应项目并排，但在不同的回购。

至于 React 应用程序，只要确保它在您的本地机器上运行即可。

现在对于 Express 应用程序，我们将修改`index.js`以服务于 React 项目，该项目位于我安装 React 应用程序的目录旁边。

我在我的本地机器上测试了相同的设置，每个 repo 都紧挨着安装。

这里是`index.js`的代码，我只需要打开一个目录，然后深入到我的 react 项目并提供那个文件。

```
const express = require('express');
const path = require('path');
const app = express();

app.use(express.static(path.join(__dirname,'../react-js-client/dist')));

app.get('/', function (req, res) {
  res.sendFile(path.join(__dirname, '../react-js-client/dist', 'index.html'));
});

app.listen(process.env.PORT || 80); 
```

Enter fullscreen mode Exit fullscreen mode

我还使用`pm2`在一个进程上运行我的应用程序，你也可以使用:

`sudo apt-get pm2 -g`

我可以切换到我的 API 项目目录并运行以下代码来启动 API 和我的 React 应用程序:

`pm2 start index.js --name my-api-and-client`

这很酷，如果我想的话，我现在可以进一步告诉我的 Express 应用程序通过传递变量来加载哪个 react 客户端或 repo。这样它就不需要知道自己在服务什么应用了。上面我们已经对兄弟目录进行了硬编码，但大多数 react 应用程序都具有相同的结构，并且可以从任何相邻的 repo 中检索到`dist`,这给了我一些关于切换到我正在开发的使用相同 API 的不同 react 应用程序的酷想法。

这是我心中的一种黑客行为，我很想知道你认为这样做的利弊是什么，但我有一个非常简单的网站，只是为了我自己的开发目的，这是最简单的方法让它上线，两个应用程序一起说话。

我的下一步是将 express 应用程序挂接到 MongoDB 以获取一些数据，构建我的端点，并在构建时慢慢调整我的 React 应用程序以使用新的和更新的端点。

如果你喜欢这篇文章和关于 React 的类似内容，请查看我在 Telerik 博客和 Dev.to 上的其他文章。