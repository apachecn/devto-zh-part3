# Web 组件 vs. React 这不是关于 Web 组件 vs . React

> 原文：<https://dev.to/steveblue/it-s-not-about-web-components-vs-react-5137>

你经常会听到工程师用 React 来比较 Web 组件。以下是我对此的看法。

### Web 组件是 spec。你的网络框架不是。

事实上，我们不需要 JavaScript 框架来使用 Web 组件是一个巨大的区别。Web 组件规范定制元素 v1、模板、插槽和 ShadowDOM 的部分都在浏览器中工作。这意味着在开始编写 Web 组件代码时不会有依赖性的争论！

[![](img/7c632ca4898796b4b8f263103dda4708.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K_Hqpcac--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cdmrq1jzjxci8ce1vpv7.png)

你不需要大量的 JavaScript 来呈现用户界面。Web 组件对于加载大量依赖关系会阻碍用户交互的情况是有益的，比如在移动设备中。然而不仅仅如此。原来[web 组件的静态站点生成只需要最少量的工具](https://github.com/readymade-ui/readymade/blob/master/build-gh-pages.js)。HTML 模块将使处理模板部件的人机工程学变得更好。今年 Chrome 团队宣布[HTML 模块规范](https://groups.google.com/a/chromium.org/forum/#!msg/blink-dev/ewfRSdqcOd8/w_Fr6rJ3DQAJ)即将出台！虽然您可能需要一个 JavaScript 框架来构建复杂的 web 应用程序，但是静态站点最好只使用规范来构建。考虑到网络中有多少是静态内容，这是一件大事。抱歉，反应过来，盖茨比。

### Web 组件包含在内。

JavaScript 生态系统是如此的支离破碎。Web 组件是每个人说同一种语言的机会。React 的支持者很快指出“没有单一的 web 组件社区’,但事实是这个社区是我们所有人的。JavaScript 框架是排他的。你必须成为俱乐部的一员。对于定制元素 v1 来说，情况并非如此。初学者可以开始编写代码，而不用像学习 JavaScript 库那样费劲。

### Web 组件解决了一个不同的问题。

React 解决了 UI 开发中缺少状态的问题。自定义元素是无状态的。您需要为定制元素引入一个状态机。JavaScript 框架总会有一席之地。Web 组件提供了一个机会来简化这种组件范式，这种范式在过去几年里一直存在，因此每个人都可以使用相同的 UI 组件。当扩展我们日常使用的基本元素时，Web 组件本质上解决了人类工程学的问题。React 文档中也是这么说的。 React 和 Web 组件解决不同的问题。

### Web 组件是可互操作的。很可能您的 Javascript 框架不是。

在框架中使用定制元素不仅是可能的，而且应该被认为是 2020 年的最佳实践。Javascript 框架应该能够处理定制元素，就像它们必须处理 DOM 一样。让 web 组件与 JavaScript 框架一起工作有很多奇怪的地方。React 有一些问题，但是 React 团队说[他们会考虑与规范](https://dev.to/ben/why-the-react-community-is-missing-the-point-about-web-components-1ic3/comments)紧密集成。Angular 不支持定制内置元素的部分规范。Angular 提供了对自主定制元素的支持，甚至使用 Angular 元素来编译它们。Vue 与自定义元素有很好的互操作性，但是如果你遇到了问题，请在这里与使用 Vue 编码的人联系。有一些变通办法可以解决集成 Web 组件时出现的问题，但是 JavaScript 框架需要做一些工作来确保完整的定制元素 v1 规范能够正常工作。

### Web 组件是 a11y。

定制的内置元素允许您扩展本机元素，如按钮、输入、选择和实现您自己的功能。通过扩展具有可访问性特征的元素，您可以免费获得这些特性。通常，在用 JavaScript 框架构建 UI 组件时，您必须复制可访问性特性。

### 教育和 web 工程有问题。

在过去十年中变得流行的代码学院中，我们忘记了详述 spec，而是直接进入 JavaScript 框架。即使是入门水平的课程也可以包括接触反应。这对于只希望我们“使用平台”的 JavaScript 框架来说是有利的，但它留下了一个空白，初级工程师缺乏我们都使用的平台的知识:HTML、CSS 和 JavaScript。Web 组件通过提供每个人都可以学习和使用的规范来解决这个问题。

## 我们大家就不能好好相处吗？

即使对初学者来说，学习 HTML 规范也很容易。让每个浏览器和库都同意并支持 spec 是很难的。HTML 是一个活的文档，并且随着时间的推移不断变化。[苹果拒绝](https://github.com/w3c/webcomponents/issues/509#issuecomment-230700060)在 Safari 中实现定制的内置元素规范。自从微软在 Edge 中迁移到 Chromium 以来，所有最流行的 evergreen 浏览器都实现了自定义元素 v1。我们只需要更好地合作，Web 组件可以为实现即将到来的 2020 年代的 UI 组件提供一个可持续的范例。

[![](img/1a39addee9a6b6b8058bac3a199986d5.png)](https://i.giphy.com/media/3o7qE1EaTWLQGDSabK/giphy.gif)

这并不意味着你现在不能开始使用 Web 组件。对于不支持该规范的浏览器，存在多填充。

我们应该共同努力，确保 web 组件进入开发工作流。从最开始的前端 web 开发教育到最复杂的 web 应用程序，web 组件都有可能简化 UI 组件开发，提供现成的可访问性功能，甚至减少构建 UI 所需的代码量。是时候释放这种潜力了，而不是争论哪个 JS 框架更擅长提供组件范例。JavaScript 框架将继续擅长它们最擅长的:状态管理，提供架构模式和方便的工具。Web 组件将继续存在。我们可以一起努力让网络变得更好。