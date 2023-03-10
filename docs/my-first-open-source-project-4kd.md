# 我的第一个开源项目。

> 原文：<https://dev.to/zafarsaleem/my-first-open-source-project-4kd>

作为雇主或客户的雇员，开发网站和应用程序很有趣。然而，开发自己的产品更有趣。我已经做了第一部分一段时间了。对于后者，在最近获得大量空闲时间后。我开始着手自己的项目。

我把这个项目命名为 TimeOff。想法和其他人一样，有些是新的，有些是现存的。然而，我继续选择了一个已经存在的。

这个项目是所有关于假期设置，即员工可以很容易地设置易于使用的用户界面休假。

在这篇博文中，我将描述这个项目。

我在这个项目中使用的技术堆栈如下。

节点
Express
Mongodb
React
Redux
Redux saga
React 路由器

服务器端的实现是可伸缩的。它所需要的只是不断实现新的服务。整个架构分为以下文件夹结构。

[![Server side folder structure](img/91874ca48cc163dc093f734766095d9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gajDD_IN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xlbokwywxe8n2dr9tolw.png)

该架构的完整报告可以在下面的存储库中找到。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [扎法尔-萨利姆](https://github.com/zafar-saleem)/[nodescalearchitecture](https://github.com/zafar-saleem/NodeScalableArchitecture)

### 可扩展的节点架构/服务器。这个库包含了在我的中型博客上编写可伸缩 nodejs 服务器/架构的完整实现。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 可扩展的节点服务器/可扩展的节点架构

[![Tweet](img/4182637137f34f5a0aa620c1e2afc983.png)](https://twitter.com/intent/tweet?text=How%20to%20build%20a%20Node.js%20server%20that%20scales&url=https://medium.freecodecamp.org/writing-scalable-architecture-for-node-js-2b58e0523d7f)

注意:这是我在 medium 上的博客的一个示例项目，可以在这里找到。

## 开始

1.  克隆此存储库
2.  在其根文件夹中运行`npm install`。
3.  运行`nodemon index.js`(假设`nodemon` npm 软件包已经安装在您的系统中)

## 该项目的客户端

为了使用浏览器测试这个项目，我在 react 和 redux 中实现了这个项目的客户端。这个博客可以在[这里](https://medium.com/@zafarsaleem/login-using-react-redux-redux-saga-86b26c8180e)找到

### 真实世界项目

我用这个架构开发了一个完整的真实世界项目，可以在这里找到。请继续探索它，如果可以的话留下反馈。

</article>

[View on GitHub](https://github.com/zafar-saleem/NodeScalableArchitecture)

关于如何写以上结构的博客可以在我的媒体简介[这里](https://medium.com/@zafarsaleem/writing-scalable-architecture-for-node-js-2b58e0523d7f)找到。

我将解释我的开源项目，而不是我的博客上面的部分，那里提供了关于如何编写文件夹结构的详细描述。

控制器。因为每个端点都是一个独立的服务。每个服务都有一个控制器，它指向服务文件夹中它各自的服务。控制器为客户端提供一个端点，并与服务交互以处理数据。

单个服务是一个功能或一组功能。一个服务可以驻留在单个文件或多个文件中。

例如，认证服务有一个包含登录和注册服务的文件。

管理员有一个文件，有几个服务，如创建新员工，获取员工名单，更新员工档案等。

控制器是一个快速路由，向客户端公开 API 端点。

如需更多端点和服务，请继续浏览上述存储库中的控制器和服务文件夹。

客户端实现的时间到了。客户端在 react 中实现，遵循 redux 状态管理库模式。下面是文件夹结构。

[![](img/86dfabf1cbc85a480f891683534e01f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VRWkHP1u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6tqhwpu5aoool9970nk7.png)

这个项目的样板文件可以在这个存储库中找到。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [扎法尔-萨利姆](https://github.com/zafar-saleem) / [反应-登录](https://github.com/zafar-saleem/react-login)

### 使用 react.js 对我的博客进行身份验证的客户端实现。这是我之前关于如何实现可伸缩 node.js 服务器的博客的第二部分。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 使用 react 的用户认证

[![Tweet](img/4182637137f34f5a0aa620c1e2afc983.png)](https://twitter.com/intent/tweet?text=How%20to%20set%20up%20user%20authentication%20using%20React%20Redux%20and%20Redux%20Saga&url=https://medium.freecodecamp.org/login-using-react-redux-redux-saga-86b26c8180e)

注意:这是我在 medium 上的博客的一个示例项目，可以在这里找到

## 开始之前

在使用这个项目之前，请确保你的服务器端正在运行，可以在这里找到

## 开始

1.  克隆此存储库
2.  在其根文件夹中运行 npm install。
3.  运行 npm 启动
4.  在您的浏览器中进入 [http://localhost:3000](http://localhost:3000) 查看它的运行情况。

### 真实世界项目

我用这个架构实现了一个真实世界的项目，可以在这里找到

</article>

[View on GitHub](https://github.com/zafar-saleem/react-login)

它拥有所有标准的 redux 概念。Actions 文件夹包含操作列表，reducers 包含 reducers 列表，该列表分为 admin 和 employees 部分。组件有组件列表，saga 有 saga 列表，服务有所有服务列表。

整个应用程序分为两个部分 admin 和 employees。

管理员是控制整个应用程序和所有员工帐户的人。员工可以控制自己的账户和自己设定的假期。

这就是我开发的整个项目，并将其开源。

我希望你喜欢，请留下反馈或提出问题和请求。

以下是我的 github 上这些项目的链接。

客户端

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [扎法尔-萨利姆](https://github.com/zafar-saleem) / [暂停-应用](https://github.com/zafar-saleem/timeoff-app)

### TimeOff 是一个允许公司员工在开始休假前设置假期的应用程序。在现代技术堆栈中实施，即 React、Redux、Redux Saga。

<article class="markdown-body entry-content container-lg" itemprop="text">

### 注意

此项目不再保留在此存储库中。请点击此处了解更多详情和最新项目。

</article>

[View on GitHub](https://github.com/zafar-saleem/timeoff-app)

服务器

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [扎法尔-萨利姆](https://github.com/zafar-saleem) / [暂停服务器](https://github.com/zafar-saleem/timeoff-server)

### TimeOff 是一个允许公司员工在开始休假前设置假期的应用程序。在现代技术体系中实施，即 Node、Express、MongoDB。

<article class="markdown-body entry-content container-lg" itemprop="text">

### 注意

该项目不再在此链接上维护。请点击此处的查看最新项目。

</article>

[View on GitHub](https://github.com/zafar-saleem/timeoff-server)

注意，我已经在 freecodecamp 上写了一篇关于这个项目的博文，但尚未发表。这篇文章和那篇文章的主旨是一样的，但是写作的方法不同。这篇博文的主要目的是写我自己的开源项目，而不是为雇主或特定客户工作。