# Vuex 3.1.1 中的新增功能

> 原文：<https://dev.to/bnevilleoneill/what-s-new-in-vuex-3-1-1-1gi7>

[![](img/02bd64463a5119143479525774928362.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zRzaH1vB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZjKwrUuqSRS2OYFPf3gKHw.jpeg)

JavaScript 已经成为一个巨大的、多样化的、快速增长的编程世界的母语。一旦一个框架或库得到更新，社区就开始处理与之相关的项目——vue . js 及其周围的项目也是如此。

Vuex 因以简单直接的方式为州政府提供服务而赢得了声誉。然而，它仍然遵循其著名的父代的路径，这意味着我们有时会在已经使用最新版本 Vue.js 的环境中看到某些功能的延迟交付。

例如，原生移动应用遍布各处。许多 JavaScript 项目都有相应的库、框架等。来处理 app 开发。

Vue 也是一样: [NativeScript-Vue](https://nativescript-vue.org/) 是一个 [NativeScript](https://www.nativescript.org/) 插件，允许你使用 Vue 来制作你的移动应用。Vuex 还可以与 NativeScript-Vue 集成，用于处理组件的状态机，但它缺乏良好的调试机制。

Vuex 的 3.1.1 版本已经发布，旨在解决这个问题。除了支持在 NativeScript 应用程序中调试 Vuex，该更新还提供了其他改进，如包含 jsDelivr CDN npm 包，并修复了模块注册的内存泄漏等问题。这个版本没有突破性的变化，所以你应该放心升级你的项目。

您可以阅读 [changelog](https://github.com/vuejs/vuex/releases/tag/v3.1.1) 了解更多关于这些的信息，以及 PRs 和贡献者提交。让我们仔细看看。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

### 支持在 NativeScript 应用中调试 Vuex

[Vue-remote-devtools](https://github.com/vuejs/vue-devtools/tree/dev/shells/electron) (基于[电子](https://electronjs.org/))是官方的 Vue devtools 应用程序，用于使用 Vue.js 进行 NativeScript 应用程序开发。它基于 Node.js 和 Chromium，允许您轻松调试您的 HTML/CSS/JS 应用程序(在本例中为 Vue):

<figure>[![](img/8115989392e5d7aedb81b26b7c589f98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RwwcP3tY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/818/0%2Avf16fSsavNGhks6N) 

<figcaption>*应用举例。*</figcaption>

</figure>

另一方面， [vue-devtools](https://github.com/vuejs/vue-devtools) ，一个用于调试基于 web 的 vue 应用程序的浏览器扩展，以与 Vuex 及其状态管理库无缝集成而闻名，它允许您可视化以前的状态并在它们之间切换。这对于我们称之为时间旅行调试(即，您可以在状态之间来回切换)来说非常棒。

然而，对于 NativeScript 项目，在这个版本之前是不可能的。现在，[您可以在非浏览器环境中检查 Vuex 状态](https://github.com/vuejs/vuex/pull/1404)。如果你不太熟悉 Vuex 状态管理，你可以[在这里](https://vuex.vuejs.org/guide/state.html)了解更多。

### 为浏览器构建新的 minifier 和 ES 模块

在 [ES 模块](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)出现之前，开发人员通常会将 JavaScript 代码导入 HTML 页面，如下所示:

```
<script src="https://some-cdn.com/vue.js"></script>
<script>
  new Vue();
</script> 
```

Enter fullscreen mode Exit fullscreen mode

众所周知，这种导入 JS 库的经典方法也有缺点。每次进口的顺序必须正确；如果顺序不正确，一个接一个的脚本就找不到依赖关系。它还向全局名称空间公开对象和函数，因此任何人都可以访问和修改它们。您可能会错误地导入文件两次，这样的例子不胜枚举。

Vue 本身就面临这个问题。在 2.6 版本之前，它的构建过程中没有专门用于在浏览器中加载 Vue 模块的步骤。所以，这样的代码是行不通的:

```
<script type=”module” src=”vue.esm.js”></script> 
```

Enter fullscreen mode Exit fullscreen mode

最后，从 2.6 版本开始——因为现在每个人都在浏览器中使用 es 模块——Vue 与这个构建步骤捆绑在一起。你可以在这里阅读更多关于 Vue 如何工作的信息。

反过来，Vuex 也面临着同样的问题，这个问题在 3.1.1 版本中已经[修复。如果你想将 Vuex 直接导入你的浏览器，只需使用:](https://github.com/vuejs/vuex/pull/1533) 

```
<script type=”module” src=”vuex.esm.browser.js”></script>
// Or below for the min version
<script type=”module” src=”vuex.esm.browser.min.js”></script> 
```

Enter fullscreen mode Exit fullscreen mode

此外，这个版本还包括一个新的迷你器， [Terser](https://github.com/terser-js/terser) 。这是因为之前的 minifier UglifyJS 是[不再维护](https://github.com/mishoo/UglifyJS2/issues/3156#issuecomment-392943058)，只支持 ECMAScript 5，而 Terser 支持 ES6+。

实际上，对于开发人员来说，这不会改变任何事情。另一方面，因为 Terser 是 UglifyJS 的一个分支，所以它保持了对 uglify-es 和 uglify-js@3 的 API 和 CLI 兼容性的支持。

### 支持 jsDelivr

JsDelivr 确实比大多数 cdn 更快。[现在](https://github.com/vuejs/vuex/pull/1496)，你也可以[从这个 CDN](https://cdn.jsdelivr.net/npm/vuex@3.1.1/dist/vuex.js) 导入 Vuex。

### 使用 register module/unregister module 时修复内存泄漏

模块是将我们的商店分成更小、更可控的部分的好方法，因为整个状态包含在 Vuex 的一个大对象中。为了在存储中添加或删除模块，我们只需分别调用 store.registerModule 或 store.unregisterModule。

然而，在大型应用程序中控制如何管理浏览器的堆内存可能有点棘手。这里有一个已知的问题,涉及到一个应用程序添加数百个模块，然后立即取消注册，这导致堆内存没有被清除。你可以想象接下来会发生什么…内存泄漏。

[修复](https://github.com/vuejs/vuex/pull/1508)包括简单地对函数 resetStoreVm 中的 oldVm 实例进行垃圾收集，该函数负责在密集活动后恢复 VM。

### 结论

你可以在这里找到[所有的变化，亲自看看 Vuex 在最新版本和之前的版本中有什么改进。或者，如果你想了解更多关于 Vuex 的信息，考虑深入了解他们的官方](https://github.com/vuejs/vuex/releases)[入门指南](https://vuex.vuejs.org/guide/)。

社区是任何开源项目成功的关键——你也是其中的一部分。如果您发现最新改进有任何问题，[请前往报告](https://github.com/vuejs/vuex/issues)。

如果您还没有升级到最新的版本，下面的命令将帮助您升级:

```
npm install — save — save-exact vuex@3.1.1
// or via yarn
yarn add — exact vuex@3.1.1 
```

Enter fullscreen mode Exit fullscreen mode

好好学习！

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[Vuex 3 . 1 . 1 中的新特性](https://blog.logrocket.com/whats-new-in-vuex-3-1-1-d796b0c791bf/)最先出现在[日志博客](https://blog.logrocket.com)上。