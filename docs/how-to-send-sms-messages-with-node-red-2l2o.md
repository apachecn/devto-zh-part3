# 如何用 Node-RED 发送短信

> 原文：<https://dev.to/vonagedev/how-to-send-sms-messages-with-node-red-2l2o>

*这是“Nexmo 和 Node-RED 入门”系列教程的第一篇文章。*

Nexmo [SMS API](https://developer.nexmo.com/api/sms) 允许您使用简单的基于 HTTP 的 API 在全球范围内发送和接收 SMS。

好消息是，随着 Nexmo 节点添加到 Node-RED 调色板中，与这个 API(以及许多其他 API)的交互变得更加容易。

在本文中，您将学习如何使用 Node-RED 发送 SMS 消息。

从[节点-红色库](https://flows.nodered.org/flow/570cfeef04a6502c7a24c0e9cfc3323a)获取此流或跟随。

## 先决条件

开始之前，您需要一些东西:

*   安装在您机器上的 [Node.js](https://nodejs.org/en/) 和 [Node-RED](https://nodered.org/docs/getting-started/installation)
*   Nexmo 帐户–[如果您还没有，请免费创建一个帐户](https://dashboard.nexmo.com/sign-up)
*   [ngrok](https://ngrok.com/download)–了解[亚伦的博客文章](https://www.nexmo.com/blog/2017/07/04/local-development-nexmo-ngrok-tunnel-dr/)

## 领取证件

为了与 SMS API 进行交互，您需要注意一些事情。一旦你创建了一个 Nexmo 账户，进入[仪表盘](https://dashboard.nexmo.com)找到你的 API 密匙和密码。

接下来，你需要一个支持短信的虚拟号码。前往 Numbers >购买号码以获得它。

[![](img/4c032fde823194a67e6f62d1c55a8c03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WZszTx09--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/04/buy-number-nexmo-dashboard.gif)

在一些国家，您可以发送短信而无需购买 Nexmo 号码，而在美国等其他国家，您需要拥有发送短信的号码。

## 设置你的节点-红色编辑器

首先，你需要[安装](https://nodered.org/docs/getting-started/installation)运行时和编辑器。这可以在您的本地机器、单板计算机(例如 Raspberry Pi)上完成，也可以在许多云托管选项上完成。这个示例将使用您的本地机器，所以一旦您已经全局安装了 Node-RED，只需在您的终端中键入以下命令即可开始。

```
$ node-red 
```

然后，您可以通过将浏览器指向 [http://localhost:1880](http://localhost:1880) 来访问 Node-RED 编辑器。

打开编辑器后，您需要安装 Nexmo 节点。你可以在*管理调色板*菜单下搜索`node-red-contrib-nexmo`包并点击安装。

[![](img/036dd84c9af0c400f54e1ad04d5e180c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7thrqhzf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/04/install-nexmo-nodered.gif) 

现在你应该看到所有的 Nexmo 节点出现在你的屏幕左侧，在其他默认节点中。

## 用 Node-RED 发送短信

向下滚动到 **`sendsms`** 节点，并将其拖动到您的工作区。这将在今天的教程中完成繁重的工作。

您可以通过双击该节点并填写下面的参数来设置它。你可以通过点击`Nexmo Credentials`旁边的编辑按钮找到`API KEY`和`API SECRET`。

| 键 | 描述 |
| --- | --- |
| `API KEY` | 您的 Nexmo API 密钥，显示在您的[账户概述](https://dashboard.nexmo.com/getting-started-guide)中。 |
| `API SECRET` | 您的 Nexmo API 秘密，显示在您的[账户概述](https://dashboard.nexmo.com/getting-started-guide)中。 |
| `TO` | 您以 E.164 格式向其发送短信的号码。例如 447401234567。 |
| `FROM` | 手机显示您的消息时显示的号码或文本。如果您的国家支持此功能，您可以从设置自定义字母数字以更好地代表您的品牌。 |
| `TEXT` | 你的信息内容。尽情发挥创造力，但一句“你好，世界！”会做得一样好。 |

接下来，向流添加一个 **`inject`** 节点，并将其连接为 **`sendsms`** 的输入。

这是一个非常强大的工具，它可以用一个特定的有效载荷值启动流。它支持注入字符串、数字、布尔值、JavaScript 对象或流/全局上下文值，缺省情况下使用自 1970 年 1 月 1 日以来以毫秒为单位的当前时间的时间戳。

您可以简单地使用它来设置您的流程，同时硬编码 **`sendsms`** 节点中的所有参数，或者您可以更进一步。

在`TO`、`FROM`和`TEXT`参数旁边有一个`{}`符号，这意味着这些字段支持 [Mustache 模板](https://mustache.github.io/)。这在将某些值动态传递给 **`sendsms`** 节点时很方便。

知道了时间戳是注入的，我们通过引用 **`{{msg.payload}}`** 把这个值加到消息正文中，比如:

```
Hello World! The timestamp is `{{msg.payload}}`. 
```

为了更深入地了解发送短信时发生的事情，将 **`sendsms`** 输出连接到 **`debug`** 节点。双击它并将`Output`设置为`complete msg object`。

点击**部署**并点击 **`inject`** 节点的按钮——你的短信应该在路上了！

[![](img/b3e069b480c4cb01589b2f348b3306b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AIhamswe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/04/send-sms-flow.gif) 

当你向 SMS API 发出一个成功的请求时，它返回一个消息对象数组。理想情况下，每条短信都有一个`0`的`status`，表示您的短信已成功安排发送，还有一些其他细节，如收件人号码、短信 id、余额、价格和将用于发送短信的运营商网络。

您可以在 Node-RED 编辑器右侧的调试区域中查看这个响应对象。

虽然检查这个输出对确定 Nexmo SMS API 做了什么很有帮助，但是不能保证消息到达了接收者的手机。

当消息送达时，手机运营商向 Nexmo 返回一张 [**送达回执**](https://developer.nexmo.com/messaging/sms/guides/delivery-receipts) 。这将包含交货状态。

## 接收移动运营商的发货回执

为了找出出站消息的`status`,您需要设置一个 webhook 端点，Nexmo 可以将**送达回执**转发给它。

### 定义一个 Webhook 端点

首先，将一个`http`输入节点连接到一个`http response`节点，以及一个`debug`节点，这样您就可以在调试区域查看您的交付收据。

在`http`输入节点中，选择`POST`作为`Method`，在`URL`字段中填入类似`/receipt`的内容。

`http response`节点应该将`200`设置为`Status code`，但是不用担心，这也是默认值。

[![](img/66515bb49436c2faa20ba5dd5b00bf2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XzioaGTf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/04/delivery-receipt-flow.gif)

### 将您的本地服务器暴露在互联网上

接下来，您必须将您的本地服务器暴露在互联网上，以便 Nexmo 可以访问它。一种方便的方法是使用隧道服务，比如 [ngrok](https://ngrok.com) 。

[下载](https://ngrok.com/download)并安装 **ngrok** ，然后在终端运行它，在端口`1880`上启动隧道。

```
$ ./ngrok http 1880 
```

[![](img/d545a58d30002a48fc828db029cc6d2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HqZ5NGBw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/04/ngrok-1880.png) 

您的本地服务器现在有了一个 ngrok URL，可以用作您的 webhook 端点。

### 用 Nexmo 设置端点

最后一步是让 Nexmo SMS API 知道应该将交货收据转发到哪里。你可以在**默认短信设置**部分的 [API 设置](https://dashboard.nexmo.com/settings)下进行设置。

将交货收据的默认 webhook URL 设置为`YOUR_NGROK_URL/receipt`，然后设置为`Save changes`。

[![](img/1111fff5427825b97cee33c395ff7b48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G5q0PN5y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/04/delivery-receipt-endpoint-url.png) 

现在，当您返回到您的节点-红色编辑器并发送另一条消息时，您会看到交货收据出现在调试区域:

[![](img/a6be624ade2e56698fa29cb0e9bb66c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6hOijjl_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/04/delivery-receipt-in-debug.png)

`status`和`err-code`参数都表示消息已经成功发送。在 Nexmo [文档](https://developer.nexmo.com/messaging/sms/guides/delivery-receipts)中了解有关交付收据状态消息和错误代码的更多信息。

## 资源

*   [宣布 Nexmo Node-RED 包](https://www.nexmo.com/blog/2019/02/21/nexmo-node-red-package-dr/)
*   了解更多关于 Nexmo [SMS API](https://developer.nexmo.com/api/sms) 的信息
*   更好地理解[交货收据](https://developer.nexmo.com/messaging/sms/guides/delivery-receipts)
*   仔细看看[节点——红色](https://nodered.org/docs/)