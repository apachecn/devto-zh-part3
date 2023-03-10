# WebSockets 教程:如何实时处理 Node 和 React

> 原文：<https://dev.to/bnevilleoneill/websockets-tutorial-how-to-go-real-time-with-node-and-react-239b>

[![](img/883a9df5be1369eb2ae7dc0f6592d18f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3gngIP91--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aqu8smzbhsJibnm1jgArhbg.jpeg)

web 已经走过了漫长的道路来支持客户机和服务器之间的全双工(或双向)通信。这是 WebSocket 协议的主要目的:通过一个 TCP 套接字连接在客户机和服务器之间提供持久的实时通信。

WebSocket 协议只有两个目的:1)建立握手，2)帮助数据传输。一旦服务器和客户机握手，它们就可以随意地以较少的开销互相发送数据。

WebSocket 通信通过使用 WS(端口 80)或 WSS(端口 443)协议的单个 TCP 套接字进行。除了 Opera Mini 之外，几乎所有的浏览器在我写作的时候都为 WebSockets 提供了令人钦佩的支持。

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 故事到此为止

历史上，创建需要实时数据的 web 应用程序(如游戏或聊天应用程序)需要滥用 HTTP 协议来建立双向数据传输。有多种方法用于实现实时功能，但没有一种方法像 WebSockets 那样高效。HTTP 轮询、HTTP 流、Comet、SSE——它们都有各自的缺点。

#### HTTP 轮询

解决这个问题的第一个尝试是定期轮询服务器。HTTP 长轮询生命周期如下:

1.  客户端发出请求，并一直等待响应。
2.  服务器会推迟响应，直到发生更改、更新或超时。该请求保持“挂起”状态，直到服务器有东西返回给客户机。
3.  当服务器端发生一些变化或更新时，它会向客户端发回一个响应。
4.  客户端发送一个新的长轮询请求来监听下一组更改。

长轮询有很多漏洞——报头开销、延迟、超时、缓存等等。

#### HTTP 流媒体

这种机制免除了网络延迟的痛苦，因为初始请求是无限期开放的。即使在服务器推送数据之后，请求也不会终止。HTTP 流的前三种生命周期方法在 HTTP 轮询中是相同的。

然而，当响应被发送回客户机时，请求永远不会终止；服务器保持连接打开，并在有变化时发送新的更新。

#### **【服务器发送事件(SSE)**

使用 SSE，服务器将数据推送到客户端。聊天或游戏应用程序不能完全依赖 SSE。SSE 的完美用例是，例如，脸书新闻提要:每当有新的帖子进来，服务器就把它们推到时间线上。SSE 通过传统的 HTTP 发送，并且对打开的连接数有限制。

这些方法不仅效率低下，而且其中的代码也让开发人员疲惫不堪。

### 为什么 WebSocket 是被许诺的王子

WebSockets 旨在取代现有的双向通信技术。当涉及全双工实时通信时，上述现有方法既不可靠也不高效。

WebSockets 类似于 SSE，但同样成功地将消息从客户端传回服务器。连接限制不再是问题，因为数据是通过单个 TCP 套接字连接提供的。

### 实用教程

正如介绍中提到的，WebSocket 协议只有两个议程。让我们看看 WebSockets 如何实现这些议程。为此，我将剥离 Node.js 服务器，并将其连接到用 React.js 构建的客户端。

### **议程 1: WebSocket 在服务器和客户端之间建立握手**

#### **在服务器级创建握手**

我们可以利用单个端口来分离 HTTP 服务器和 WebSocket 服务器。下面的要点显示了一个简单的 HTTP 服务器的创建。一旦它被创建，我们将 WebSocket 服务器绑定到 HTTP 端口:

```
const webSocketsServerPort = 8000;
const webSocketServer = require('websocket').server;
const http = require('http');
// Spinning the http server and the websocket server.
const server = http.createServer();
server.listen(webSocketsServerPort);
const wsServer = new webSocketServer({
  httpServer: server
}); 
```

Enter fullscreen mode Exit fullscreen mode

