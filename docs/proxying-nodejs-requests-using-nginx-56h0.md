# 使用 Nginx 代理 Nodejs 请求

> 原文：<https://dev.to/patelviral_12/proxying-nodejs-requests-using-nginx-56h0>

嘿伙计们，我是病毒。我是一名全栈开发人员，主要负责 Vuejs、Laravel、Nodejs 和 AWS。今天，我们将学习如何使用 Nginx 反向代理将端口 80 上的请求代理到不同的端口，以便我们部署一个中间 web 服务器来实现负载平衡，或者为我们所有的小型服务器使用单个 ssl，或者仅作为我公司的 POC。干杯！

这是一个非常基础的教程，强调理解 Nginx 的反向代理特性。

*我们开始吧，*

我们将使用 Nginx 的反向代理，因为它就像是这个世界之外的东西，让我大吃一惊。我在这里试图实现的是理解我们如何运行 Nginx 服务器，以及如何将来自端口 80(大多数 web 服务器上的默认端口)的传入请求代理到运行 Nodejs 服务器的不同端口(可能是端口 3000)。让我们假设端口我们现在在端口 4040 上运行我们的测试 Nodejs 应用程序。

我们将从使用 Express 和 socket 设置 Nodejs 应用程序开始，这样我们就可以在工作和 Nginx 服务器中看到它。

**Nginx**

为此，您需要安装 Nginx。完成后，打开默认的站点可用文件，该文件主要位于`/etc/nginx/sites-available/`

使用 nginx 最新版本的这个包 URL:

```
sudo add-apt-repository -y ppa:nginx/development
sudo apt-get update 
```

如果你正确安装了 nginx，那么当你访问服务器 URL 时，你会看到这样一个屏幕。我在本教程中使用 AWS，所以它应该看起来像这样。

[![](img/5925b273b8217b78293deb8ffba80a3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Y2gYUhO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bptkmpunhxbobkz7vuhv.png)

如果你看到了上面的图片，那么你的设置已经完成，我们可以继续了。

打开 Nginx 的默认配置文件。

```
sudo vim /etc/nginx/sites-available/default 
```

[![](img/e8a11917bb9c2b484736e510750218d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mytasKWH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/neps2v8w0jvb0i2a55jf.png)

这是一个基本的 nginx 默认配置文件，只有一个监听端口 80 的监听器。所以当一个请求进来时，它在`/var/www/html`加载文件，正如你在上面的根参数中看到的。

我们的 Nginx 设置已经完成，我们将在这篇文章的后面部分回到这个问题。让我们继续设置我们的 Nodejs 应用程序。我假设您已经在服务器上设置了 Nodejs 和 npm。

# 节点

#### 依赖关系

在你的项目根

```
mkdir nodetestapp
cd nodetestapp
npm init
npm install express
touch index.js 
```

打开 index.js，粘贴以下代码

```
const express = require('express');
const app = express();
const port = 4040;

app.get('/',(req,res) => {
        res.send('Hello world');
});

app.listen(port, () => {console.log('server running on port', port)}); 
```

现在运行在终端

```
node index.js 
```

并导航到您的 SERVER_URL:4040(因为我的 AWS 安全组设置为这个)。对我来说是`[http://3.95.169.170:4040/](http://3.95.169.170:4040/)`。用您的服务器地址替换 IP 地址。您应该会看到这样的屏幕。

[![](img/fb5d45353fb89d62e21159945b479f8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aKKnJ_OT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ooacrq29twionu7ppjhk.png)

如您所见，我们在端口 4040 上运行 nodejs 服务器，这是一切变得奇怪的地方。一些奇怪的问题会是，为什么我必须在 URL 后面键入 4040 和“:”，为什么不只是端口 80，这样我就不必每次都键入它。我们将使用 Nginx 反向代理来解决这个问题。既然我们的设置已经完成，让我们回到 Nginx 文件并做一些修改。

我们需要做的:
1。)当我们在端口 80 上收到一个请求时，我们将配置 nginx 将其代理到本地主机上的端口 4040，因为我们的 nodejs 正在那里运行。
2。)检索成功的响应并显示在浏览器中。

打开 Nginx 配置文件

```
sudo vim /etc/nginx/sites-available/default 
```

将以下代码粘贴到位置/ {}块

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
proxy_http_version 1.1;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header Host $host;
proxy_pass http://localhost:4040; 
```

在上面的代码块中，我们指示 Nginx 用我们的一些自定义需求替换请求头，并将其发送到`localhost:4040`，因为我们正在端口`:4040`上运行 nodejs 服务器。

按下`ESC`键保存文件，然后输入`:wq`并按下`ENTER`

重启 Nginx

```
sudo service nginx reload 
```

使用
启动 nodejs 服务器

```
cd nodetestapp
node index.js 
```

打开浏览器，转到没有端口的服务器 URL，您会看到 nodejs 应用程序在端口 80 上运行。但是，我们仍然在内部使用端口 4040 来运行 Nodejs 服务器。

[![](img/a6c72f4d5d2c944eb60346bba042bbd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4DgqjVqm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vu458bmst507o6bh9mwt.png)

如果你理解了所有的东西并按照上面的方式设置，你应该能够看到 Nodejs 输出，而不是我们在 Nginx 默认文件中设置的根目录。

# 我的案例

这是对 Nginx 反向代理如何在幕后工作的基本理解。然而，它的威力远不止于此。我遇到的使用它的最好的例子之一是在端口 80 上运行的同一台服务器上设置 PHP 和 Nodejs 应用程序。

我当然希望这能帮助你。如果你有任何问题，请在下面的评论中告诉我。

页（page 的缩写）我将在演示中关闭服务器。所以不要麻烦点击网址。

封面图片来源:[https://www.journaldev.com/](https://www.journaldev.com/)