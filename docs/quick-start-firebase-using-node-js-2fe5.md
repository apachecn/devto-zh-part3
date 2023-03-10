# 使用节点 JS 快速启动 Firebase

> 原文：<https://dev.to/nodefiend/quick-start-firebase-using-node-js-2fe5>

照片信用:[幻想渴望](https://unsplash.com/@fancycrave)

# 用途:

### 本指南将帮助您使用一个非常简单的节点 API firebase 后端。

1.  创建一个 firebase 项目，点击转到控制台，设置所有默认设置。

[![alt text](img/2c9b1939cf5a53897b39e75bd8893a81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NeAl3gxF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/os8bdzile9ealhjma7w5.png)

1.  选择创建新项目的默认设置

[![alt text](img/c35621a5c84528d0504198cc57dfef65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jNbNN4cI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e06fjzoufhdc81xthrdj.png)

1.  创建一个您想要加载我们的 firebase 项目的目录(并导航到该目录):

`Sites/firebase_project`

1.  确保您在我们的`firebase_project`的新目录中

2.  如果你的机器上没有安装，安装 firebase 全局包

```
npm install -g firebase-tools 
```

Enter fullscreen mode Exit fullscreen mode

1.  如果您以前从未这样做过，也没有登录，请键入:`firebase login`

6a。这将打开一个浏览器窗口，要求你登录你的谷歌账户。

## 导航回 firebase_project 目录，键入以下内容:

```
firebase init 
```

Enter fullscreen mode Exit fullscreen mode

1.  在命令行你会看到选项，使用箭头键选择:`Functions: Configure and deploy Cloud Functions`并按空格键选择它

2.  现在按回车键

    1.  当它询问语言时，点击`Javascript`。
    2.  如果它要求 ESLINT，按 no(除非你想经常被窃听)
    3.  当它要求安装时，依赖项单击是，

如果您得到一个错误检查，以确保您的目录有正确的权限。

如果在运行`firebase init --debug`模式时仍然出现错误，这将有助于您查看可能出现的任何错误。

1.  导航到`functions/index.js`并取消注释:

```
exports.helloWorld = functions.https.onRequest((request, response) => {
  response.send("Hello from Firebase!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

1.  现在，从终端的目录中运行:

```
firebase deploy 
```

Enter fullscreen mode Exit fullscreen mode

部署后，您应该会得到如下所示的 URL:

[![helloworld](img/7c31895182fcff7404a446bd66ff78a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lspk1OJT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ogky21t113z82h5r0zoa.png)

现在将你在终端中看到的这个链接粘贴到你最喜欢的 HTTP 请求工具中，例如(POSTMAN ),试着点击 URL，看看你是否得到了`"Hello from Firebase!"`

### 恭喜！

这是您的第一个节点 js 请求端点，您可以为它分配一个路由，或者添加您喜欢的任何内容！