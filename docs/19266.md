# 进入节点

> 原文：<https://dev.to/rjdoughty/getting-in-the-node-2l8e>

我写过关于使用 html 创建输入和使用 JavaScript 和 jQuery 接收用户输入的博客。所以我的下一个话题是输入值会发生什么变化。为了扩展这个想法，我必须考虑 Node.js 对 web 开发有多重要。

Node.js 是一个增强 JavaScript 功能的应用程序。JavaScript 只能在浏览器中运行，以使网页具有交互性。Node.js 可以在没有浏览器的情况下作为独立的应用程序运行。JavaScript 就像一列火车，只能在铁轨上行驶。Node.js 对 JavaScript 火车进行转换，使其可以上路行驶。这创造了更多的可能性。

Node 的最大用途是作为 web 服务器的基础。web 服务器接受客户请求，并返回一些内容，比如新的网页、文本信息或内容。

感谢一些伟大的程序员，已经有一些包可以包含在应用程序中来创建 web 服务器。Node.js 使用 npm(节点包管理器),它拥有可以使开发更快更好的包。需要加载从 npm 安装的库。节点模块内置于自动安装的模块中。

软件包是通过向终端输入命令来创建的。在项目文件夹中，在终端中输入命令`npm init`创建 package.json 文件。package.json 文件将包含指向应用程序的入口点。Express 是一个节点包，有助于使路由更容易，这将有助于从服务器发送和检索信息。要安装 express，请输入命令`npm install express`，其他节点模块将被添加到文件夹中。

我们现在准备创建存储信息的模式和发送和接收信息的路由。