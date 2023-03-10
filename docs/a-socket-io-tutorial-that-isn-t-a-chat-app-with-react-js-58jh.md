# 非聊天应用的 Socket.io 教程(带 React.js)

> 原文：<https://dev.to/captainpandaz/a-socket-io-tutorial-that-isn-t-a-chat-app-with-react-js-58jh>

# 一个*不是*聊天 app 的 Socket.io 教程(附 React.js)

最近，一位朋友向我咨询如何在仪表板上实时(或尽可能接近实时)显示传感器数据。具体来说，他将从一辆赛车中读取 OBD2 数据，并希望向外部系统显示一些数据点。在考虑了像卡夫卡这样的消息总线之后，我们认为它远远超过了 kill。我推荐使用 websockets，特别是它们的 Socket.io 实现。公平地说，我从来没有使用过 Socket.io 库，我只是读到过它们，而且我很难找到如何在聊天应用程序或多人游戏之外使用它们的信息。对我来说，将在 [Socket.io](https://socket.io/get-started/chat/) 上找到的聊天室应用程序教程翻译成不同的用例是一个过程，所以我决定写下这个小演示，它可能对以不同方式思考套接字的人有用。

## 第 3 层

下面是我们想出的超高级设计:
[![I Like Memes](img/27b56f552898c727f7051673345d9082.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g0TIft_3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mw9b6z27mfttcelju3cv.png)

对于我们的原型，我们将模拟传感器/阅读器，设置一个后端服务器作为通道，并实现一个前端消费者。

## 后端

我们需要的两个包是:

*   [快递](https://www.npmjs.com/package/express)
*   [socket.io](https://www.npmjs.com/package/socket.io)

```
const express = require("express");
const http = require("http");
const socketIo = require("socket.io");

//Port from environment variable or default - 4001
const port = process.env.PORT || 4001;

//Setting up express and adding socketIo middleware
const app = express();
const server = http.createServer(app);
const io = socketIo(server);

//Setting up a socket with the namespace "connection" for new sockets
io.on("connection", socket => {
    console.log("New client connected");

    //Here we listen on a new namespace called "incoming data"
    socket.on("incoming data", (data)=>{
        //Here we broadcast it out to all other sockets EXCLUDING the socket which sent us the data
       socket.broadcast.emit("outgoing data", {num: data});
    });

    //A special namespace "disconnect" for when a client disconnects
    socket.on("disconnect", () => console.log("Client disconnected"));
});

server.listen(port, () => console.log(`Listening on port ${port}`)); 
```

让我们来分解一下:

```
const express = require("express");
const http = require("http");
const socketIo = require("socket.io");

//Port from environment variable or default - 4001
const port = process.env.PORT || 4001;

//Setting up express and adding socketIo middleware
const app = express();
const server = http.createServer(app);
const io = socketIo(server); 
```

如果你以前使用过 express，这并不是什么新鲜事。我们在这里看到的唯一与 socket.io 相关的东西是`const io = socketIo(server);`,它设置了一个新的 socket.io 服务器实例。

```
//Setting up a socket with the namespace "connection" for new sockets
io.on("connection", socket => {
    console.log("New client connected");

    //Here we listen on a new namespace called "incoming data"
    socket.on("incoming data", (data)=>{
        //Here we broadcast it out to all other sockets EXCLUDING the socket which sent us the data
       socket.broadcast.emit("outgoing data", {num: data});
    });

    //A special namespace "disconnect" for when a client disconnects
    socket.on("disconnect", () => console.log("Client disconnected"));
});

server.listen(port, () => console.log(`Listening on port ${port}`)); 
```

这里我们设置了一个名为`connection`的套接字名称空间，客户端将连接到这个名称空间。一旦建立了初始连接，我们就监听两个新的名称空间。`incoming data`和`disconnect`。第一个是我们的“生产者”或传感器/阅读器也将推送数据的地方。

在我们的回调中，我们调用`socket.broadcast.emit("outgoing data", {num: data});`。广播标志是特殊的，因为它允许我们向每个客户端发送数据，但向我们发送数据的客户端除外。将数据发送回生产者是没有意义的，所以我们在另一个名称空间`outgoing data`上广播。

您会注意到我们在推进到我们的`outgoing data`名称空间之前序列化了传入的数据。这将使我们的前端更干净，并给你一个如何在一次发射中发送多个数据点的想法。

断开名称空间是为客户机失去连接而保留的。这是做任何清理工作的好地方。例如，如果您的服务器正在跟踪连接的客户端，那么最好将客户端的状态更改为断开连接。

最后一行是设置我们的 express 应用程序开始监听。

## 模拟传感器

因为这是一个模拟，我们需要做的就是发送一些随机数据。对于原型，这是在 pure node.js 中完成的，但有许多可供 socket.io 使用的客户端库，它们肯定更适合在 Arduino 或其他连接到 OBD2 传感器的微控制器上运行。不要在这里把我烤得太硬，这只是一个演示。

在本次演示中，我将演示一个“速度”值。

我们在这里使用的唯一一个包是 [socket.io-client](https://www.npmjs.com/package/socket.io-client) 。

```
let socket = require('socket.io-client')('http://127.0.0.1:4001');

//starting speed at 0
let speed = 0;

//Simulating reading data every 100 milliseconds
setInterval(function () {
    //some sudo-randomness to change the values but not to drastically
    let nextMin = (speed-2)>0 ? speed-2 : 2;
    let nextMax = speed+5 < 140 ? speed+5 : Math.random() * (130 - 5 + 1) + 5;
    speed = Math.floor(Math.random() * (nextMax - nextMin + 1) + nextMin);

    //we emit the data. No need to JSON serialization!
    socket.emit('incoming data', speed);
}, 100); 
```

这其中的大部分应该是不言自明的，所以这一部分会很短。
`let socket = require('socket.io-client')('http://127.0.0.1:4001');`设置要使用的包。我们开始设置速度变量为 0。

向我们返回要使用的套接字连接。我们告诉它在哪里运行，在哪个端口运行。

我在这里使用`setInterval`来模拟微控制器和传感器之间每隔 100 毫秒的读请求。设置下一个速度的数学只是一种“黑客”的方式，每次稍微增加或减少速度，不允许速度超过 140 或低于 0。

`socket.emit('incoming data', speed);`是我们通过套接字发送数据的地方。我们在`incoming data`名称空间上发出数据，这个名称空间是我们在上一节中在后端设置的。

就是这样！很酷吧？

## 仪表盘

我在 React 中构建了这个，它非常简单快捷。我不打算讨论 React 的细节，因为这超出了范围。我将重点介绍如何使用套接字中的数据。话虽如此，我还是用[react-D3-速度计](https://www.npmjs.com/package/react-d3-speedometer)显示了一个速度计。我不得不说它的样子给我留下了深刻的印象！我还使用了我们在生产者上使用的相同的 [socket.io-client](https://www.npmjs.com/package/socket.io-client) 包。

下面是 React 组件:

```
import React, {Component} from "react";
import socketIOClient from "socket.io-client";
import ReactSpeedometer from "react-d3-speedometer"

class App extends Component {
    constructor() {
        super();
        this.state = {
            response: 0,
            endpoint: "http://127.0.0.1:4001"
        };
    }

    componentDidMount() {
        const {endpoint} = this.state;
        //Very simply connect to the socket
        const socket = socketIOClient(endpoint);
        //Listen for data on the "outgoing data" namespace and supply a callback for what to do when we get one. In this case, we set a state variable
        socket.on("outgoing data", data => this.setState({response: data.num}));
    }

    render() {
        const {response} = this.state;
        return (
            <div style={{textAlign: "center"}}>
                <ReactSpeedometer
                    maxValue={140}
                    value={response}
                    needleColor="black"
                    startColor="orange"
                    segments={10}
                    endColor="red"
                    needleTransition={"easeElastic"}
                    ringWidth={30}
                    textColor={"red"}
                />
            </div>
        )
    }
}

export default App; 
```

`state.response`将保存来自后端的值，而`state.endpoint`就是服务器所在的位置。神奇的事情发生在生命周期函数`componentDidMount()`中。对于那些不熟悉 React 的人来说，这个函数是在组件被添加到 DOM 时调用的。因此，这是我们连接到套接字并监听数据的地方。

简单地将我们连接到服务器并打开一个套接字连接。

看起来很熟悉，不是吗？我们开始在`outgoing data`名称空间上列出。我们有一个回调函数，它接受响应并将状态设置为新值。

让我们来看看渲染函数:

```
 render() {
        const {response} = this.state;
        return (
            <div style={{textAlign: "center"}}>
                <ReactSpeedometer
                    maxValue={140}
                    value={response}
                    needleColor="black"
                    startColor="orange"
                    segments={10}
                    endColor="red"
                    needleTransition={"easeElastic"}
                    ringWidth={30}
                    textColor={"red"}
                />
            </div>
        )
    } 
```

ReactSpeedometer 组件有一堆属性，您可以传递给它来定制它。其中大部分是不言自明的，但你可以在这里阅读全部内容。我用“easeElastic”针过渡，因为它看起来很酷，但“eastLinear”可能是里程表的更好选择。在这里阅读过渡效果。

render 函数提取速度的当前状态值，并将其传递给名为`value`的 ReactSpeedometer 属性。这将更新速度表。

## 所以看起来怎么样！

[https://i.imgur.com/D4qzm7o.gif](https://i.imgur.com/D4qzm7o.gif)
(在这篇文章中嵌入 gif 有问题。不好意思！)

它最终表现得更像一个转速计，但结果相当酷！