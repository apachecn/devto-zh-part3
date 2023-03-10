# MockIt:帮助开发人员模仿端点的工具

> 原文：<https://dev.to/boyney123/mockit-a-tool-to-help-developers-mock-endpoints-39bp>

我想和你分享一个开源项目，这个项目是我在过去一个月左右的时间里一直在做的，它可以帮助开发者模仿 HTTP 端点。

[https://github.com/boyney123/mockit](https://github.com/boyney123/mockit)

# 问题

构建应用程序时，您经常需要与服务进行交互。当服务还没有准备好被消费时，您有几个选择:

*   用一个 JSON 文件模拟响应
*   自己创建一个模拟服务
*   使用 MockIt。

# 此解

该工具旨在帮助开发人员快速为他们的应用程序创建端点。无需创建服务器或构建 API，使用 docker，您只需一个命令就可以运行该项目。

使用 MockIt，您可以创建、编辑和管理到 API 的路由。对 API 的每一个更改都会反映在服务器上，并立即更新(实时重新加载)。

MockIt 有一些现成的特性:

*   克-奥二氏分级量表
*   基本认证
*   混沌猴(释放一只猴子来拿下你的端点)

如果你想了解更多关于 MockIt 的信息，我为你制作了一个小的介绍视频(点击下面的图片)

[![Youtube Video](img/7578526dc18c4ee51eebe409dd7852ec.png)](https://www.youtube.com/watch?v=Ns1SGH8k7oE&t)

[https://github.com/boyney123/mockit](https://github.com/boyney123/mockit)