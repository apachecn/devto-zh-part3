# 使用 Azure 函数代理和逻辑应用自定义连接器发送电报消息

> 原文：<https://dev.to/foppenma/sending-telegram-messages-with-azure-function-proxy-and-logic-app-custom-connector-38o2>

[![Sending Telegram Messages With Azure Function Proxy and Logic App Custom Connector](img/c3a39115a18feb934b8ed8dd508a589b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O_1CGEuz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/03/logic-app-icon-5.png)

前几天，我在 Azure Maps 中试验新的地理围栏功能。我试图实现的是当我离开家的时候给我的电报发一条信息。能有多难对？

然后我发现 Telegram 没有逻辑应用程序连接器，他们的 Bot API URL 格式也不像我希望的那样直接。这解释了为什么还没有连接器。为了解决这个问题，我使用了一个逻辑应用连接器来调用 Azure 函数代理。代理将调用转换成 Telegram API 能够理解的东西。这让我可以连接一次，并轻松地从同一个 Azure 订阅中的多个逻辑应用程序发送消息。最重要的是，这是一个“无代码”的解决方案。

## 先决条件

要做到这一点，向 Telegram 注册一个机器人是有先决条件的。这是一个简单的过程，微软已经解释过了。使用本指南注册您的 bot[https://docs . Microsoft . com/en-us/azure/bot-service/bot-service-channel-connect-telegram？view=azure-bot-service-4.0](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-telegram?view=azure-bot-service-4.0) 或者从之前注册的 bot 中准备好 bot 密钥。

在 bot 注册旁边，我们还需要检索聊天 id。这可以通过用@get_id_bot 开始聊天来实现。说什么它都会返回你的聊天 id。还可以将@get_id_bot 添加到组中，以检索组 id。

## 用电报机器人 API 发送消息

在检索到机器人密钥和聊天 id 后，我们应该能够用机器人发送消息。这可以通过使用以下 url 并替换占位符来实现:

```
https://api.telegram.org/bot<botKey_here>/sendMessage?text=<text_here>&chat_id=<id_here> 
```

一个例子是:

```
https://api.telegram.org/bot628752025:AAFjS9MR3MubX-RWpxqltVtR5Wju9EoUAAo/sendMessage?text=Hello World&chat_id=123456789 
```

注意，在我们调用的 URL 中有一个前缀“bot”。这就是为什么只有一个逻辑应用程序连接器是不够的。Logic App 连接器不允许在路径中使用身份验证类型“API 密钥”中的 API 密钥。目前仅支持“标题”和“查询”。我们可以做的是利用 Azure 函数代理将 URL 从查询字符串参数重写为有效的 Telegram Bot API URL。你可以测试这个网址，只要把它粘贴到你的浏览器中，你就会从电报机器人那里得到你的第一条信息。

## Azure 函数代理

为了能够更改 URL，我们需要添加一个新的 Azure 函数应用程序。为此我使用了消费计划，但是专用计划也可以。我创建了一个这样的功能应用程序:

[![Sending Telegram Messages With Azure Function Proxy and Logic App Custom Connector](img/d13396abb9ab39ccba1806b678afc33e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--62bTe2gk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/03/CreateFunctionProxy.png)

创建函数应用程序后，进入高级编辑器并使用下面的 JSON:

```
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "telegramtrigger": {
            "matchCondition": {
                "route": "/{operation}",
                "methods": [
                    "GET"
                ]
            },
            "backendUri": "https://api.telegram.org/bot{request.querystring.apikey}/{operation}"
        }
    }
} 
```

" route": "/{operation} "使此代理在每次调用可在代理概述中找到的基本 URL 时触发。“backendUri”是该代理将调用的新 URL。

[![Sending Telegram Messages With Azure Function Proxy and Logic App Custom Connector](img/dd7d59aeace5796929ed3565caa7d577.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_7bF7iJ9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/03/TelegramProxy.png)

然后，通过在其中添加 bot 密钥来更改 URL。最后的{operation}实际上只是复制你的基本 URL 后面的所有内容，并将其粘贴到新的“backendUri”中。

## 逻辑 App 自定义连接器

列表中的下一个是实现逻辑应用程序自定义连接器。你可以从[https://github.com/foppenm/TelegramLogicAppConnector](https://github.com/foppenm/TelegramLogicAppConnector)下载自定义连接器模板。在类似 VS 代码的编辑器中打开 de JSON 文件，并更改为以下内容以匹配您的代理 URL:

[![Sending Telegram Messages With Azure Function Proxy and Logic App Custom Connector](img/6273679679cf405a59043dee13ef071e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d2Mc5Q1d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/03/ChangeJson.png)

现在，让我们创建一个逻辑应用程序自定义连接器，并按编辑。

[![Sending Telegram Messages With Azure Function Proxy and Logic App Custom Connector](img/85568dd7fa91801e69848d6665329849.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v32i3-Ha--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/03/EditConnector.png)

从上一步导入更改后的 JSON 后，您只需按“更新连接器”，就可以开始在您的逻辑应用程序中使用它了。你也可以选择一种颜色和一个图标。这决定了它在 logic apps 设计器中的显示方式。

现在，当在 Azure Maps 地理围栏中检测到区域变化时，我可以向我的电报发送消息。为此，首先将自定义电报连接器添加到您的逻辑应用程序中，并使用您的 bot 密钥。

[![Sending Telegram Messages With Azure Function Proxy and Logic App Custom Connector](img/b23412798eb99a1dae4206384d4d5cf3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U4bYgvFS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/03/TelegramConnection.png)

然后使用事件网格触发器和来自自定义连接器的“发送消息”步骤，从 Azure Maps 发送有关区域更新的消息。

[![Sending Telegram Messages With Azure Function Proxy and Logic App Custom Connector](img/da6cd33b08a43d4f1438902d20a1245e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OvDtVjjN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/03/EventGridTelegram.png)

## 结论

这是自定义连接器和函数代理的最佳用途吗？可能不会，但弄清楚它能做什么确实很有趣，而且在某些情况下它可能非常强大。我发现值得注意的是，这是一个没有代码的解决方案。当然，你可以在电报中使用 azure 函数。但是在我看来，对于这种仅仅发送简单消息的场景来说，完全是矫枉过正。

感谢阅读！