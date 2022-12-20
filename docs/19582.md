# Socket.io

> 原文：<https://dev.to/westondease/socketio-4ggd>

Socket.io 是一个节点包，支持客户端和服务器之间的实时通信。这使我们能够让客户实时地相互交流，甚至不需要刷新页面！让我们看看这是如何工作的。

如果我们要让我们的应用程序通过 socket.io 连接到服务器，我们将在服务器中实现以下代码:

const express = require(' express ')；
const app = express()；
const server = require('http ')。createServer(app)；
const io = require('socket.io ')。监听(服务器)；

这将我们的服务器设置为包含 socket.io，但我们需要应用程序来建立连接。因此，在我们的服务器中，我们还需要设置一个连接语句来处理客户端连接。我们包括以下内容。

io.on('connection '，function(socket){
console . log(" we connected！");
//这里有更多 socket.io 的代码

这段代码应该记录“我们连接了！”当客户端连接时，在后端。但是连接还不够，我们还想发送和接收信息。因此，为了接收消息，在连接内部，我们使用:

//这里我们监听一个名为' some-event '
socket . on(' some-event '，function(data){
console . log(data)；
io.emit('new-event '，newData//这里 newData 是一些做作的数据
})；

现在让我们看看 io.emit 的最后一行，这是我们发送事件的方式。如果我们在应用程序文件中放置一个监听器，如下所示:

socket.on('new-event '，function(data){
console . log(data)；
})；

这段代码监听新事件，并在客户端的控制台上记录它。这意味着我们现在拥有了让这个应用程序实时通信的所有部件。这样做的好处是可以改变我们数据库中的数据，并使其在所有客户中保持最新。这只是最基本的。我们可以使用几种不同的语句来包含我们的消息发送到哪里。我们拥有的当前语句发送给所有用户。但是我们可以做得更好。下面是我们可以使用的所有 emit 类型的一个方便的备忘单:

[https://socket.io/docs/emit-cheatsheet/](https://socket.io/docs/emit-cheatsheet/)