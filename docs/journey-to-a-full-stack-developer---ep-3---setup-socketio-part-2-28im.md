# 全栈开发者之旅。3 -设置 Socket.io 第 2 部分

> 原文：<https://dev.to/tnguyen303/journey-to-a-full-stack-developer---ep-3---setup-socketio-part-2-28im>

本周，我们将继续上周关于如何在节点应用中集成 Socket.io 包的帖子。Socket.io 是一个软件包，允许将数据实时推送给与应用程序交互的特定或所有用户，例如聊天应用程序，在脸书 UI 中可以看到实时通知。

从上一集开始，我们现在已经完全设置好了服务器文件:

```
const express = require("express");
const app = express();
//http package lets you run server without Express, socket.io requires more control that is provided with this package
const server = require("http").createServer(app);
const io = require("socket.io")(server);

require("./sockets/message-sockets")(io); 
```

这是我们的前端 javascript 代码，由 html 文件运行，它包含一个函数，通过 POST 路由(ajax 调用)向我们的数据库发送聊天消息，另外还向我们处理实时数据的 socket“listener”文件发送相同的消息:

```
const sendMessage = function(event) {
  const message = $("#message").val();
  event.preventDefault();
    //prepare our data to be sent across your application
    const newMessage = {
      message: message,
      sender: sender,
      recipient: recipient
    };
    //send to server, calling new-message to reference in message-sockets.js
    $.ajax({
      url: `/api/chat/${sender}/${recipient}`,
      method: "POST",
      data: newMessage
    });
    //this is the magic sauce, socket.emit sends data to our server which then gets redirected by the message-sockets.js file to other people's browser. The tag is "new-message" and this must match the receiving socket.on in your message-sockets.js file.
    socket.emit("new-message", newMessage);
}; 
```

正如您所看到的，我们正在调用一个“消息套接字”文件，供 Socket.io 包使用。这是一个 JavaScript 文件，用于保存实时数据的服务器端“处理”:

```
//this is message-sockets.js file in sockets subfolder
module.exports = function(io) {
  console.log("running");
  //this .on listener runs every time a user connects
  io.on("connection", function(socket) {
    //SOCKET ROUTES:

    // push to all sockets, can be used to notify users of a New Version Update is available
    socket.on("new-message-to-all", function(data) {
      io.emit('emit-message-to-all',data);
    });

    //push to specific (private) sockets, can be used to send private info such as messages to a user or group
    socket.on("new-message", function(data){
      const socket1 = users[data.sender];
      const socket2 = users[data.recipient];
      socket1.emit('emit-message', data);
      socket2.emit('emit-message', data);
    });

  });
}; 
```

在服务器重定向了我们的新消息数据之后，前端代码必须有一个函数来监听这个新数据，现在称为“emit-message ”,并将其呈现给目标用户:

```
//this is my client-side javascript file that runs when my html runs
//display message on private sockets
socket.on("emit-message", function(data) {
  //this is a simple function to render a new message to the website
  renderMessageList(data);
}); 
```

它看起来复杂而冗长，但整个代码可以总结为，在 socket.io 中，我们有三个步骤来发送实时数据:

1.  emit——这只是一个通知，告诉服务器有新数据，以及新数据和一个标记
2.  On(服务器端)——这基本上是一个事件监听器，监听特定的“Emit”标记，然后执行一个向客户端目标“sockets”或用户的发射。
3.  On (client-side) -同样，这只是步骤 2 中重定向数据的监听器，并执行一个函数，通常是一个简单的函数，将数据显示给客户端的浏览器。

下周我将与你分享我在测试中学到的东西。希望这是一个有用的方法，我会很高兴知道我帮助了需要帮助的人！