一旦创建了 WebSocket 服务器，我们需要在收到来自客户端的请求时接受握手。我在代码中将所有连接的客户机作为一个对象来维护，并在从浏览器接收到它们的请求时使用一个惟一的用户 id。

```
// I'm maintaining all active connections in this object
const clients = {};

// This code generates unique userid for everyuser.
const getUniqueID = () => {
  const s4 = () => Math.floor((1 + Math.random()) * 0x10000).toString(16).substring(1);
  return s4() + s4() + '-' + s4();
};

wsServer.on('request', function(request) {
  var userID = getUniqueID();
  console.log((new Date()) + ' Recieved a new connection from origin ' + request.origin + '.');
  // You can rewrite this part of the code to accept only the requests from allowed origin
  const connection = request.accept(null, request.origin);
  clients[userID] = connection;
  console.log('connected: ' + userID + ' in ' + Object.getOwnPropertyNames(clients))
}); 
```

Enter fullscreen mode Exit fullscreen mode

***那么，连接被接受后会发生什么？*T3】**

当发送常规 HTTP 请求以建立连接时，在请求头中，客户端发送*Sec-WebSocket-Key*。服务器对这个值进行编码和散列，并添加一个预定义的 GUID。它在服务器发送的握手中回显*Sec-WebSocket-Accept*中生成的值。

一旦服务器接受了请求(在产品中进行必要的验证之后)，握手就完成了，状态代码为 101。如果您在浏览器中看到除状态代码 101 之外的任何内容，则 WebSocket 升级失败，将遵循正常的 HTTP 语义。

*Sec-WebSocket-Accept*头字段指示服务器是否愿意接受连接。此外，如果响应缺少*Upgrade*头字段，或者*Upgrade*不等于 websocket，则意味着 WebSocket 连接失败。

成功的服务器握手如下:

```
HTTP GET ws://127.0.0.1:8000/ 101 Switching Protocols
Connection: Upgrade
Sec-WebSocket-Accept: Nn/XHq0wK1oO5RTtriEWwR4F7Zw=
Upgrade: websocket 
```

Enter fullscreen mode Exit fullscreen mode

#### 在客户端创建握手

在客户端级别，我使用与我们在服务器中使用的相同的 WebSocket 包来建立与服务器的连接(Web IDL 中的 WebSocket API 正在由 W3C 进行标准化)。一旦请求被服务器接受，我们将在浏览器控制台上看到 WebSocket 客户端连接。

下面是创建到服务器的连接的初始框架:

