# PhoneSaber 入门套件

> 原文：<https://dev.to/checkboxoz/phonesaber-starter-kit-1h9m>

[![](img/d6cc2881b66cd333b4d0d3555c327fdf.png)](https://i.giphy.com/media/XdJkp9XEnGPBXzlp8e/giphy.gif)

[https://glitch.com/embed/#!/embed/phonesaber-starter?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/phonesaber-starter?previewSize=100&path=index.html)

*   在桌面上访问此链接(或使用上面的嵌入内容):[https://phone saber-starter . glitch . me](https://phonesaber-starter.glitch.me)
*   点击`Start Server`，记下服务器 ID
*   访问带有 Gyro 的设备上的相同链接(或使用上面的嵌入)
*   键入服务器 ID
*   点击`Connect to Server`
*   移动你的设备在屏幕上看到 3D 光剑更新！

## 背景

所以我一直在开发一些工具来帮助残疾人通过计算机视觉不用手使用网络。这个想法是，人们可以使用头部跟踪来控制屏幕上的鼠标，通过使用 JavaScript，你可以构建任何人都可以在任何设备上使用的工具，而无需下载。

* * *

[![duckhunt-handsfree](img/3ae7753e18c86449c0cd57737efeb66b.png)](https://i.giphy.com/media/MWvfeCGV2MYmaRzvkP/giphy.gif)
[【duck hunt-免提(微笑拍摄)](https://duckhunt-handsfree.glitch.me/)

* * *

然而，只有当你能够将相机对准你的脸时，头部跟踪才起作用，这并不总是可能或实际的。所以我开始考虑用陀螺仪来移动鼠标。

我的第一个想法是将手机作为一种“大理石迷宫平台”，倾斜手机会像大理石一样移动屏幕上的光标，就像那些老式的学校大理石迷宫玩具一样:

[![](img/c9fa7f37a606b8e61524ef124dc55c8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pmp_bzcR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://images-na.ssl-images-amazon.cimg/I/71AVKJRCNDL._SX425_.gif)

但当我有这个想法的时候是 5 月 4 日，因为我是《星球大战》的粉丝，我想尝试使用 Gyro 控制屏幕上的光剑，作为练习使用 API 的一种有趣方式。

这个 API 确实工作得很好，所以我非常渴望开始用它来做可访问性的实验。然而，我想分享我的演示，我已经发布了一个关于 Glitch 的[初学者工具包。](https://glitch.com/~phonesaber-starter)

我没有完成我的应用程序，而是把它变成了一个初学者工具包，可以让你制作令人兴奋的游戏和体验！

## 工作原理

### 依赖关系

*   多年来，我一直用它作为 goto 原型样式表
*   [Babylon.js](https://babylonjs.com) -我用的 3D 引擎。aFrame.io 是另一个很棒的引擎，它已经为 WebVR 做好了准备(而且学习起来更容易)
*   这个库已经有一段时间没有更新了，但是这是我第一次尝试使用 gyro API，所以我用它来简化事情
*   [Firebase](https://firebase.com/) -我如何在客户端/服务器之间通信
*   凯尔·卡特兰的光剑

### 关键文件

*   这可能是你开始游戏代码的地方，包含一个回调函数
*   `/src/scene.js` -我架起光剑的地方，3D 场景
*   `/src/client-server.js` -设置客户端/服务器，你可以忽略这一点

## 思想

*   添加声音效果
*   偏转激光
*   使用 [PoseNet](https://github.com/tensorflow/tfjs-models/tree/master/posenet) 来让它也四处移动！

## API 键

我添加了一个免费的 Firebase API 密匙供你试用，但是我建议你用[创建自己的](https://firebase.google.com/docs/storage/web/start)。这非常快，你所要做的就是用你自己的值替换`index.html`底部的值(在文档中搜索“初始化 Firebase”)

* * *

## 谢谢！

让我知道，如果你需要任何帮助设置等。我没有写很多操作指南，所以我不知道要放多少信息😅