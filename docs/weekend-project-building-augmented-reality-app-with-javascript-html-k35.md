# 周末项目:用 Javascript/HTML 构建增强现实应用

> 原文：<https://dev.to/sezginege/weekend-project-building-augmented-reality-app-with-javascript-html-k35>

在我的 twitter feed 上看到几个有趣的项目后，增强现实(AR)引起了我的注意，我想使用基本的 web 开发技能做一个小的 AR 演示，只是为了实验 AR 的东西。

结果如下:

> 做一些增强现实实验我稍后会在我的博客上发布细节 pic.twitter.com/WPTALpZGj9
> 
> -自订(1230)@自订(t1)[2019 年 4 月 27 日](https://twitter.com/SezginEge/status/1122202410229825537?ref_src=twsrc%5Etfw)

开发时只用过 [a-frame](https://aframe.io/) 框架和 [AR.js](https://github.com/jeromeetienne/AR.js/) 。

*   **[a-frame](https://aframe.io)** :是一个构建虚拟现实体验的 web 框架。
*   **[AR.js](https://github.com/jeromeetienne/AR.js)** :是一个 javascript 框架，在不牺牲性能的情况下，直接在 web 浏览器上启用 AR 特性。它可以在任何支持 webgl 和 webrtc 的手机上工作。

## [让我们看看代码](#lets-see-the-code)

我已经在代码中添加了注释。这很简单。基本上，我们定义我们的场景，初始化 arjs，添加相机和原点标记。此外，我们还定义了希望在原点显示的实体。我已经使用 3D groot 模型，但你可能想显示静态图像/框等。在这种情况下，您需要使用 a-box 或 a-image 组件。

## [我们怎么能检验这个呢？](#how-we-can-test-this)

如果您直接在浏览器上打开 index.html 文件并检查开发人员控制台，您可能会看到 CORS 问题:

 `Access to XMLHttpRequest at 'file:///<workplace>/AR/test/scene.gltf' from origin 'null' has been blocked by CORS policy: Cross origin requests are only supported for protocol schemes: http, data, chrome, chrome-extension, https` 

*请看看 CORS 的维基页面，了解 CORS 的意思:[https://en.wikipedia.org/wiki/Cross-origin_resource_sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)T3】*

为了解决这个问题，我们需要运行 http-server。Node.js 有一个简单的 HTTP 服务器包。要安装，您需要运行:

`npm install http-server -g`

成功执行命令后，我们需要转到 index.html 所在的文件夹，然后启动 http 服务器；

`http-server`

我们应该在终端上看到这样的输出:

`Starting up http-server, serving ./
Available on:
http://127.0.0.1:8080
http://192.168.1.36:8080` 

因此，服务器已经启动并运行。我们可以点击这些地址中的任何一个，然后在浏览器中打开。然而，我们将面临两个问题。

*   用我们浏览器的网络摄像头测试 AR 一点也不舒服。
*   我们需要为我们的服务器创建 SSL 证书。否则，我们会看到“网络摄像头错误不支持错误只允许安全来源”错误:[https://github.com/jeromeetienne/AR.js/issues/399](https://github.com/jeromeetienne/AR.js/issues/399)

为了简化这一过程，我们可以使用 [ngrok](https://ngrok.com/) ，这是一种反向代理软件，可以建立从公共端点(如互联网)到本地运行的网络服务的安全隧道。我们需要下载并运行 ngrok。

`./ngrok http <port that your local server listening>`

我们应该在终端上看到这样的输出:

`Forwarding http://e2db416e.ngrok.io -> localhost:8080`

`Forwarding https://e2db416e.ngrok.io -> localhost:808`

现在，我们应该能够在任何具有互联网连接的设备上使用 https 端点来实现我们的演示。

为了在屏幕上看到我们的实体，我们应该向摄像机展示 hiro 标记。标记可以在这里找到:[https://jeromeetienne.github.io/AR.js/daimg/HIRO.jpg](https://jeromeetienne.github.io/AR.js/daimg/HIRO.jpg)

## [结论](#conclusion)

演示相当简单。然而，如果我们考虑到我们用几行 html 代码和几个 JS 库做了这个演示，这里有巨大的潜力来构建更大的东西。

我正在用我最喜欢的 AR 应用程序之一完成这篇文章。尽情享受吧！