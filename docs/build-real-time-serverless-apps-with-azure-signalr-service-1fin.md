# 使用 Azure SignalR 服务构建实时无服务器应用

> 原文：<https://dev.to/azure/build-real-time-serverless-apps-with-azure-signalr-service-1fin>

今天，微软[宣布](https://azure.microsoft.com/en-us/blog/real-time-serverless-applications-with-the-signalr-service-bindings-in-azure-functions/?WT.mc_id=devto-blog-antchu)Azure SignalR 服务绑定 Azure 功能的全面上市。绑定允许 Azure 功能与 SignalR 服务无缝集成，通过 WebSockets 大规模广播实时消息。

我们将看看如何使用 HTML 和 JavaScript 构建一个基于浏览器的协作绘图应用程序。多人可以在自己的浏览器中打开应用程序，并在画布上绘图。使用 Azure 函数和 SignalR 服务实时同步更改。

Azure 函数中的 Java 语言支持最近也变得[普遍可用](https://azure.microsoft.com/en-us/blog/announcing-the-general-availability-of-java-support-in-azure-functions/?WT.mc_id=devto-blog-antchu)。在本文中，我们将使用 Java 编写函数，但是 SignalR 服务绑定也适用于 Azure 函数支持的其他语言。查看 [GitHub repo](https://github.com/anthonychu/serverless-draw) 获取 Java、C#和 JavaScript 的源代码。

## 概述

该应用程序有三个主要组件:绘图画布、Azure 功能应用程序和 Azure SignalR 服务。

[![Architecture](img/1f3472a03cce47f7a9816b5142787397.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LfVrSMgH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/49mhwtjpunxh01n4z5hw.png)

1.  画布是一个运行在浏览器中的简单 JavaScript 应用程序。当应用程序启动时，它从一个名为“negotiate”的 Azure 函数中检索 SignalR 服务连接端点和访问令牌。

2.  该应用程序连接到 SignalR 服务，并建立实时通道。它通常是一个 WebSocket 连接，但是如果 WebSocket 对于一个给定的连接不可用，SignalR 将自动退回到其他传输。

3.  当在画布上画线时，应用程序会向一个名为“draw”的 Azure 函数发送一系列笔画。

4.  draw Azure 函数使用 SignalR 服务向所有连接的客户端广播一系列笔画。

## 创建 Azure SignalR 服务实例

Azure SignalR 服务是一个完全托管的实时消息平台，我们的应用程序将使用它来广播消息。我们可以在 Azure 门户中创建一个免费的服务实例，或者运行下面的 Azure CLI 命令。

```
az signalr create -n $SIGNALR_NAME -g $GROUP_NAME --sku Free_DS2 -l westus 
```

Enter fullscreen mode Exit fullscreen mode

创建实例后，我们需要获取函数应用程序将使用的连接字符串。

```
az signalr key list -n $SIGNALR_NAME -g $GROUP_NAME 
```

Enter fullscreen mode Exit fullscreen mode

## 打造 Azure 功能 app

Azure Function app 由两个关键功能组成。第一个是返回 SignalR 服务连接信息的`negotiate`函数。第二个是一个`draw`函数，每当在任何画布上画线时都会调用它；它会将这些线广播到其他画布上。

> 如果你以前没有使用过 Azure 函数，可以看看它们的[快速入门](https://docs.microsoft.com/en-us/azure/azure-functions/?WT.mc_id=devto-blog-antchu)。

### 协商功能

这个 HTTP 触发的函数使用`SignalRConnectionInfoInput`绑定来为客户端生成访问令牌和端点信息。绑定使用连接字符串生成此信息。

```
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
    @HttpTrigger(
      name = "req",
      methods = { HttpMethod.POST },
      authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
    @SignalRConnectionInfoInput(
      name = "connectionInfo",
      hubName = "serverlessdraw") SignalRConnectionInfo connectionInfo) {
  return connectionInfo;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 绘制功能

`draw` HTTP 触发函数使用`SignalROutput`绑定将画布上绘制的笔画传播到当前连接到 SignalR 服务的所有画布。

它只是将 HTTP 请求体的内容转发给 SignalR 服务。

```
@FunctionName("draw")
public void draw(
    @HttpTrigger(
      name = "req",
      methods = { HttpMethod.POST },
      authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<StrokeCollection> req,
    @SignalROutput(
      name = "signalRMessage",
      hubName = "serverlessdraw") OutputBinding<SignalRMessage> signalRMessage) {

  StrokeCollection strokeCollection = req.getBody();
  SignalRMessage msg = new SignalRMessage("newStrokes", strokeCollection);
  signalRMessage.setValue(msg);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 创建绘图画布

canvas 是一个非常简单的基于浏览器的 JavaScript 应用程序，它使用 HTML canvas。你可以在我的 Twitch stream 上观看我构建它的第一个版本。

### 信号员服务连接

当 canvas 应用程序启动时，我们做的第一件事是连接到 SignalR 服务。我们可以使用 CDN 引用 SignalR JavaScript 客户端。

```
<script src="https://cdn.jsdelivr.net/npm/@aspnet/signalr@1.1.2/dist/browser/signalr.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以使用客户端创建到 SignalR 服务的连接。按照惯例，SignalR 客户端将在 URL 的末尾追加`/negotiate`来发现连接协商端点，因此我们在初始化`HubConnectionBuilder`时将函数 URL 的这一部分去掉。

```
var connection = new signalR.HubConnectionBuilder()
  .withUrl(`${apiBaseUrl}/api`) // function URL minus /negotiate
  .build()

// set up event listeners
connection.on('newStrokes', drawStrokes)
connection.on('clearCanvas', clearCanvas)

connection.start()
  .then(() => console.log('connected')) 
```

Enter fullscreen mode Exit fullscreen mode

在配置连接和启动连接之间，我们设置了几个事件监听器。当来自 SignalR 服务的事件与其中一个名称匹配时，将调用事件处理程序。下面是`clearCanvas`函数的样子:

```
function clearCanvas() {
  ctx.clearRect(0, 0, canvas.width, canvas.height)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 绘制画布

主画布是(你猜对了)一个单独的`<canvas>`标签:

```
<canvas id="draw-canvas" height="500" width="800"></canvas> 
```

Enter fullscreen mode Exit fullscreen mode

主 JavaScript 函数被命名为`mouseMove`。每画一笔就调用一次。我们算出线的起点和终点，并把它画在画布上。然后，我们将笔画添加到一个数组中，这样我们就可以收集一些笔画，并成批发送它们。

```
function mouseMove(ev) {

  // 🌟 math happens here

  drawStroke(start, end, colorButton.value)
  unsentStrokes.push({
    start: start,
    end: end,
    color: colorButton.value
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

每隔 250 毫秒左右，我们将一批笔画发送到我们的`draw` Azure 函数。

```
setInterval(function () {
  if (unsentStrokes.length) {
    axios.post(`${apiBaseUrl}/api/draw`, {
      sender: clientId,
      strokes: unsentStrokes
    })
    unsentStrokes = []
  }
}, 250) 
```

Enter fullscreen mode Exit fullscreen mode

draw 函数将使用 SignalR 服务在我们应用程序的每个运行实例中引发`newStrokes`事件。这将调用名为`drawStrokes`的函数来绘制笔画。

成品看起来是这样的:

[![Serverless draw app](img/6b77804348fd35880cd2400e1e850daf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G_3StrWt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/08tjfk0nvynqjlz0l0s9.gif)

查看 [GitHub repo](https://github.com/anthonychu/serverless-draw) 获取 Java、JavaScript 和 C#版本的函数应用的源代码。

## 资源

*   用于 Azure 功能的 Azure SignalR 服务绑定正式发布
*   [宣布 Azure 函数中 Java 支持的普遍可用性](https://azure.microsoft.com/en-us/blog/announcing-the-general-availability-of-java-support-in-azure-functions/?WT.mc_id=devto-blog-antchu)
*   【Azure 函数引用的绑定
*   [信号服务无服务器编程指南](https://docs.microsoft.com/en-us/azure/azure-signalr/signalr-overview-azure-functions?WT.mc_id=devto-blog-antchu)
*   [无服务器绘制源代码](https://github.com/anthonychu/serverless-draw)