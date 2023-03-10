# 如何使用 ngrok 在本地测试应用认证

> 原文：<https://dev.to/karen_pwhite/how-to-test-app-authentication-locally-with-ngrok-4p9h>

*原载于 [BigCommerce 开发者博客](https://medium.com/bigcommerce-developer-blog)，2019 年 1 月 5 日*

在本地开发可以让您在工作时快速轻松地测试更改，但是没有网络访问也有缺点。在开发阶段，您可能会遇到需要可公开访问的 URL 的情况。也许您想向同事或客户展示您的作品，或者您需要一个安全的、公开可用的回调 URL 来与 web 服务进行交互。你可以把你的应用上传到 Heroku 这样的主机平台，但是每次你更新的时候，你都必须把这些改变上传到你的主机服务器上…这不是很好。

幸运的是，有 ngrok。Ngrok 是一个方便的工具，它为运行在 localhost 上的应用程序创建一个安全的、可公开访问的隧道 URL。您可以共享隧道 URL，以便其他人可以查看您的工作，或者您可以创建可公开访问的路由来完成诸如完成 Oauth 握手之类的事情。

在本教程中，我们将为 BigCommerce 构建一个超级简单的节点应用程序，并演示如何使用 ngrok 从 BigCommerce auth 服务中检索 Oauth 令牌并在您的商店中安装该应用程序，同时仍然在本地工作。这是您在本地测试、运行和认证 BigCommerce 应用程序的全面指南。

## 什么是 ngrok？

Ngrok 是一个免费的程序，它将运行在本地主机上的进程暴露给公共互联网。当您启动 ngrok 时，您可以指定本地服务器运行在哪个端口上，ngrok 会创建一个安全的隧道 URL，使本地应用程序可以公开访问。访问 ngrok URL，你会看到和访问 [http://localhost:myport](http://localhost:myport) 时看到的一样的东西。

当您启动 ngrok 时，您将在终端中看到类似这样的打印输出:

[![ngrok dashboard](img/0824d16b41945f63c4ab4ffd1f7741b7.png "ngrok dashboard")](https://cdn-images-1.medium.com/max/800/0*6yc5G5EsUwJ6eJFM)

注意到那两个转发地址了吗？Ngrok 提供了指向本地主机的 URL 的 https 和 http 版本。Ngrok 还提供了一个 Web 界面仪表板，可以打印出通过隧道的任何 http 流量的详细信息。这在应用程序开发过程中非常有用，尤其是在处理 webhooks 时。

默认情况下，ngrok 会在每次启动时生成一个随机子域。这对于测试来说很好，但是如果你在一个项目上工作了一段时间，并且每次重启 ngrok 时都必须更新项目中的 URL，这可能会很痛苦。如果你注册了一个付费计划，ngrok 允许你指定一个自定义的子域，这样你每次都会有相同的 URL。

真的，解释 ngrok 如何工作的最好方法就是向您展示。让我们下载 ngrok 并构建一个快速的应用程序来运行在 localhost 上进行演示。

## 如何设置 ngrok

准备好开始了吗？您可以使用 npm(我的首选方法)安装 ngrok，也可以手动安装。

### 用 npm 安装

1.  确保你的电脑上安装了 [Node.js](https://nodejs.org/en/download/) 。运行以下终端命令以确认节点已安装并检查您的版本:
    `node -v`

2.  运行`npm install -g ngrok`全局安装 ngrok。现在，您可以从任何目录运行 ngrok 命令。

### 手动安装

1.  前往[https://ngrok.com/download](https://ngrok.com/download)，下载与你的操作系统相对应的软件包。

2.  将文件解压到容易访问的地方，比如根文件夹。

3.  您可以导航到解压缩 ngrok 的文件夹来运行它，或者如果您想从任何位置运行 ngrok，您可以将它移动到 PATH 中的目录，通常是/usr/local/bin。

### 启动 ngrok

1.  打开一个终端窗口，导航到解压 ngrok 的位置。如果您已经全局安装了 ngrok 或者将它移动到您的$PATH 中，那么您可以从任何目录运行 ngrok。

2.  运行以下命令启动 ngrok 并创建一个到本地主机端口 3000 的隧道:
    `ngrok http 3000`

3.  按 Ctrl + C 停止 ngrok。

## 创建快递 App

[Express](https://expressjs.com/) 是一个创建 skeleton Node.js 应用的框架。这是快速创建应用程序文件结构的好方法。

1.  使用以下终端命令安装快速生成器命令行工具:
    `npm install express-generator -g`

2.  创建应用程序。我们指定应用程序应该使用车把视图引擎，并在名为 myapp:
    `express — view=hbs myapp`的文件夹中创建

3.  导航到 myapp 文件夹:
    `cd myapp`

4.  安装依赖项:
    `npm install`

5.  使用以下命令启动应用程序:
    `npm start`

## 隧道 ngrok 到本地主机

是时候把碎片拼在一起了。我们已经安装了 ngrok，并使用 Express 创建了一个骨架应用程序。现在，我们将启动应用程序和 ngrok 来查看运行中的隧道 URL。

1.  打开终端窗口并导航到您的 myapp 目录。运行`npm start`启动应用程序。默认情况下，Express generator 应用程序在 localhost:3000 上启动服务器。如果你想改变端口，它被定义在应用程序的 bin/www 文件的第 15 行，但是我们现在将它留在端口 3000 上。

2.  打开浏览器窗口并导航至 [http://localhost:3000](http://localhost:3000) 。您应该会看到快速应用程序主页:

[![express localhost](img/93b18a5091b8247c838a2695724cc401.png "express localhost")](https://cdn-images-1.medium.com/max/800/0*CPknxeD9UykxzsPx)

1.  打开一个新的终端窗口(保持第一个终端窗口运行)并在端口 3000 上启动 ngrok:
    ngrok http 3000

2.  从终端复制 https 转发 URL，并将其粘贴到新的浏览器选项卡中。您应该会看到与您在本地主机 URL 上看到的相同的 Express app 主页:

[![express ngrok](img/6ee1cf20cd7215df94b52abeee4ccfdb.png "express ngrok")](https://cdn-images-1.medium.com/max/800/0*a1XYZL9mtajn5Y2l)

万岁！这可能看起来不太像，但是我们已经展示了 ngrok 的一个强大特性。你可以把你的 ngrok 转发网址通过电子邮件发给一个朋友，他们会看到相同的 Express 应用主页(只要你在终端上运行 ngrok)。这很酷，但是接下来我们将展示如何使用 ngrok 做更多的事情。我们将创建一个转发 URL，这将允许我们在应用程序中创建公共可访问的路由，这样我们就可以完成在 BigCommerce 商店中安装应用程序所需的 Oauth 流程。

## BigCommerce App 认证

在我们继续之前，了解一下 BigCommerce 应用程序认证的背景会有所帮助，以说明为什么需要 ngrok 来安装仍在本地托管的应用程序。

BigCommerce 应用程序在安装期间使用 Oauth 以编程方式针对商店生成 API 令牌。一旦应用程序收到商店的 API 令牌，应用程序可以将令牌保存在数据库中，以便在调用 API 时重用。

接收 Oauth 令牌的过程需要在 BigCommerce 和应用程序主机之间来回切换。首先，该应用程序需要从 BigCommerce 请求一个临时授权码。当 BigCommerce 发送临时令牌时，它还会发送一些其他信息:API 令牌的授权范围和安装应用程序的商店的散列或标识符。

接下来，应用程序发回一个包含一系列声明的响应，让 BigCommerce auth 服务知道可以返回一个真正的 Oauth 令牌。这些声明包括之前从 BigCommerce 收到的临时身份验证令牌、存储哈希、范围以及在应用程序注册期间提供的客户端 Id 和客户端机密。如果一切正常，BigCommerce auth 服务会发回一个永久的 Oauth 令牌，应用程序会在商店控制面板中显示“已安装”。

所有这些网络请求都需要通过可公开访问的 URL 发生。在测试应用安装和认证时，我们要么需要将应用托管在服务器上，要么托管在 Heroku 这样的平台上，要么使用 ngrok 这样的工具从 localhost 创建隧道 URL。

## 注册 App

要在大型商业商店安装应用程序，你需要……一家商店。在 https://www.bigcommerce.com/报名参加免费试用。

然后，点击右上角的创建帐户，在[https://developer.bigcommerce.com/](https://developer.bigcommerce.com/)注册一个开发工具帐户。请确保使用您注册试用商店时使用的电子邮件地址。使用相同的电子邮件地址链接您的试用商店和您的开发工具帐户，因此您在开发工具中创建的任何应用程序都可以安装在您商店控制面板的草稿应用程序区域中。

Dev Tools 是创建 BigCommerce 应用程序的工作空间。如果你是应用程序市场的供应商，你可以在这里注册新的应用程序并管理应用程序列表的详细信息。现在，我们将只做最基本的步骤来注册一个新的应用程序，并获得一个客户端 Id 和客户端密码。

1.  登录开发工具，点击橙色的**创建应用**按钮。

2.  输入应用的名称。这可以是任何东西——我的测试应用、节点应用，任何你喜欢的东西。

3.  点击**创建 App** 。

4.  点击您创建的应用程序上的**编辑应用程序**图标。

5.  在步骤 1 中，您可以跳过填写个人资料表单。这只是收集 BigCommerce 需要的信息，供想要向应用程序市场提交应用程序的开发人员使用。这不是必需的，但我确实喜欢将图片上传到底部的应用程序摘要区域。图像将显示在控制面板中您的草稿应用程序的卡片上。同样，这不是必需的，但它看起来更好。

6.  跳过步骤 2:应用程序详细信息，继续步骤 3。只有希望向市场提交应用程序的开发人员才需要应用程序详细信息。

7.  在第 3 步，填写回调 URL 字段，用 ngrok 中的 https 转发 URL 替换 example.com。比如:
    Auth:[https://4022ffe4.ngrok.io/auth](https://4022ffe4.ngrok.io/auth)T3】Load:[https://4022ffe4.ngrok.io/load](https://4022ffe4.ngrok.io/load)T6】卸载:[https://4022ffe4.ngrok.io/uninstall](https://4022ffe4.ngrok.io/uninstall)

8.  点击**下一步**直到到达第 6 步，然后点击**更新并关闭**。

## 在快递中创建路线

在应用程序注册期间，我们定义了三个回调 URL:Auth、Load 和 Uninstall。这些 URL 告诉 BigCommerce:当有人安装、加载或卸载我的应用程序时，您应该在这里发送该操作的 http 请求。接下来，我们将在 Express 应用程序中创建这些路由，以处理来自 BigCommerce 的授权、加载和卸载请求。

路由监听特定路径的 http 请求，GET 或 POST，然后在 http 请求发生时做*一些事情*，比如运行一个函数或调用一个响应方法。

### 创建授权路径:

1.  通过运行 myapp 目录中的 npm install node-bigcommerce 来安装 [BigCommerce 节点客户端](https://github.com/getconversio/node-bigcommerce)。这个包是由 [Conversio](https://conversio.com/) 的开发者编写的，用来认证和调用 BigCommerce API。

2.  在 routes 目录中创建新文件 auth.js。

3.  将以下内容粘贴到文件内容中:

```
const express = require('express'),
router = express.Router(),
BigCommerce = require('node-bigcommerce');

const bigCommerce = new BigCommerce({
clientId: 'your Client Id from app registration',
secret: 'your Client Secret from app registration',
callback: 'https://your-ngrokURL/auth',
responseType: 'json'
});

router.get('/', (req, res, next) => {
bigCommerce.authorize(req.query)
.then(data => console.log(data))
.then(data => res.render('auth', { title: 'Authorized!' })
.catch(err));
});
module.exports = router; 
```

在这里，我们要求在文件的顶部有 Express 路由器和 BigCommerce 节点客户机，并实例化一个新的 BigCommerce 配置对象。

看看 BigCommerce 配置对象下面的函数。使用`router.get(‘/’, (req, res, next)`，我们告诉路由器，当您接收到对该路径的 GET 请求时(该路径是/auth，因为我们在 routes/auth.js 文件中)，从节点客户端依赖项调用 authorize 函数。

[authorize 函数](https://github.com/getconversio/node-bigcommerce/blob/master/lib/bigcommerce.js#L75)从 BigCommerce 返回一个数据对象，其中包含商店散列、用户的电子邮件地址(用于标识用户)以及与商店相关联的 Oauth 令牌。如果我们要进一步开发这个应用程序，我们希望将这些信息保存到数据库中，以便重用。

一旦我们返回 Oauth 令牌，我们调用 res.render 来呈现一个名为“auth”的视图，该视图传入文本“Authorized！”

1.  替换您的客户端 Id、客户端密码和 ngrok 隧道 URL 的值。

2.  通过在 Views 文件夹中创建一个名为 auth.hbs 的新文件来创建“auth”视图。

3.  将以下内容粘贴到文件内容中:
    `<h1>{{title}}</h1>`

### 创建装载路线:

1.  在 Routes 文件夹中创建名为 load.js 的新文件

2.  将以下内容粘贴到文件内容中:

```
const express = require('express'),
router = express.Router(),
BigCommerce = require('node-bigcommerce');
const bigCommerce = new BigCommerce({
secret: 'your Client Secret',
responseType: 'json'
});

router.get('/', (req, res, next) => {
try {
const data = bigCommerce.verify(req.query['signed_payload']);
console.log(data);
res.render('welcome', { title: 'Welcome!'});
} catch (err) {
next(err);
}
});

module.exports = router; 
```

1.  将“secret”的值替换为您的客户端机密。与我们创建的/auth 路由类似，我们指定了一个回调函数，在对/load 路由的 GET 请求之后执行。我们调用 [verify](https://github.com/getconversio/node-bigcommerce/blob/master/lib/bigcommerce.js#L38) 函数来验证请求是否来自 BigCommerce 并识别商店和用户。当成功时，我们控制台记录数据对象并呈现名为“welcome”的视图

2.  通过在 Views 目录中创建一个名为 welcome.hbs 的新文件来创建“welcome”视图。

3.  将以下内容粘贴到文件内容中:
    `<h1>{{title}}</h1>`

### 创建卸载路径:

1.  在 Routes 目录中创建一个名为 uninstall.js 的新文件

2.  将以下内容粘贴到文件内容中:

```
const express = require('express'),
router = express.Router(),
BigCommerce = require('node-bigcommerce');
const bigCommerce = new BigCommerce({
secret: 'Your Client Secret',
responseType: 'json'
});

router.get('/', (req, next) => {
try {
const data = bigCommerce.verify(req.query['signed_payload']);
console.log(data);
} catch (err) {
next(err);
}
});

module.exports = router; 
```

1.  将“secret”的值替换为您的客户端机密。在/uninstall 回调中，我们使用 verify 函数来解码从 BigCommerce 发送的签名负载，并记录识别卸载应用程序的用户的负载。在这种情况下，我们不呈现视图，因为 BigCommerce 不会呈现任何发送回 BigCommerce 的 HTML。

## 加载 App.js 中的路由器模块

在使用我们创建的路由模块之前，我们需要将路由器模块挂载到主应用程序文件中的一个路径上。

1.  在 app.js 文件顶部附近添加以下内容:

```
var auth = require(‘./routes/auth’);
var load = require(‘./routes/load’);
var uninstall = require(‘./routes/uninstall’); 
```

1.  找到 app.js 文件中间附近的这些行:

```
app.use(‘/’, index);
app.use(‘/users’, users); 
```

在它们下面，添加:

```
app.use(‘/auth’, auth);
app.use(‘/load’, load);
app.use(‘/uninstall’, uninstall); 
```

## 安装 App

现在，我们已经将应用程序连接到适当的路线路径，是时候将应用程序安装到您的商店了。

1.  通过运行 myapp 目录中的`npm start`命令启动 Express 应用程序。

2.  打开第二个终端窗口，在端口 3000 上启动 ngrok:
    `ngrok http 3000`

确保在开发工具中更新 ngrok URL，以匹配您当前会话中的 ngrok URL。

1.  登录您的 BigCommerce 商店，导航至应用>我的应用>我的应用草稿选项卡。

2.  点击 app 卡上的**了解更多**，然后点击**安装**。你应该看到你的应用程序的授权！消息。授权了！视图表明我们已经成功地从 BigCommerce 收到了 Oauth 令牌。

3.  通过返回到控制面板中的“我的应用程序”部分来测试加载路径。现在应用程序已经安装，你会看到两个新的按钮:加载和卸载。点击 **Load** 按钮渲染欢迎视图。

4.  现在，点击**卸载**按钮。该应用将从我的应用部分中移除。检查终端中记录到控制台的 auth、load 和 uninstall 数据对象。

## 常见问题

我已经安装了 ngrok。当我尝试启动它时，为什么会出现“找不到命令”?

您可能没有从可执行文件的工作目录运行 ngrok。您可以将文件移动到 PATH 目录，或者导航到包含 ngrok 的目录。exe 文件。例如，如果您在根目录中解压缩 ngrok，您可以使用以下命令运行它:

`cd ~`

`./ngrok http 3000`

如何使用 ngrok 测试 webhooks？

Webhooks 允许你监听发生在第三方平台上的事件。例如，当 BigCommerce 产品的库存发生变化时，您的应用程序可能希望收到通知，以便您的应用程序可以采取某种行动。

注册 webhook 时，可以提供一个 ngrok 隧道 URL 作为目标 URL。当 webhook 事件发生时，您将通过您的隧道接收 webhook 数据对象。您可以在 ngrok Web 界面仪表板中显示数据对象的详细信息，并在您的应用程序中处理该事件。

关于用 ngrok 测试 webhooks 的更多细节，请参见本教程中的[注册和测试 webhooks](https://developer.bigcommerce.com/api-docs/getting-started/webhooks/setting-up-webhooks) 。

**为什么我在访问我的 ngrok 隧道 URL 时会收到 502 错误网关错误？**

Ngrok 期望 web 服务器在本地主机上运行。如果没有运行服务器，您将看到一个 502 错误，并伴随一条消息，指出 ngrok 无法完成隧道连接。默认情况下，Express generator 应用程序在端口 3000 上启动服务器，因此您只需确保在向 ngrok URL 发出请求之前启动应用程序。

## 总结

在本教程中，我们安装了 ngrok，并使用它将我们在本地托管的应用程序上注册的路由隧道化到可公开访问的回调 URL。干得好！现在，您已经可以在本地构建和测试您的应用程序了——当您仍处于开发阶段时，无需将您的更改部署到应用程序主机。

以此为起点，在您的应用程序中构建更多的复杂性:将您的 Oauth 令牌保存到数据库中，并使用它来调用 BigCommerce API 或创建视图模板，为您的应用程序用户提供 UI。寻找 Python、PHP 或 Ruby 版本的 Hello World 应用程序？访问 big commerce[Tools&Resources](https://developer.bigcommerce.com/tools-resources)页面，获取其他语言的示例应用和 API 客户端。

让我们知道你在做什么，问我们问题，给我们发送你的反馈！在下面评论或发推特给我们 [@BigCommerceDevs](https://twitter.com/BigCommerceDevs)