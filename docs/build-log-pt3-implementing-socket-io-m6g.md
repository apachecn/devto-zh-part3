# 构建日志:情感-聊天 pt3 |实现 socket.io

> 原文：<https://dev.to/tttaaannnggg/build-log-pt3-implementing-socket-io-m6g>

这与整个项目没多大关系，更多的是让 [socket.io](https://socket.io/) 从我的客户端与服务器通信。这将是一个非常接近，鼻子到磨刀石类型的交易。

现有的大多数指南会告诉您如何发送和接收数据，但并不清楚什么去哪里，所以我将在我们进行项目时提供过多的代码片段。

我们正在使用 Node/Express 后端，它的设置有点特殊。下面是在我的服务器上用 Express 设置 socketio 的样子。

## **服务器**

```
const express = require('express');
const app = express();
const server = require ('http').createServer(app);
const io = require('socket.io')(server);
//...
server.listen(3000, ()=>{console.log('listening on 3000'}) 
```

快速/“应用程序”设置的工作方式类似于任何其他节点/快速设置。然而，接下来的两行变得有点奇怪。我不完全确定它们是如何工作的，但似乎我们正在创建某种 http 服务器，给它我们的`app`作为参数，并创建某种 socketio 连接，给它`server`作为参数。

它不是在我们的`app`上调用`listen`作为一个方法，而是在我们的`server`上调用，我想这是让我们除了正常的服务器功能之外还可以使用套接字连接的原因。

我会假设你知道如何做平常的事情。发球路线等等。所以我们跳过这部分。相反，现在我们已经得到了`io`的东西，我们将倾听其中的联系。

```
const express = require('express');
const app = express();
const server = require ('http').createServer(app);
const io = require('socket.io')(server);

io.on('connection', (socket)=>{
  console.log('user connected');
})

server.listen(3000, ()=>{console.log('listening on 3000'}) 
```

## **客户端**

目前，这是服务器。我们来看看客户。

在我们的`index.html`中，我们需要添加以下标签:`<script src="/socket.io/socket.io.js"></script>`。当然，这是一个相对文件路径。我们不自己设置它，而是通过在我们的服务器中使用 socket.io 提供给我们。

有了这个脚本标签，我们就可以开始在前端进行连接了。忽略任何其他种类的前端功能，让我们想象一下，我们实际上只有一个连接到我们的服务器的 javscript 文件。看起来应该是这样的:

```
const socket = io() 
```

`io`由我们插入的脚本标签提供给我们。

现在，当我们启动服务器并在浏览器中加载页面时，它将触发`io.on('connection', ...)`监听器并将`'user connected'`记录到服务器的控制台。

现在我们已经建立了一个连接，并确认了客户机和服务器正在通信，我们需要监听其他事件。

## **服务器**(再次)

所以，让我们回到服务器。我们正在监听一个连接，一旦连接建立，就发出一个回调。

```
const express = require('express');
const app = express();
const server = require ('http').createServer(app);
const io = require('socket.io')(server);

io.on('connection', (socket)=>{
  console.log('user connected');
})

server.listen(3000, ()=>{console.log('listening on 3000'}) 
```

让我感到困惑的是，我认为我们可以再添加一个`io.on`，类似于`'connection'`的事件监听器。然而，我们实际上需要在我们的`io.on('connection')`的回调中添加其他事件监听器。

您可能已经注意到，我们在回调函数中采用了一个名为`socket`的参数。这实际上为我们提供了一个带有`on`方法的对象，我们将使用它来创建另一个事件监听器。我们可以给我们的听众起任何我们想要的名字。我们称它为`'hello'`。

```
const express = require('express');
const app = express();
const server = require ('http').createServer(app);
const io = require('socket.io')(server);

io.on('connection', (socket)=>{
  console.log('user connected');
  socket.on('hello', (data)=>{
    console.log('data received:', data)
  }
})

server.listen(3000, ()=>{console.log('listening on 3000'}) 
```

与`io.on`类似，`socket.on`需要两个参数。第一个是对应于要监听的事件的字符串。第二个是回调函数。在这种情况下，我们希望回调得到一些我们将从前端发送的数据。

现在，让我们回到前端。

## **前端**(再次)

这是我们停下来的地方。`io`打开一个到我们后端的连接，它位于`socket`对象上。

```
const socket = io(); 
```

让我们做一个小小的你好世界。从前端发送数据相当容易。现在我们有了`socket`，我们可以这样做:

```
const socket = io();

socket.emit('hello', 'hi from the frontend!'); 
```

`socket.emit`的第一个参数当然是事件的名称。我们的后端正在监听一个`'hello'`，所以我们的前端正在发送那个`'hello'`。第二个参数是我们发出的数据，对应于事件。

 **所以，当我们的前端开火时

```
socket.emit('hello', 'hi from the frontend!'); 
```

我们的后端在这里捕捉到了:

```
io.on('connection', (socket)=>{
  console.log('user connected');
  socket.on('hello', (data)=>{
    console.log('data received:', data)
  }
} 
```

我们的服务器接收到`'hello'`事件，并在我们的回调函数中将`'hi from the frontend'`作为`data`，最后，将`data received: 'hi from the frontend'`记录到控制台。

有了它，我们可以从客户端向服务器发送消息。接下来，我们需要让我们的客户端监听来自服务器的消息。我希望明天就能完成，然后这周剩下的时间做 UI/UX 的事情。感谢您的阅读！**