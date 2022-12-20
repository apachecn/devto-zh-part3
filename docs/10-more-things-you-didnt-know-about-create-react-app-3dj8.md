# 关于 Create React App 你不知道的 10 件事

> 原文：<https://dev.to/progresstelerik/10-more-things-you-didnt-know-about-create-react-app-3dj8>

编者按:上个月我们发表了另一篇关于“[我不知道的关于 Create React App 的 5 件事](https://dev.to/progresstelerik/5-things-i-didnt-know-about-create-react-app-49nj)”的文章——也可以看看这篇文章，了解更多关于 React 的技巧和窍门！

* * *

了解 Create React App 的新提示和技巧，使您更高效地使用工具，并帮助您更快地构建应用程序。

Create React App 是一款为设置 React 应用程序而开发的工具。它为用户节省了配置和设置的时间。使用 Create React App，用户可以在几分钟内设置并运行单页 React 应用程序。

这个巧妙的工具可以为用户做很多事情。有些特性非常受欢迎，比如它不需要设置，用户可以通过运行一个命令来创建一个完全成熟的应用程序。但是这个工具能做的比它最忠实的用户所知道的要多得多。

在这篇文章中，我们将列举十件你可能不知道的关于 Create React App 的事情。有些可能会让你大吃一惊。如果你发现你知道这里列出的大部分东西，那么你应该留意一些对你来说是新的东西——它们可能真的会派上用场。

## 1。服务人员支持

Create React App 为服务人员提供了开箱即用的支持。这意味着您的应用程序可以是一个离线工作的渐进式 Web 应用程序，并使用缓存优先策略。在最新一期的 [Create React App(第 2 版)](https://dev.to/progresstelerik/hello-create-react-app-20-316-temp-slug-3014493)中，服务人员只能选择加入。

要在您的生产构建中使用服务人员，您必须在您的`index.js`文件中注册服务人员。在您的`src/index.js`文件中，查找下面一行:

```
// If you want your app to work offline and load faster, you can change  
// unregister() to register() below. Note this comes with some pitfalls.  
// Learn more about service workers: http://bit.ly/CRA-PWA  
serviceWorker.unregister(); 
```

将`serviceWorker.unregister()`改为`serviceWorker.register()`。选择使用服务人员使您的应用程序成为离线优先的渐进式 Web 应用程序，具有缓存资产和添加到移动用户主屏幕的能力。

## 2。CSS 自动重排

为了确保跨浏览器支持，Create React App 将供应商前缀添加到应用程序的 CSS 中。这减少了在样式化组件时手动添加供应商前缀的压力。这方面的一个例子是`flex`显示属性。下面的代码片段将由此转换而来:

```
.App {  
display: flex;  
flex-direction: row;  
align-items: center;  
} 
```

到此缩小后:

```
.App {  
display: -webkit-box;  
display: -ms-flexbox;  
display: flex;  
-webkit-box-orient: horizontal;  
-webkit-box-direction: normal;  
-ms-flex-direction: row;  
flex-direction: row;  
-webkit-box-align: center;  
-ms-flex-align: center;  
align-items: center;  
} 
```

您可以通过使用[浏览器列表](https://github.com/browserslist/browserslist#readme)规范更改`package.json`文件中的`browserlist`属性来控制、限制和定位支持浏览器。

点击阅读 Create React 应用[中关于自动修复的更多内容。](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#post-processing-css)

## 3。SASS 支持

Create React App v2 增加了对 CSS 预处理程序的支持。最后，我们在 Create React 应用程序中有现成的嵌套和混合支持。在以前版本的 Create React App 中，为了实现类似嵌套的功能，我们使用了组件组合。要开始在您的项目中使用 SCSS，请安装 [`node-sass`](https://github.com/sass/node-sass) ，然后将所有`css`文件重命名为`scss`。

你可以在 Create React App [这里](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-sass-stylesheet)阅读更多关于 SCSS 入门的信息。

## 4。使用动态导入进行代码拆分

在构建我们的应用程序的过程中，我们最终会得到臃肿的构建文件。代码分割作为一种技术可以帮助减少构建文件的大小。Create React App 支持开箱即用的[动态导入](https://github.com/tc39/proposal-dynamic-import)提议。使用动态导入，包的大小可以大大减少。

动态导入是异步的，所以它们可以和`Async/Await`一起使用。使用这种技术，组件是按需导入的，并将从主包文件中单独构建，从而减小包的大小。

下面的代码片段展示了如何利用`import()`特性:

```
import React from 'react'  

export class TestComponent extends React.Component{  
constructor(){  
super();  
this.onClick = this.onClick.bind(this);  
}  

async onClick(){  
const component = await import('../neededComponent.js');  
}  

render(){  
return <button onClick={this.onClick}>Lazy load me</button>  
}  
} 
```

点击阅读更多关于使用动态导入进行代码分割的内容[。](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-sass-stylesheet)

## 5。开发期间的代理 API 请求

通常在开发过程中，我们在相同的主机和端口上设置服务器和 React 应用程序，然后我们将在"`/"`上提供前端应用程序，也可能从"`/api"`路径提供 API。使用 Create React app，您不一定需要进行设置，因为您可以告诉 Create React App 服务器将此类请求代理到您的 API 服务器。

因此，要让这个特性工作，您需要做的就是在您的`package.json`文件中添加一个`proxy`字段。

```
“proxy”: ‘[http://localhost:4000](http://localhost:4000)’ 
```

这样，开发服务器无法处理的任何请求都将代理到`package.json`文件中代理字段的值。所以请求到`/api/todos`会代理到`http://localhost:4000/api/todos`。

这非常方便，因为你不必在开发中处理 CORS 问题。点击阅读更多关于代理 API 请求[的信息。](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#proxying-api-requests-in-development)

## 6。在开发过程中支持 HTTPS

在开发过程中，可能需要开发服务器提供超过`HTTPS`的页面。也许 OAuth 应用程序要求您的应用程序在认证之前通过`HTTPS`提供服务，或者出于其他原因。无论你的理由是什么，Create React App 一如既往地为你提供帮助。

这是一个非常简单的设置。所需要做的就是在启动服务器之前将环境变量`HTTPS`设置为“true”。因此，不运行:

```
npm start 
```

在 Windows cmd 上运行:

```
set HTTPS=true&&npm start 
```

在 Powershell 上运行:

```
($env:HTTPS = $true) -and (npm start) 
```

最后在 Linux 和 macOS 上运行:

```
HTTPS=true npm start 
```

查看开发期间 HTTPS 设置的全部要点[点击](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#using-https-in-development)。

## 7。支持环境变量

在开发过程中，您会有一些不应该包含在脚本中的敏感信息。客户端密钥、客户端机密和其他内容最好存储在环境变量中，Create React App 通过将代码库中引用的环境变量替换为它们的实际值，再次帮助了我们。

你所需要做的就是在你的项目文件夹的根目录下创建一个`.env`文件，并定义任何你希望在你的文件中使用的变量，格式如下:

```
//.env  
REACT_APP_CLIENT_SECRET=client-secret  
REACT_APP_CLIENT_KEY=client-key 
```

这里的想法是在您希望定义的任何环境变量前面加上`REACT_APP`，Create React App 将在构建您的文件时用它的实际值替换它。

点击查看如何为生产和开发创建不同的环境变量[。](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-development-environment-variables-in-env)

## 8。支持最新的 JavaScript 标准

Create React App 支持一些最新的、最常用的 JavaScript 标准。Create React App 完全支持 ES6 语法以及其他一些实验性建议。

像`async / await`、对象扩散/静止属性这样的实验性提议是一些其他的也是开箱即用的。

要使用其他实验特征，如符号、承诺等，需要使用 [polyfill](https://github.com/facebook/create-react-app/blob/master/packages/react-app-polyfill/README.md) 。[聚合填充](https://github.com/facebook/create-react-app/blob/master/packages/react-app-polyfill/README.md)也由 Create React App 提供。他们从不停止帮助，是吗？

点击阅读更多关于当前支持的标准[。](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#supported-language-features)

## 9。一个生成依赖项

这是一个有趣的事实，而不是对你的发展有实际贡献的一点。Create React App 利用了 webpack、Babel 和其他软件，但在它们的基础上构建以提供统一的体验。这就是为什么我们安装了一个工具，我们得到了一个服务器，林挺，transpilation 和它旁边的休息。

## 10。驱逐

如果遇到这种情况，并且您认为您的项目中需要一些 Create React App 不支持的功能，您可以随时`eject`。也许您需要使用 TypeScript 进行静态类型检查，或者构建设置不够好。随时可以`eject`。

现在，退出意味着将所有 Create React Apps 配置复制到您的项目中，并将完全控制权移交给您。如果你走这条路，回去会很难，但不是不可能。

无论何时你准备好走这条路，只要跑`npm eject`就行了。但是请记住，强大的力量伴随着巨大的责任。

点击阅读更多关于弹射[的好处和危险。](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#npm-run-eject)

这些仅仅是 Create React App 为帮助开发体验所做的许多事情中的十件。浏览他们的官方 [`README`](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md) ，你可以发现 Create React App 提供的更多有趣的功能。我希望这里列出的一些东西实际上有助于使您的开发体验更容易和更容易。

* * *

**有关使用 React 构建应用程序的更多信息:**请查看我们的 [All Things React](https://www.telerik.com/blogs/all-things-react) 页面，该页面收集了大量信息和 React 信息的链接——包括从入门到创建引人注目的 UI 的热门话题和最新信息。