# 使用 Node.js 和 Express 构建交互式语音响应菜单

> 原文：<https://dev.to/vonagedev/build-an-interactive-voice-response-menu-using-node-js-and-express-3io8>

*这篇文章最初出现在[Nexmo 博客](https://www.nexmo.com/blog/2019/04/08/build-interactive-voice-response-node-express-javascritpt-dr/)上，但是我想特别为 dev.to 社区添加一些内容。如果你不想跟进，但你只是想尝试一下，我已经在 Glitch 上放了我的[代码，并设置了一个 Nexmo 应用程序，其中包含一些不同国家的 Nexmo 电话号码，你可以拨打+442038973497 或+19373652539，并播放交互式语音响应。如果你想让我为你的国家设置一个号码，只需在 Twitter 上告诉我就可以了，我会在这里提供并更新帖子供其他人使用。](https://glitch.com/edit/#!/nexmo-ivr-menu?path=index.js:1:0)*

我们将构建一个交互式语音响应菜单，浏览设置 Node.js 应用程序所需的一切，该应用程序可以接收入站呼叫并捕获通过键盘输入的用户输入。

通过学习本教程，您将最终拥有一个简单的应用程序，它可以扩展为包含更复杂的交互式元素，并为您的调用者提供一个构建交互式菜单的良好开端。

本教程的代码可以在 [GitHub](https://github.com/Nexmo/nexmo-node-code-snippets/blob/master/voice/ivr-menu.js) 上找到。

## 先决条件

*   一个 [Nexmo 账户](https://dashboard.nexmo.com/sign-up)
*   安装在您机器上的 [Node.js](https://nodejs.org/en/download/)
*   为了让外部世界能够访问我们本地机器上的代码
*   [Nexmo CLI](https://developer.nexmo.com/tools) : `npm install -g nexmo-cli`

## 设置

当 Nexmo 收到您租用的电话号码的呼叫时，会向一个 URL(您指定的“webhook ”)发出 HTTP 请求，其中包含接收和响应呼叫所需的所有信息。这就是通常所说的*答案 URL* 。

Nexmo 将关于调用进度的所有信息发送到一个 webhook URL，您将在创建 Nexmo 应用程序时指定这个 URL，称为*事件 URL* 。

当用户在键盘上按下一个数字时，您可以通过 DTMF ( *双音多频*)收集它。每当从用户那里收集到一个 DTMF 输入时，它就会被发送到你的应用程序中的一个不同的 webhook URL，你也必须指定这个 URL。

所以让我们开始编写这个 webhook 服务器吧！我将使用 [express](https://expressjs.com/) 作为 web 应用框架，所以我需要安装它。我需要处理 JSON 主体，所以我也要安装`body-parser`。在终端的项目文件夹中运行以下命令:

```
npm install express body-parser 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在主文件夹中，创建一个名为`index.js`的新文件，并使用`body-parser`添加一个样板文件`express`服务器，该服务器监听端口 3000。例如:

```
const app = require('express')()
const bodyParser = require('body-parser')

app.use(bodyParser.json())

app.listen(3000) 
```

Enter fullscreen mode Exit fullscreen mode

## 接到电话

我需要创建*应答 URL* ，这是 Nexmo 将要发出`GET`请求的地方，它期望接收 [Nexmo 呼叫控制对象](https://developer.nexmo.com/api/voice/ncco)，简称 NCCO。这没什么特别的，一个带有一组预定义动作对象的 JSON 对象。我们将使用`talk`动作来问候呼叫者，并要求他们按一个数字，将`bargeIn`选项设置为`true`，这样用户就可以输入一个数字，而不必等待语音消息结束。我们将为 NCCO 添加一个`input`,以便通过 DTMF 捕获数字。将`maxDigits`属性设置为 1，将`eventURL`设置为服务器上的处理程序，以接收和处理输入。为了实现这一切，您可以将下面的代码添加到您的`index.js`文件中:

```
app.get('/webhooks/answer', (req, res) => {
  const ncco = [{
      action: 'talk',
      bargeIn: true,
      text: 'Hello. Please enter a digit.'
    },
    {
      action: 'input',
      maxDigits: 1,
      eventUrl: [`${req.protocol}://${req.get('host')}/webhooks/dtmf`]
    }
  ]

  res.json(ncco)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 处理用户输入

让我们在`index.js`中添加处理输入 DTMF 的代码。Nexmo 向我们的 webhook 发出一个`POST`请求，我们将在`webhooks/dtmf`公开这个请求。当我们收到请求时，我们将创建另一个`talk`动作来检查请求对象并读回调用者按下的数字:

```
app.post('/webhooks/dtmf', (req, res) => {
  const ncco = [{
    action: 'talk',
    text: `You pressed ${req.body.dtmf}`
  }]

  res.json(ncco)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 记录通话事件

我们需要在应用程序中创建另一条`POST`路线，以记录来自 Nexmo 的所有呼叫相关事件。将以下代码添加到您的`index.js`文件中:

```
app.post('/webhooks/events', (req, res) => {
  console.log(req.body)
  res.send(200);
}) 
```

Enter fullscreen mode Exit fullscreen mode

作为参考，你最终的`index.js`文件应该类似于[这个](https://github.com/Nexmo/nexmo-node-code-snippets/blob/master/voice/ivr-menu.js)。

现在，您已经设置好并准备好运行代码了，您可以通过在终端中输入以下命令来运行代码:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

这将启动一个服务器，并通过您的`index.js`文件将任何流量路由到`http://localhost:3000`。

## 用 ngrok 暴露你的 app

为了允许 Nexmo 向您的应用程序发出请求，您需要向外界公开运行在本地机器上的代码。

ngrok 是我们为此选择的工具，我们提供了对工具的极好的[介绍，如果您以前没有使用过它，您可以阅读以快速上手。](https://www.nexmo.com/blog/2017/07/04/local-development-nexmo-ngrok-tunnel-dr/)

一旦你安装了 ngrok，运行`ngrok http 3000`将你的应用程序暴露在互联网上。您需要记下生成的`ngrok` URL，因为我们需要在下一步将它提供给 Nexmo(它看起来有点像`http://e83658ff.ngrok.io`)。我以后称之为`YOUR_NGROK_URL`。

## 买个号，创建个 app

随着服务器的运行并向全世界开放，我们现在需要获得一个 Nexmo 电话号码，并将这个代码链接到它，这个代码将在本地运行。

让我们从通过 Nexmo CLI 购买一个号码开始:

```
nexmo number:buy  --country_code US 
```

Enter fullscreen mode Exit fullscreen mode

如果愿意，您可以使用不同的国家代码。记下您购买的号码，因为我们在下一步中会用到它。

我们现在需要创建一个 [Nexmo 应用程序](https://developer.nexmo.com/concepts/guides/applications)，它是您的应用程序所需的所有设置的容器。我们需要指定*答案 URL* 和*事件 URL* ，以便 Nexmo 可以与我们创建的服务器进行交互。

使用 Nexmo CLI 创建您的应用程序，确保用 ngrok 之前给您的自己生成的 URL 来替换`YOUR_NGROK_URL`:

```
nexmo app:create "IVR Menu" YOUR_NGROK_URL/webhooks/answer YOUR_NGROK_URL/webhooks/events 
```

Enter fullscreen mode Exit fullscreen mode

您将得到的响应将包含一个巨大的私钥输出，除此之外，还有一个应用程序 ID。您可以忽略私钥，因为它不是处理呼入电话所必需的。记下应用程序 ID(如下所示:`aaaaaaaa-bbbb-cccc-dddd-abcdef123456`)。

我们有一个连接到服务器的应用程序和一个 Nexmo 电话号码，但是电话号码没有连接到应用程序。因此，我们需要将我们刚刚购买的号码链接到我们刚刚创建的应用程序。您可以使用 Nexmo CLI 发出以下命令，替换`YOUR_NEXMO_NUMBER`和`YOUR_APPLICATION_ID` :

```
nexmo link:app YOUR_NEXMO_NUMBER YOUR_APPLICATION_ID 
```

Enter fullscreen mode Exit fullscreen mode

这就是将上述代码与您的 Nexmo 应用程序和编号相关联所需的全部内容。您可以通过拨打您购买的号码并按下键盘上的数字来测试它！

## 结论

在大约 30 行 JavaScript 代码中，您现在拥有了一个具有交互式语音响应菜单的应用程序。你怎么能从这里开始扩展呢？

如果您想了解更多有关呼入语音呼叫的功能，以及如何通过添加录音或将来电者连接到您的手机等功能使其更加复杂，您可以在 [NCCO 参考](https://developer.nexmo.com/api/voice/ncco)中了解更多有关这些操作的信息。

一如既往，如果你对这篇文章有任何问题，欢迎在 Twitter 上给我发消息，我是 [@lakatos88](https://twitter.com/lakatos88) 。您也可以给 Nexmo、【devrel@nexmo.com】T3、[的开发者关系团队发电子邮件，或者加入 Nexmo 社区 Slack 频道 T5，我们随时恭候您的光临。](https://developer.nexmo.com/community/slack)

## 开发到的额外内容

我想更进一步。[我过去做过一些很傻的东西](https://dev.to/lakatos88/puns-dev-or-how-i-got-internet-famous-for-like-2-seconds-1jo2)，所以我想为什么不把这个交互式语音应答做得有点傻，同时继续探索进一步构建它的概念。我想给输入添加更多的数字，并让它根据输入向 API 请求数据。因为我在这里和数字打交道，而且我见过[惠晶使用 NumbersAPI](https://dev.to/nexmo/build-an-sms-bot-on-glitch-with-hapijs-and-the-nexmo-messages-api-25ha) ，我想我也应该试一试。我们还将使交互式语音响应递归，这样您可以在每个事实后继续发送 DTMF 并获得另一个，直到您受够了并挂断电话。

### 更改答案网址

我觉得一个数字会限制 NumbersAPI，很快就会变得令人厌烦，所以让我们将`input`动作的`maxDigits`属性改为 Nexmo 允许的最大值，即 20。因为我们允许这么多数字，默认的`timeOut`3 是不够的，所以让我们添加 10 秒的超时，这是 Nexmo 允许的最大值。有了这么大的超时，我们还应该添加一个替代提交方法，这样我们就不必每次都等待 10 秒钟。`submitOnHash`应该会成功，所以 DTMF 会在 10 秒后或用户按下`#`键后提交。

```
app.get('/webhooks/answer', (req, res) => {
  const ncco = [{
      action: 'talk',
      voiceName: 'Chipmunk',
      bargeIn: true,
      text: '<speak>Hello. Please enter a number between 0 and <prosody rate="fast">99999999999999999999</prosody> followed by the # key.</speak>'
    },
    {
      action: 'input',
      maxDigits: 20,
      timeOut: 10,
      submitOnHash: true,
      eventUrl: [`${req.protocol}://${req.get('host')}/webhooks/dtmf`]
    }
  ]

  res.json(ncco)
}) 
```

Enter fullscreen mode Exit fullscreen mode

因为我改变了`input`这么多，我想我也应该改变`talk`动作，以反映输入参数。我加了一个`voiceName`只是为了好玩，花栗鼠是我的最爱。有很多你可以使用的 Nexmo，取决于你想要的语言和角色，你可以在[文档](https://developer.nexmo.com/voice/voice-api/guides/text-to-speech#voice-names)中查看它们。当我测试它说出最大的 20 位数`99999999999999999999`时，花费了太多时间，所以我需要将文本从普通文本转换成 SSML 或*语音合成标记语言*。有了 SSML，你可以混合多种语言，控制合成文本的速度、音量和音高，控制单词和数字的发音。在这里，我用它来改变在 SSML 使用`<prosody>`标签的大数字的语速。

### 更改 DTMF webhook

现在我们改变了答案 webhook，我们可以接受一个 20 位数的数字。让我们将它连接到 [NumbersAPI](http://numbersapi.com/) ，获得关于该数字的随机事实，然后将其添加回 NCCO 的`talk`动作。我们还将添加我们在 answer webhook 中使用的相同的`input`动作，这样呼叫继续进行，您可以输入另一个数字来获得更多关于它的随机事实。

首先，我们需要安装一个 http 请求库，我不喜欢 Node.js 中默认的 HTTP 请求库。巧合的是，它被称为`request`，所以让我们通过`npm` :
来安装它

```
npm install request 
```

Enter fullscreen mode Exit fullscreen mode

每次在`/webhooks/dtmf`端点上有 POST 请求时，我们都会向`http://numbersapi.com/${number}`发出请求，其中`number`将是来自 Nexmo 的 DTMF 号码。当用户在超时前没有输入任何内容时，我们需要对它进行清理，以防它是空的。我会默认为 42 而不是 0，因为 42 就是生命的意义。我们将把自己的消息附加到 Numbers API 返回的消息中，告诉用户再次输入一个数字或者挂断电话。因为这是两个不同的来源，我想在电话中说得更清楚，所以我再次使用 SSML，而不是简单的讲话。我将使用`<s>`或句子标签来分隔消息，这使得两条消息之间出现停顿。

```
app.post('/webhooks/dtmf', (req, res) => {
  let number = req.body.dtmf || 42;
  let message = "";

  request(`http://numbersapi.com/${number}`, (error, response, body) => {
    if (error) {
      message = "The Numbers API has thrown an error."
    } else {
      message = body
    }

    const ncco = [{
        action: 'talk',
        bargeIn: true,
        voiceName: 'Chipmunk',
        text: `<speak><s>${message}</s> <s>Enter another number if you want to continue or just hang up the call if you've had enough.</s></speak>`
      },
      {
        action: 'input',
        maxDigits: 20,
        timeOut: 10,
        submitOnHash: true,
        eventUrl: [`${req.protocol}://${req.get('host')}/webhooks/dtmf`]
      }
    ]

    res.json(ncco)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 试试看

作为参考，你最终的`index.js`文件应该看起来像[这个](https://github.com/AlexLakatos/nexmo-ivr-menu/blob/master/index.js)。如果您已经遵循了这么长时间，您将需要通过在您的终端中再次运行`node index.js`来重启您的服务器，并且您已经准备好了。拨打您的 Nexmo 号码，开始与您的键盘互动。