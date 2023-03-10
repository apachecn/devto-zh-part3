# 使用 package bundler 作为网络包的替代方案

> 原文：<https://dev.to/kendalmintcode/using-parcel-bundler-as-a-webpack-alternative-ofk>

我最近成为了蔻驰编码的导师，并发现许多开发斗争似乎就发生在第一个障碍上:项目建立。人们有很多伟大的想法，但发现很难开始，因为所有的语言选择，设计系统和一般项目设置。

根据您想要使用的内容，有很多选项，包括[创建 React 应用](https://facebook.github.io/create-react-app/)(用于基于 React 的项目)和捆绑器，如 Webpack，用于更通用的 JavaScript web 应用，或者如果您想要更多地控制项目的进出。

特别是 Webpack 非常棒，但是对于大多数现代 web 项目来说，要配置到一个合适的规格还需要很长时间。为了帮助解决这个问题，我创建了 [Webpack starter kit](https://robkendal.co.uk/blog/quick-start-javascript-projects-with-this-webpack-project-starter-kit/) ，它包含了常见的助手插件以及开发和生产环境的所有配置。

太好了，我听到你说，那么我们为什么要谈论[包裹](https://parceljs.org/)？

## 什么是包裹，为什么它是一个很好的 Webpack 替代品？

Parcel JS 是一个类似的 Webpack 替代产品，它提供了与 Webpack 相同的特性和功能，但方式更加简单，并且配置了更多开箱即用的“标准”选项。

例如，if 将自动从你的第一个起始文件中找到并链接 JavaScript 依赖项，并帮助捆绑常见的 web 资产，如图像和 CSS 文件；这是 Webpack 在开箱即用时无法做到的。

现在，包裹似乎不太为人所知，甚至有点地下化，但它作为 Webpack 的真正替代品正在受到越来越多的关注。

Parcel 真正大而致命的特点是它提供了一个非常网络项目友好的开箱即用配置，几乎不需要任何 faff。

包裹的[文档非常棒:简单明了，非常值得一查。](https://parceljs.org/getting_started.html)

[![Workbench filled with tools](img/76ad83cc402f787a9ba42543ff8416af.png "Image credit to Cesar Carlevarino-aragon via Unsplash")](https://res.cloudinary.com/practicaldev/image/fetch/s--EHtMmqS2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://robkendal.co.uk/static/177f36b2898755109aa1ba6b6644ceb0/971c4/cesar-carlevarino-aragon-778069-unsplash.jpg)

## 包裹入门

包裹对于你的下一个项目来说再容易不过了。事实上，开始行动只有四个步骤:

1.  安装包裹
2.  创建一个`package.json`文件
3.  创建起点/入口点(如`index.html`)
4.  对文件调用 parcel 命令

前两点可以这样实现:

```
/* add it globally, so it's available to all your projects */
yarn global add parcel-bundler 
```

Enter fullscreen mode Exit fullscreen mode

并初始化项目，添加一个 package.json 文件。

```
yarn init -y 
```

Enter fullscreen mode Exit fullscreen mode

现在，在第 3 步，我们可以创建两个文件来做一些有用的事情。首先，一个 starter html 文件

```
/* add this to your project root as index.html */
<html>
  <body>
    <main id="app"></main>
    <script src="./index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要一个初始的 JS 文件来做一些工作:

```
/* again, add this file to your project root as index.js */
const myName = { 
  firstName: 'Parcel',
  lastName: 'Tastic'
};
document.querySelector('#app').innerHTML = `Hello there, ${myName.firstName}  ${myName.lastName}`; 
```

Enter fullscreen mode Exit fullscreen mode

最后，使用 package 命令:
开始这一切

```
parcel index.html 
```

Enter fullscreen mode Exit fullscreen mode

也就这样了(暂时)。Parcel 很方便地包含了一个开发服务器(您可以浏览到`http://localhost:1234`来查看上面代码的输出),所以您应该会看到上面呈现的类似于我的代码沙箱实例的输出:

[https://codesandbox.io/embed/8z4vzk10p8](https://codesandbox.io/embed/8z4vzk10p8)

## 介绍包裹 JS 入门套件

使用上面四个简单的步骤，你可以在 10 分钟内让一个项目起飞。然而，如果你想要更多的东西，比如使用 SASS 或更新的 ES6 语言特性，你需要一些额外的配置来开始。

这就是为什么(连同我的 [Webpack starter kit](https://robkendal.co.uk/blog/quick-start-javascript-projects-with-this-webpack-project-starter-kit/) )我为那些对使用 package 感兴趣的人创建了一个类似的 starter 项目。

### 套件包含什么？

老实说，不是很多。因为 package 非常有用，而且开箱即用，所以这个 starter 项目只是在我们在本文前面介绍的基本文件的基础上增加了一些好的东西。

具体来说，在整个项目中，它包括:

*   一切开始的地方。这个文件被设置为您的`package.json`文件中的主入口点，在这里，Parcel 根据提示加载它从那里找到的资源。这个基本的 HTML 起点增加了一些细节，比如移动视窗元和“无脚本”标签。
*   如果你现在想使用更新的 JS 语言特性，你需要 Babel。Babel 已经作为一个依赖项安装在项目中了，但是您需要在项目根目录中使用这个小的配置文件来启用 Babel goodness。
*   除了通常的项目信息，这里没有什么特别的东西。但是，它包含一个用于在本地运行开发服务的`start`命令和一个用于打包您的项目并将其添加到`/dist`文件夹中进行部署的`build`命令。
*   `src/index.js` -包裹在打包旅程开始时寻找的第一个文件。Parcel 将从这里开始，查找其中引用的任何其他文件，并根据需要加载和捆绑它们。
*   一个简单的 SASS 文件的起点。它添加了一个默认的字体系列和字体大小，但应该用作项目的其余样式文件的起点。
*   `/dist` -该文件夹包含(或将包含)包裹输出的压缩、打包文件，遵循生产构建命令-可从`package.json`文件作为`yarn build`获得。

### 使用宗地起始项目

我试图让这个入门项目超级简单易用——就像 package 本身一样！你需要做的只是另外四个步骤:

1.  叉回购(此处可用:[https://github.com/bpk68/parcel-template](https://github.com/bpk68/parcel-template))或下载文件
2.  运行`yarn install`
3.  编辑你喜欢的任何东西
4.  运行`yarn start` -你的浏览器应该打开类似[的内容](http://localhost:1234)

仅此而已。超级简单。

## 需要别的吗？尝试 createapp.dev

除了本文中的[package starter kit](https://github.com/bpk68/parcel-template)之外，还有一个叫做 Create App 的[便利工具，它包括一个包项目设置。本质上，如果您的需求更具体一些，或者您希望从一些不同的东西开始，您可以使用 GUI 检查一系列选项来配置 Webpack 或 Parcel 项目。](https://createapp.dev/parcel)

绝对值得花些时间调查！