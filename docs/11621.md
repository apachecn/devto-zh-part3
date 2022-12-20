# 线上/线下？马上检查！

> 原文：<https://dev.to/nishugoel/onlineoffline-check-in-a-minute-37a1>

这是一个小而简单的角度应用程序来检查你是否连接到互联网或没有！

首先，您需要在 angular 应用程序中安装一个名为 ng-connection-service 的包。让我们看看如何做到这一点。

使用以下命令从 CLI 创建新的角度应用程序

ng 新互联网测试

使用以下命令从 CLI 在应用程序中安装 ng-connection-service 包:

NPM install ng-connection-service—保存
软件包安装完成后，转到 package.json 检查是否已成功安装。

在 package.json 中，您应该看到，

[https://thepractical dev . S3 . Amazon AWS . com/I/gkk 7 naosmjxdwtvh 9 BMC . png](https://thepracticaldev.s3.amazonaws.com/i/gkk7naosmjxdwtvh9bmc.png)

现在已经安装了所需的包，转到 component.ts 文件并导入连接服务，如下所示:

从“ng-connection-service”导入{ connection service }；

最后，让我们将服务注入到组件中。如果你熟悉服务，你会知道要注入一个服务，我们去组件类的构造器，注入它如下:

构造函数(private connection service:connection service){
})；

为了添加检查互联网连接的功能，我们使用服务的函数，并切换 isConnected 属性的值，该属性默认情况下初始化为 true，status 属性默认情况下初始化为“ONLINE”。参见下面的代码:

[https://gist . github . com/nishugoel/6 BBA 8594 c 1424327 aee 172365 BF 4 f75f](https://gist.github.com/NishuGoel/6bba8594c1424327aee172365bf4f75f)

现在，我们最后提醒组件类中的连接状态，完成的组件如下所示:

[https://gist . github . com/NishuGoel/CDA 15 ca 29331 E6 af 9 EDB 5 DCE 24 c8 CB 9d](https://gist.github.com/NishuGoel/cda15ca29331e6af9edb5dce24c8cb9d)

您的组件 html 看起来像:

[https://gist . github . com/NishuGoel/ADB 00727 C2 E1 a 20 AE 03033 f 21 bb 58159](https://gist.github.com/NishuGoel/adb00727c2e1a20ae03033f21bb58159)

结果看起来是这样的:

[https://thepractical dev . S3 . Amazon AWS . com/I/a3 elykc 5 jdqcq 2 m1 jrzk . gif](https://thepracticaldev.s3.amazonaws.com/i/a3elykc5jdqcq2m1jrzk.gif)

演示也放在了 Stackblitz 上。
在这里找到它:

[https://stackblitz.com/edit/testinternet](https://stackblitz.com/edit/testinternet)