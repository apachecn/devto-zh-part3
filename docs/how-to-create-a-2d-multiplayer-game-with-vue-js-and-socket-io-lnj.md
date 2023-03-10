# 如何用 Vue.js 和 Socket.io 创建 2D 多人游戏

> 原文：<https://dev.to/bnevilleoneill/how-to-create-a-2d-multiplayer-game-with-vue-js-and-socket-io-lnj>

**作者:[NIC raboy](https://blog.logrocket.com/author/nicraboy/)T3】✏️**

[![](img/a75165cd6b5b821c6d61484db6094357.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yOYhhPIB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AfPw-jaibk0n-pJFSIMJH8g.png)

请看这里的摘要:
[https://www.youtube.com/embed/JEYEpledOxs](https://www.youtube.com/embed/JEYEpledOxs)
当涉及到多个客户端和一个服务器之间的实时通信时，RESTful 和 GraphQL APIs 通常不是最佳方法，因为它们必须不断轮询数据的变化。相反，首选的方法是使用套接字，无论是 web 套接字还是其他。

因为套接字对于实时数据非常有用，所以它们对于在线多人游戏非常有益。玩家可以在他们的游戏中进行交互，数据被发送到服务器，服务器将数据广播给游戏中的其他玩家。这就是我们要举的例子。

在本教程中，我们将使用 Vue.js 探索基于 web 浏览器的游戏，但我们将从多人的角度通过 [Socket.io](https://socket.io/) 包括套接字。

为了了解我们想要实现的目标，请看下面的动画图像:

[![](img/29a95c5a4a8077cc03dab16e06e056ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S1lr8giq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Awc2FGcCgJFfOFbaE_VI7LQ.gif)

首先，你会注意到在游戏方面有一个诱饵和开关。是的，这是我们将要探索的游戏的基础，但我们不会制作一个真正的游戏。相反，您会注意到我们打开了两个 web 浏览器窗口，我们对其中一个窗口所做的任何更改都会反映在另一个窗口中。

总而言之，正在发生的事情是，按钮向服务器发送一个消息，我们要在某个方向上移动画布矩形。不是客户端改变盒子的位置，而是服务器用新的盒子位置来响应，所以所有的客户端都是同步的，没有人伪造位置来欺骗。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### **如何用 Node.js 和简单的 JavaScript 创建游戏服务器**

虽然 Vue.js 是本教程的重点，但所有繁重的工作都由我们的服务器来完成，它将使用 Node.js 来创建。

假设您已经安装了的 [Node.js，创建一个新的目录来代表您的服务器，并执行以下命令来创建一个新项目:](https://nodejs.org/en/) 

```
npm init --y
npm install express socket.io --save
touch app.js 
```

Enter fullscreen mode Exit fullscreen mode

以上命令将创建一个 **package.json** 文件，并在其中安装我们的两个项目依赖项。上述命令还将为我们所有的服务器端逻辑创建一个 **app.js** 文件。如果你的操作系统不支持 touch 命令，继续创建 **app.js** 文件，不过这是对你最有意义的。

首先，打开 **app.js** 文件，添加以下样本 JavaScript 代码:

```
const Express = require("express")();
const Http = require("http").Server(Express);
const Socketio = require("socket.io")(Http);

var position = {
    x: 200,
    y: 200
};

Http.listen(3000, () => {
    console.log("Listening at :3000...");
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们导入并初始化了之前安装的每个包。我们还在端口 [3000](http://localhost:3000) 上启动我们的服务器。因为在这个例子中我们不会使用数据库，所以只要服务器使用 position 变量运行，对游戏中数据的任何更改都将持续。

这个伪游戏只有一个相关的数据，即 x 和 y 位置数据。

现在我们可以开始向我们的服务器添加更多的定义逻辑:

```
Socketio.on("connection", socket => {
    socket.emit("position", position);
    socket.on("move", data => {
        switch(data) {
            case "left":
                position.x -= 5;
                Socketio.emit("position", position);
                break;
            case "right":
                position.x += 5;
                Socketio.emit("position", position);
                break;
            case "up":
                position.y -= 5;
                Socketio.emit("position", position);
                break;
            case "down":
                position.y += 5;
                Socketio.emit("position", position);
                break;
        }
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的连接侦听器中，我们正在侦听新的客户端连接。当客户端连接时，存储的正方形位置被发出，这样客户端就可以更新画布。虽然职位的副本将保存在客户端本地，但我们永远不会依赖客户端来提供职位。

为任何特定的客户机建立套接字连接后，就会创建一个侦听器。当客户端发送标题为 move 的消息时，与该消息一起发送的数据用于确定位置应该如何改变。在位置数据改变之后，它被发送回客户端。

您会注意到，我们的信息有一个职位标题和一个移动标题。只要你在客户端和服务器端保持一致，你想怎么叫都行。在我们的例子中，move 表示从客户端发送的移动对象的消息，而 position 表示包含实际位置的消息。

您可能还会注意到，在 socket 和 Socketio 上都使用了 emit。在套接字的情况下，消息只被发送到一个特定的客户端，而使用套接字发送的消息被广播到所有连接的客户端。

现在，我们可以启动 Node.js 服务器，开始开发 Vue.js 应用程序。

### **用 Vue.js 和 Socket.io 开发客户端前端**

我们创建了我们的服务器，但我们没有任何东西来呈现我们的游戏或位置的变化。

假设您已经安装了 [Vue CLI](https://cli.vuejs.org/) ，在 Node.js 项目目录之外执行以下代码:

```
vue create client 
```

Enter fullscreen mode Exit fullscreen mode

出现提示时，选择使用默认值。在 Vue.js 项目目录中导航，执行以下命令再次安装 Socket.io】

```
npm install socket.io --save 
```

Enter fullscreen mode Exit fullscreen mode

记住，我们正在处理两个不同的项目，都使用 Socket.io，因此需要安装 Socket.io 两次。

在 Vue.js 项目中创建一个**src/components/block game . vue**文件来表示我们的游戏组件。这个文件应该包含以下样板代码:

```
<template>
    <div>
        <canvas ref="game" width="640" height="480" style="border: 1px solid black;"></canvas>
    </div>
</template>

<script>
    import io from "socket.io-client";
    export default {
        name: 'BlockGame',
        data() {
            return {
                socket: {},
                context: {},
                position: {
                    x: 0,
                    y: 0
                }
            }
        },
        created() { },
        mounted() {
            this.context = this.$refs.game.getContext("2d");
        },
        methods: { }
    }
</script>

<style scoped></style> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们有一个游戏引用的 HTML 画布。我们不能在 Vue.js 中直接访问 DOM，所以我们必须使用适当的引用。

在`<script>`标签中，我们导入了 Socket.io 客户端，并且定义了一些在组件中使用的变量。在 HTML 视图挂载之后，我们可以利用 mounted 方法来获取对 HTML canvas 对象的引用。

这就是我们开始进入套接字的地方。

在装载视图之前，我们希望建立一个到之前创建的 Socket.io 服务器的连接。我们可以在创建的方法中这样做:

```
created() {
    this.socket = io("http://localhost:3000");
}, 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们连接到我们的服务器，我们可以开始监听服务器广播的位置变化，这样我们就可以渲染它们。因为这将是渲染，我们需要确保视图准备好了。因此，我们必须监听挂载方法的变化，如下:

```
mounted() {
    this.context = this.$refs.game.getContext("2d");
    this.socket.on("position", data => {
        this.position = data;
        this.context.clearRect(0, 0, this.$refs.game.width, this.$refs.game.height);
        this.context.fillStyle = "#FFFFFF";
        this.context.fillRect(0, 0, this.$refs.game.width, this.$refs.game.width);
        this.context.fillStyle = "#000000";
        this.context.fillRect(this.position.x, this.position.y, 20, 20);
    });
}, 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们有一个位置消息的监听器。请记住，我们的服务器正在发送标记为 position 的消息，因此我们需要准备好接收它们。

当我们收到位置信息时，我们清除画布，呈现我们选择的背景，然后根据侦听器中返回的坐标信息绘制一个矩形。每次服务器说我们的位置变了都会这样。

那么我们该如何改变自己的立场呢？让我们为这项工作建立一个方法:

```
methods: {
    move(direction) { this.socket.emit("move", direction); },
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的 move 方法需要一个方向。这个方向被发送到服务器。记住，服务器正在监听标记为 move 的消息。在调用 move 方法后，我们实际上并不移动矩形。实际上，我们只有在得到服务器关于新位置的响应后才会移动。

让我们创建几个按钮来利用这个新的移动方法:

```
<template>
    <div>
        <canvas ref="game" width="640" height="480" style="border: 1px solid black;"></canvas>
        <p>
            <button v-on:click="move('right')">Right</button>
            <button v-on:click="move('left')">Left</button>
            <button v-on:click="move('up')">Up</button>
            <button v-on:click="move('down')">Down</button>
        </p>
    </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

在`<template>`标签中，我们有四个按钮，每个按钮向 move 方法传递一个方向。我们正在做的事情没有什么新奇的事情发生。

到目前为止，我们有一个区块游戏组件，但是它没有与我们的应用程序挂钩。为此，打开项目的 **src/App.vue** 文件，并包含以下内容:

```
<template>
    <div id="app">
        <BlockGame />
    </div>
</template>

<script>
    import BlockGame from './components/BlockGame.vue'

export default {
        name: 'app',
        components: {
            BlockGame
        }
    }
</script>

<style>
    body {
        background-color: #ddd;
    }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

我们的 BlockGame 组件是非常独立的，所以只需导入它，然后在 block 中使用它就足够了。

假设服务器当前正在运行，这可以通过现在运行 Vue.js 客户机来测试。

### **结论**

您刚刚看到了如何使用 Vue.js 和 [Socket.io](https://socket.io/) 来构建一个简单的游戏。这个游戏很简单，因为没有竞技角度。我们用 Node.js 创建了一个游戏服务器，用 Vue.js 创建了一个客户端。服务器向每个客户端发送位置信息，客户端使用该位置信息在屏幕上呈现一个 2D 对象。

如果你想看一个关于 Socket.io 的基本聊天示例，我在几年前用 Angular 创建了一个例子，标题是[用 CEAN 堆栈和 Socket.io](https://www.thepolyglotdeveloper.com/2016/01/create-a-real-time-chat-application-with-the-cean-stack-and-socket-io/) 创建一个实时聊天应用。

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[如何用 Vue.js 和 Socket.io](https://blog.logrocket.com/how-to-create-a-2d-multiplayer-game-with-vue-js-and-socket-io-174ef2818e65/) 创建 2D 多人游戏最先出现在 [LogRocket 博客](https://blog.logrocket.com)上。