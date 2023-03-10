# 网络上互斥的终端

> 原文：<https://dev.to/l04db4l4nc3r/mutually-exclusive-terminals-on-the-web-4cde>

**简介**

在这个在线学习的时代，坐在你最喜欢的沙发上，一边享受你最喜欢的咖啡，一边点击一个按钮就能掌握知识。在这个教育在线分发的时代，互斥的考场是一种必需品。我们将学习如何创建一个在线共享命令行考场。

请注意，将要被引用的[项目](https://github.com/L04DB4L4NC3R/examinaar)具有以下可交付成果:

*   网络上的终端会话
*   能够随意创建/删除任何操作系统的外壳实例
*   文件系统共享，但基于会话的排他性
*   随意加入/离开多个会话的能力
*   会话期间的实时视频聊天

事不宜迟，让我们开始一段旅程，它将把你带到浏览器的地平线和终端的顶点。

*   golang 标准网络/http 库
*   docker 社区版
*   gotty，go web 终端库

我们的应用程序的基本草案将有这样的双向共享终端-

[![Two exclusive terminals running on the web](img/1ab46bda75a2c9cd8a9dbe6bfd798156.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A20MmE_a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w34ctkpeptye5k5psbts.png)

*   确保安装最新版本的 golang
*   安装 docker CE 的最新版本
*   安装 [goTTY](https://www.tecmint.com/gotty-share-linux-terminal-in-web-browser/)

**安装 go 依赖项**

```
$ sudo apt-get update && sudo apt-get upgrade -y
$ go get github.com/yudai/gotty
$ tree .

├── bin  
│   ├── get_dependancies  
│   └── main  
├── _config.yml  
├── controller  
│   ├── auth.go  
│   ├── helpers.go  
│   ├── host.go  
│   ├── routers.go  
│   └── user.go  
├── database.db  
├── main.go  
├── model  
│   └── dbfuncs.go  
├── README.md  
└── views  
    ├── _card.html  
    ├── host.html  
    ├── index.html  
    ├── login.html  
    ├── _navbar.html  
    ├── _scripts.html  
    ├── session.html  
    ├── signup.html  
    └── view\_sessions.html 
```

Enter fullscreen mode Exit fullscreen mode

**写入 main.go 服务器文件**