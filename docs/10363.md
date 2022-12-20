# 如何使用 TypeScript 创建 React 项目

> 原文：<https://dev.to/apium_hub/how-to-create-a-react-project-with-typescript-44lh>

在我的上一篇文章中，我们看到了如何开始[将 JavaScript 项目迁移到 TypeScript](https://dev.to/apium_hub/a-simple-way-to-migrate-from-javascript-to-typescript-574h-temp-slug-2755564) ，以一种递增的方式，而不需要付出巨大的努力或中断更改。现在，我想谈谈**在一个新的前端项目**中使用 TypeScript。更具体地说，我将向您展示**如何用 TypeScript** 创建一个 React 项目。

## 如何用 TypeScript 创建 React 项目

正常情况下，一谈到 frontend 和 TypeScript，马上想到的是**Angular**；而 React 通常与 **JavaScript** 联系在一起。因此，即使在寻找关于 React 的教程或任何问题解决方案时，大多数示例和解决方案都来自 JS。为了改变这一点，我们准备了一个使用 **React-Redux 和 TypeScript** 的应用程序的工作示例。

如果你不是 react 新手，你很可能熟悉 [create-react-app](https://github.com/facebook/create-react-app) ，这是一个由脸书直接开发的 React 项目启动器，它让我们有机会在几乎没有时间的情况下启动一个应用程序，因为它隐藏了大多数构建配置。如果没有，不妨试一试，尝试用它制作一些简单的应用程序。

然而，如果你想要同样的东西，这一次是使用 TypeScript，你可以检查[这个伟大的项目启动器](https://github.com/Microsoft/TypeScript-React-Starter)做同样的事情(事实上，它也使用 create-react-app)，但是使用 TypeScript。您可以阅读随它提供的自述文件，了解更多关于 React、Redux，当然还有 TypeScript 的知识。

然而，它并没有涵盖启动一个完整功能项目所需的所有组件。因此，我们准备了一个包含更多功能的项目(并添加了一些常见的依赖项，例如 *react-router-dom* )，从中您可以了解更多关于 react 如何工作的信息:

[React–TS 演示](https://github.com/ropre4/react-ts)

(您可以克隆该项目并按照简短的自述文件运行它)

这个简单的应用程序具有开发人员从头开始创建项目时遇到的最常见的功能。因此，你可以把它作为一个例子或一个起点，从这里你可以做出自己的决定或寻找替代方案；主要部件包括:

### 1。按指定路线发送

–我们使用[react-router-DOM](https://www.npmjs.com/package/react-router-dom)

–“routes . tsx”显示如何使用路由以及如何使用 URL 中的参数

–“item . page . tsx”显示如何从 URL 接收参数

### 2。SCSS 支持

-我们使用[节点-sass-choki Dar](https://www.npmjs.com/package/node-sass-chokidar)

--“navbar . scss”是 sass 文件
的一个示例-“navbar . component . tsx”是如何从上面的文件导入样式的一个示例

### 3。将存储的状态和调度绑定到一个组件的道具

-'list.container.ts '就是这种绑定的一个例子

### 4。加载程序标志

-使用“item.actions.ts”中的操作创建者，我们在我们的商店状态中设置一个布尔值作为“item.reducer.ts”中的标志，以表示应用程序加载

的状态-“list . page . tsx”使用该值根据标志值以不同方式呈现 UI

### 5。对后端的 Http 请求

-'item.service.ts '包含对后端的所有请求并返回项目实体

-在我们的应用程序中，我们使用项目对象(参见' item.ts ')而不是普通的 JS 对象

### 6。测试

-我们使用[jest](https://www.npmjs.com/package/jest)[链接:【https://www.npmjs.com/package/jest】T2
T5】-你会在文件夹“/tests”下找到测试不同层的例子

### 7。以打字打的文件

-没有单曲。项目中的 js 文件

该回购的目的是提供一个足够大的项目，以涵盖前端应用程序的主要概念，并提供一个沙箱，在其中尝试和试验所使用的技术和库，同时添加新功能或更改和改进现有功能。希望它的复杂性足够简单，允许任何人用最少的努力和研究就能理解这些概念。

帖子[如何用 TypeScript](https://apiumhub.com/tech-blog-barcelona/create-react-project-typescript/) 创建 React 项目最早出现在 [Apiumhub](https://apiumhub.com) 上。