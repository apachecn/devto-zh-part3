# 用 Firecamp 在 10 秒内测试 Socket.io 后端

> 原文：<https://dev.to/nishchit14/test-socketio-backend-in-10sec-with-firecamp-28je>

#### 问题

* * *

插座的测试和调试。IO 实时事件不像今天的休息那么轻松。测试它的唯一方法是用 jQuery/HTML 创建额外的前端，并在那里使用`console.log`。有时硬编码的摩卡测试可以工作。

但是这种方式更复杂，时间更昂贵，更难管理，而且对代码质量和最佳实践的信心更低。

#### 解

* * *

Firecamp 是 SocketIO 和 WebSocket 的 GUI 测试客户端。

通过节省额外的时间和精力，彻底消除测试对前端的依赖，并快速推动实时工程(RTE)的发展。套接字请求可以被保存以备将来之用，这样将来工程师可以在紧急情况下在 **5-10 秒**内开始调试它。

SocketIO 主要有三个元素

1.  `Connection URL` -连接到套接字 URL
2.  `Listener` -监听来自服务器的事件
3.  `Emitter` -向服务器发出事件

我们举一个由 [Ali Spittle](https://dev.to/aspittel) 构建的实时绘图应用的例子。

1.  演示应用:[https://www.alispit.tel/websocket-drawing/](https://www.alispit.tel/websocket-drawing/)
2.  后端:[https://github . com/aspittel/web socket-drawing-back end/blob/master/app . js](https://github.com/aspittel/websocket-drawing-backend/blob/master/app.js)
3.  前端:[https://github . com/aspittel/web socket-drawing/blob/master/src/app . js](https://github.com/aspittel/websocket-drawing/blob/master/src/App.js)

从后端服务器和演示应用程序，我们有

1.  连接 URL，
    *   `https://agile-taiga-80620.herokuapp.com`
    *   和路径`/socket.io`
2.  听众
    *   `color_change`
3.  发射器
    *   `color_change`
    *   有效负载:{ "color": "#e0e0e0 "，" id": 71 }

本视频将展示调试套接字事件的三个主要步骤。**接**、**听**、**发**，就这么简单。

[https://www.youtube.com/embed/Y3wbhbSPKxY](https://www.youtube.com/embed/Y3wbhbSPKxY)

感谢[阿里·斯皮特尔](https://dev.to/aspittel)的[实时绘图 app](https://www.alispit.tel/websocket-drawing/) 和[文章。](https://dev.to/aspittel/socketio-making-web-sockets-a-piece-of-cake-bmd)