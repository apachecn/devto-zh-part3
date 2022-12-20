# Gammu SMSD 的 Web 客户端

> 原文：<https://dev.to/joenas/web-client-for-gammu-smsd-3n94>

[![Web client for Gammu SMSD](img/3f8d54d426c03af15c612f71391ec524.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dujVyx0R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonnev.se/conteimg/2019/01/Screenshot-2019-01-01-at-14.06.59.png)

最后，我发布了我为 [Gammu SMSD](https://wammu.eu/smsd/) 编写的客户端，我在我的 Raspberry Pi Zero [短信网关](https://dev.to/joenas/raspberry-pi-zero-as-sms-gateway-58gk)上使用它。这是一个 React 应用程序，带有一个 Node Express 后端，用于从 Postgres 数据库中获取存储的消息。虽然仍然是一个 *WIP* (需要认证和一些用户界面的爱)，它可以很好地接收和发送/回复短信。

💻👉**源代码**在这里是带有不同部署方式的指令。

我自己用 Docker 直接在我的 Raspberry Pi 上运行它，但是如果您已经设置好了，也可以将它连接到现有的 Postgres 实例。或者在单独的机器上运行它，最好是用`docker-compose`并让您的`gammu-smsd`连接到数据库容器。

我学到了很多关于 Gammu 内部和 React/Node 的东西。我的第一次尝试是在 [Crystal](https://crystal-lang.org/) 中构建后端，因为我认为发布一个小的二进制文件对 RPi 来说是完美的，但这比预期的要难。这可能是另一篇博文的内容...

同样**也是**在 RPi 上构建 Docker 图像是混乱的，这让我有了我在[上一篇文章](https://dev.to/joenas/building-node-docker-images-for--raspberry-pi-zero-2hfm)中提到的发现。我还在这个存储库中包含了我的示例中的一个脚本版本，以便于其他人构建和部署 Docker 映像的`armv6`版本。

不管怎样，我希望有人觉得这很有用，请给我留言，告诉我你的任何反馈！