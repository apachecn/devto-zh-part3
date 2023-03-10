# 使用 React lazy 和 React Suspense 进行代码拆分

> 原文：<https://dev.to/codingislove/code-splitting-using-react-lazy-and-react-suspense-4a1d>

[![React lazy](img/5095a74e09008741ed15041a4f13fb67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xmCitRGL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codingislove.com/wp-content/uploads/2018/12/REACT-LAZY.png)

初始载荷对于任何应用都是至关重要的。开发人员应该优化应用程序，以便应用程序即使在慢速网络上也能更快地加载。

作为开发人员，我们总是对添加第三方库感兴趣，这样我们就可以节省编写自己代码的时间和精力。在这之后，我们必须考虑包的大小。我们应该让我们的应用程序即使在添加了很多第三方库之后也能快速加载。

在 react 上工作了两年之后，我可以说使用 Webpack 或其他工具来优化你的应用有点困难。Webpack 是一个很棒的工具，它提供了许多优化应用程序的方法，但你需要花费大量的时间和精力来学习并在你的应用程序中应用它们。

## 代码拆分

代码分割是一种技术，它将大束分成小束，然后在需要时按需加载它们。

通常，在 JS 应用程序中，整个应用程序的 JS 被捆绑到一个 JS 文件中，并且在第一次打开应用程序时加载整个 JS 文件。这导致显示初始应用程序需要一些等待时间，这可以使用代码拆分来解决。

在 React 16.6 中，React 本身提供了一种简单的方法来优化您的应用程序，只需付出最少的努力。现在我要用 **React.lazy 和 React 向大家展示 React 16.6 中不使用任何工具的优化技术。悬念**

现在，我们将了解所有这些功能。让我简单介绍一下这两个特性。

## 做出反应。懒惰的

这个函数帮助我们只需更改一行代码就可以按需加载组件。我们需要做的就是用动态导入替换常规导入。

## 做出反应。焦虑

这用于用回退(加载指示器)包装延迟加载的组件。加载惰性组件时，会显示此后备组件。

## 懒惰和悬念的用法

这两个特性将帮助我们优化应用程序，当需要时加载组件，当道具与以前的道具相同时停止重新渲染。

通常，当我们向组件添加外部库时，所有的库都将被捆绑，并在第一次页面加载时被加载到浏览器中。当用户有良好的互联网连接时，这将起作用。网速慢的用户怎么办，网速慢的用户要等很长时间才能加载所有代码。

当您的应用程序有许多路径时，延迟加载非常有用。当用户点击特定路线时，我们可以加载每个路线代码。常规组件和延迟加载组件之间的唯一区别是我们导入它的方式。

ES6 导入动态语法允许我们动态导入 js 代码。React.lazy 接受回调。回调应该解析到模块。让我们看看如何动态导入组件。

这就是我们通常导入组件的方式。

[![](img/385e026776c3aa97b66b685d2fa06172.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lq-DXpKi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AIAiqMd7l_urjF2Jo)

这就是我们如何导入动态组件并将其传递给 React.lazy

[![](img/e696857f9c59747fcf7b14886c7bad6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HWk2aDZX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AzSPfqC6-698Bp48w)

做出反应。暂停将包装延迟加载的组件。在延迟加载的组件需要呈现之前，它不会呈现组件。这将减少束的大小。悬念将采取后备道具。后备属性用于在下载延迟加载的组件代码时显示加载器/微调器。

[![](img/9980eaf4b0cd1b59a7dc4646b5723332.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iletKe75--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2ALoo1vYLV5ZV4lS-D)

通过这段代码，OtherComponent 被 React.lazy 延迟加载。我们传递了一个回调函数，然后 OtherComponent 使用悬念包装，fallback 加载文本。

Fallback 接受任何 react 元素作为值。让我展示一个单页面应用程序的例子，它有 3 个带 lazy()和不带 lazy()的路由，看看它是如何优化包大小的。

## 演示时间

让我们来看一个小演示。在这个演示应用中，我使用 react-router 展示了如何按需动态加载单个路由的演示。

当我们在浏览器中加载 react router 应用程序时，所有路由将被一次加载，加载所有路由需要时间。有了 React.lazy 和悬念，我们会按需加载路线。

如果您看到下面的网络请求，第一个图像显示没有懒惰和悬念，这就是为什么捆绑包的大小是 705KB，第二个图像显示懒惰和悬念代码。现在，包的大小最初减少到 415 Kb，当您单击第二条路径时，将按需加载 280 kB。这就是 React.lazy 和 React .悬念的妙处。

[![](img/38213043071628f476d0d8aca6d55c29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cBX0Im9S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codingislove.com/wp-content/uploads/2018/12/Screen-Shot-2018-12-16-at-6.21.26-PM-1024x91.png) 

<figcaption>无反应.懒而有反应。悬念</figcaption>

[![](img/29b2b59000d765329d90937ab150f542.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q95hmFfq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codingislove.com/wp-content/uploads/2018/12/Screen-Shot-2018-12-16-at-6.22.09-PM-1-1024x86.png) 

<figcaption>同 React.lazy 和 React。悬念</figcaption>

[![](img/f725cd6b36826965c05bb4aad339006c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--96y8kTRB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codingislove.com/wp-content/uploads/2018/12/Screen-Shot-2018-12-16-at-6.25.30-PM-1024x56.png) 

<figcaption>按需加载第二条路线点击路线
</figcaption>

源代码可在[这里](https://github.com/dineshu07/How-to-use-React.lazy-and-React.Suspense)获得

## 结论

反应。懒惰和反应。悬念将帮助我们快速加载应用程序。我们可以推迟不需要的组件的渲染，我们可以减少包的大小 [![✌](img/eb8e50cbd0efe8df90c96925df0224e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XrBmMtpL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/270c.png)

使用 React lazy 和 React suspension 进行代码拆分的帖子[最早出现在](https://codingislove.com/code-splitting-react-lazy-suspense/)[编码是爱情](https://codingislove.com)上。