```
import React, { Component } from 'react';
import { w3cwebsocket as W3CWebSocket } from "websocket";

const client = new W3CWebSocket('ws://127.0.0.1:8000');

class App extends Component {
  componentWillMount() {
    client.onopen = () => {
      console.log('WebSocket Client Connected');
    };
    client.onmessage = (message) => {
      console.log(message);
    };
  }

  render() {
    return (
      <div>
        Practical Intro To WebSockets.
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

客户端发送以下报头来建立握手:

```
HTTP GET ws://127.0.0.1:8000/ 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: vISxbQhM64Vzcr/CD7WHnw==
Origin: http://localhost:3000
Sec-WebSocket-Version: 13 
```

Enter fullscreen mode Exit fullscreen mode

现在客户机和服务器通过相互握手连接起来，WebSocket 连接可以在接收消息的同时传输消息，从而实现了 WebSocket 协议的第二个议程。

### 议程二:实时消息传输

<figure>[![](img/d29c9d71da4d77853e7165f1e740f05c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--29udi5do--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANzx8FJa3bTZSri9BED3I1g.gif) 

<figcaption>实时流媒体内容修改。</figcaption>

</figure>

我将编写一个基本的实时文档编辑器，用户可以在这里一起编辑文档。我在追踪两件事:

1.  **用户活动:**每次有用户加入或离开，我都会将消息广播给所有其他连接的客户端。
2.  **内容更改:**每次编辑器中的内容发生更改时，都会广播到所有其他连接的客户端。

该协议允许我们以二进制数据或 UTF-8 的形式发送和接收消息(注意，传输和转换 UTF-8 的开销较小)。

只要我们很好地理解了套接字事件:onopen、onclose 和 onmessage，理解和实现 WebSockets 就非常容易。客户端和服务器端的术语是相同的。

#### **在客户端发送和监听消息**

在客户端，当新用户加入或内容发生变化时，我们使用 client.send 向服务器发送一条消息，将新信息发送到服务器。

```
const webSocketsServerPort = 8000;
const webSocketServer = require('websocket').server;
const http = require('http');
// Spinning the http server and the websocket server.
const server = http.createServer();
server.listen(webSocketsServerPort);
const wsServer = new webSocketServer({
  httpServer: server
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们跟踪的事件:用户加入和内容改变。

并且监听来自服务器的消息非常简单:

```
componentWillMount() {
  client.onopen = () => {
   console.log('WebSocket Client Connected');
  };
  client.onmessage = (message) => {
    const dataFromServer = JSON.parse(message.data);
    const stateToChange = {};
    if (dataFromServer.type === "userevent") {
      stateToChange.currentUsers = Object.values(dataFromServer.data.users);
    } else if (dataFromServer.type === "contentchange") {
      stateToChange.text = dataFromServer.data.editorContent || contentDefaultMessage;
    }
    stateToChange.userActivity = dataFromServer.data.userActivity;
    this.setState({
      ...stateToChange
    });
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 在服务器端发送和监听消息

在服务器中，我们只需捕获传入的消息，并将其广播给连接到 WebSocket 的所有客户端。这就是臭名昭著的 Socket。IO 和 WebSocket:使用 WebSockets 时，我们需要手动将消息发送给所有客户端。插座。IO 是一个成熟的库，所以它可以自己处理。

```
// I'm maintaining all active connections in this object
const clients = {};

// This code generates unique userid for everyuser.
const getUniqueID = () => {
  const s4 = () => Math.floor((1 + Math.random()) * 0x10000).toString(16).substring(1);
  return s4() + s4() + '-' + s4();
};

wsServer.on('request', function(request) {
  var userID = getUniqueID();
  console.log((new Date()) + ' Recieved a new connection from origin ' + request.origin + '.');
  // You can rewrite this part of the code to accept only the requests from allowed origin
  const connection = request.accept(null, request.origin);
  clients[userID] = connection;
  console.log('connected: ' + userID + ' in ' + Object.getOwnPropertyNames(clients))
}); 
```

Enter fullscreen mode Exit fullscreen mode

向所有连接的客户端广播消息。

[![](img/ac8d88b017a4d0d7b86cacc0b3ae5b00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m3lqVe2b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMqOjfmnuia8J3MGXsjF_vA.gif)

#### 浏览器关闭后会发生什么？

在这种情况下，WebSocket 调用 close 事件，这允许我们编写逻辑来终止当前用户的连接。在我的代码中，当一个用户离开文档时，我向其余用户广播一条消息:

```
connection.on('close', function(connection) {
    console.log((new Date()) + " Peer " + userID + " disconnected.");
    const json = { type: typesDef.USER_EVENT };
    userActivity.push(`${users[userID].username} left the document`);
    json.data = { users, userActivity };
    delete clients[userID];
    delete users[userID];
    sendMessage(JSON.stringify(json));
  }); 
```

Enter fullscreen mode Exit fullscreen mode

这个应用程序的源代码在我在 GitHub 上的 [repo](https://github.com/AvanthikaMeenakshi/node-websockets) 中。

### 结论

WebSockets 是在应用程序中实现实时功能的最有趣和最方便的方式之一。它为我们利用全双工通信提供了很大的灵活性。我强烈建议在试用 Socket 之前先使用 WebSockets。IO 和其他可用的库。

编码快乐！:)

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子 [WebSockets 教程:如何实时处理 Node 和 React](https://blog.logrocket.com/websockets-tutorial-how-to-go-real-time-with-node-and-react-8e4693fbf843/) 最先出现在[日志博客](https://blog.logrocket.com)上。