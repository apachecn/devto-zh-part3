# 用 Apollo 和 Cosmic JS 构建一个进步的博客应用

> 原文：<https://dev.to/jacobknaack/building-a-progressive-blog-app-with-apollo-and-cosmic-js-1g1g>

你好陌生人。跟我来一次短暂的冒险，我们为发布博客风格的文章构建了一个快速、简单的界面。一些明智的事情。它应该看起来干净、简单，但是能够用一些查询魔法执行强大的资源获取。我说的是 Graphql，这是一种基于 web APIs 构建的查询语言。说到 web APIs，我们还将使用我们方便的内容管理服务:Cosmic JS 来创建和存储我们的博客数据。

## TLDR:

[进步阿波罗博客](https://cosmicjs.com/apps/apollo-blog)
[进步阿波罗博客代码库](https://github.com/jacobknaack/apollo-blog)

## 开始前 0.0

在继续之前，请确保您的机器上安装了所需的开发工具。主要是你会需要[节点 JS](https://nodejs.org/) ，它附带的包管理器: *npm* ，和 [git](https://git-scm.com/) 。Git 是半可选的，如果你的目标是编码存储和部署，它将只用于编码存储和部署。

一旦你安装了这些，我们就可以开始设置我们的项目，并最终开始编写一些 Javascript。

### 0.1 库和包

让我们来看看我们将用来创建我们的博客平台的主要包。

*   **[创建 React 应用](https://facebook.github.io/create-react-app/)**——我们将利用曾经流行的 React 库来构建我们的 UI 组件。为了引导我们的项目，我们还将使用一个方便的命令行工具 create-create-app，这样我们就可以在配置和设置上花费尽可能少的时间，并开始创建有意义的 javascript。

*   这是一个 UI 框架，允许我们导入完全风格化的 UI 组件。没有繁琐的 CSS 规范来获得一些看起来很好的东西。

*   **[阿波罗/graph QL](https://www.apollographql.com/)**——这两个包齐头并进。Apollo 将被用作 Graphql 客户机，向我们的 Graphql 服务器端点发出请求。它易于配置，使用起来非常简单。这又一次让我们能够专注于编写更少的 javascript，而不是专注于应用程序的高级组合。当然，Graphql 将允许我们发出只返回我们关心的数据的请求，让我们有效地从 Cosmic JS 中获取内容。
    我相信所有无聊的事情都过去了，我们可以开始敲打键盘了！

## 1.0 设置我们的项目

让我们启动我们的终端，开始引导我们的项目源文件。对于那些没有安装 Create React 应用程序的用户，我们可以使用一个简单的 npm 命令:

```
$npm install -g create-react-app 
```

一旦完成(或者你已经安装了这个包)，我们可以通过运行 cli 调用我们的初始项目文件:

```
$create-react-app apollo-blog 
```

你会注意到在你的终端上发生了相当多的奇迹，因为我们的应用程序是用各种强大的库和源代码引导的。看一下我们的文件系统，会显示一个名为`apollo-blog`的目录。将目录切换到这个文件夹将会显示一个类似这样的结构:

```
apollo-blog/
    README.md 
    node_modules/
    package.json
    public/
        index.html
        favicon.ico
    src/
        App.css
        App.js
        App.test.js
        index.css
        index.js
        logo.svg 
```

我们现在应该可以运行了:

```
$npm start 
```

或者

```
$yarn start 
```

这将运行存储在我们的 **package.json** 文件中的启动脚本，该文件与 Webpack 和构建工具捆绑在一起，这些工具将处理我们所有的 React 源代码的编译，并将其捆绑到准备提供给浏览器的文件中。

脚本运行后，您应该会在终端中看到一条简单的消息，并且您的默认浏览器应该会打开一个项目正在运行的选项卡。您应该会看到类似这样的内容:

### 1.1 安装 Javascript 库

下一步是安装一些 npm 库，这将有助于快速创建一个好看的应用程序界面，并轻松地从 Cosmic JS graphql 服务器获取数据。

在您的终端中运行以下命令，让我们讨论一下这些包将为我们做些什么:

```
$npm install --save apollo-boost react-apollo graphql dotenv semantic-ui-css semantic-ui-react react-router-dom 
```

Apollo-boost/react-Apollo/graph QL——这些家伙将作为主要的解决方案，将我们的数据从 Cosmic JS 服务器高效、可扩展地传输到我们的客户端 UI。它们为我们的组件提供包装器，以便能够配置查询，并为数据加载和错误处理提供有用的变量。

这个包将允许我们配置半敏感密钥，这样我们就不需要公开存储任何东西来访问我们的宇宙 JS 桶。

semantic-ui-css/semantic-UI-react-我们的 UI 框架将允许我们导入预样式的容器和组件，让我们专注于编程功能，减少花在 CSS 和其他样式考虑上的时间。

react-router-dom -一个 react 库，允许我们配置 url 路由逻辑，以控制应用程序内的链接和访问 URL 参数。

安装好这些之后，我们就可以开始写一些 javascript 了！

### 1.2 配置环境变量

我们将添加一个最低限度的安全预防措施，以便我们不会让任何请求通过我们的 bucket 的 API 端点，请随意跳过这一部分，但要注意，您的组件将会有一些不同的行为，任何知道如何发出 API 请求的人都可以查询您的 bucket。所以，你知道...不要把任何敏感信息放进你的桶里。

我们将要求一个 **read_key** 附加到我们的 bucket 的每个 API 请求上。你必须为你的宇宙 JS 桶生成一个设置标签，点击基本设置。**宇宙 JS >你的斗>设置>主**。

在顶部应该有标题为 **API 访问> API 读取访问密钥**的部分。点击**按钮生成新密钥**。将密匙复制到你的剪贴板，让我们用我们的配置文件配置我们的项目目录:

```
$touch .env 
```

这个文件将被用来配置我们的项目和我们的 API 读访问密钥，这样我们就不必把它硬编码到我们的源代码中，并提交给全世界/其他窥探的人。

只需在您的。环境文件: