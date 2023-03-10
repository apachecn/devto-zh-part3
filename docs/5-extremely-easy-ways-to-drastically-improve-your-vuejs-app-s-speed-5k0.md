# 5 个非常简单的方法来大幅提高你的 VueJS 应用程序的速度

> 原文：<https://dev.to/veebuv/5-extremely-easy-ways-to-drastically-improve-your-vuejs-app-s-speed-5k0>

一点背景，我叫 Vaibhav——来自 Five2One。我参与 VueJS 已经有 2 年了(自从 1.0 版本发布以来)，基本上帮助构建/训练了悉尼 3 个最大的 VueJS 代码库。为数百万人服务的代码，所以我的工作不仅仅是构建可伸缩的代码模式，而是真正关心 ALOT 的性能。

我们通常会看到小型的初创项目和代码库，目标是推出代码并快速将产品提供给客户，这是我们擅长的——你可以看看我们在 five2one.com.au 的工作，但除此之外，作为工程师，我们的目标是关注性能和可扩展性。

让我们直接进入主题——讨论一些可以提高 VueJS 应用程序性能的简单方法。

## 数字 1

[![](img/7fe32669282c2c831e6cc2e4cdefbd9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gew9EAWz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AQWK-hGCtQo0XUV-0_2a-iw.png)

这里我们有一个“功能”模板，因为它没有声明的状态，只处理道具。这可以通过使用渲染方法[https://vuejs.org/v2/guide/render-function.html](https://vuejs.org/v2/guide/render-function.html)很容易地创建成一个基于 Vue 的功能组件

如果你仔细阅读，你会看到被传递的道具叫做`functional: true`

下面是解决这个问题的一个简单方法

[![](img/bccbc0221d7ff0ea05d7e2dd9a665e39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w0dL8fcw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AeO6TGOdLcs8we1NGzp3BMw.png)

就这么简单，你不需要担心改变你的模板语法，你可以坚持下去，仍然享受 Vue 语法的豪华。

快速编辑:因为它是一个功能组件，所以它的上下文是不存在的，所以要访问 props，你需要做 props . name——感谢 Dinesh Madhanlal 的提醒

## 秒易提示

对动态加载的组件使用保活。

[![](img/d57dfad14ad0e012100cfb975d2c5159.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--03cnJv4R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AOeATejWeqKvcR4t3EWqrOw.png)

有时我们使用 Vue 提供的`is` prop 动态加载组件，我们可能会在动态加载的组件之间切换。为了让我们在组件切换时维护状态并防止重新加载数据，无穹顶包装器是一个很好的加速解决方案

[![](img/79a861be0363d41186fa5affd775615f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gmWzid8N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ArNPQUvMGJFNSnEqIa_dU0Q.png)

## 第三易提示

鉴于 Vue 的 vDOM 系统是如何运行的，这一点对大多数人来说可能更明显一些。vDOM 的目标是充当中间更新媒介，跟踪(非常有效地)对项目 UI 的隔离更改，并触发对那些目标组件的隔离重新呈现，而不是实际重新绘制整个屏幕。

[![](img/894133b9e46c808b21ab12ead457ac9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4VH2qq-W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AYpXzK5bvJx23JYSIRUH_DQ.png)

很多时候，我们可能会创建一个组件，该组件有一个包装器，该包装器会大量地重新呈现，而同一模板的其他部分必须在模板的其他部分重新呈现时做 a lot 工作，一个简单的解决方法是简单地将文件组件化。除非子进程依赖父进程写入数据，否则它应该运行良好。

[![](img/804f8fe5e871662d6c1c4faa08e12764.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3VZRthbB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ApLTTKdcuIgnp5ZNtRszs-w.png)

## 第四易提示

在 CTA 事件中使用匿名函数。每当一个匿名函数被传递给按钮的“onClick”时，我在 React 的开发人员中见过这种模式，因为这是 React 传递自定义数据给函数的方法之一，最好不要传递匿名函数。原因是这样的。

以下面这个例子为例

[![](img/698ec6daea81330f5ba8292abf058a93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hjd4fRDJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A0I7js8vkbd8e8ECJciGvRA.png)

这里发生的事情是，每次 div 的长度增加(一个简单的进度条)时，所有的按钮都会重新呈现。从技术上讲，它们不应该是，因为它们没有任何变化，对吗？没有道具更新或没有数据更新等。

这是一个快速捕捉，JS 与内存中的匿名函数进行交互，即每次重新渲染发生时，都会创建一个匿名函数的新实例，而 diffing algo 会将其作为新的道具，因此即使不需要也要重新渲染按钮。

幸运的是，Vue 太神奇了，它足够聪明地理解任何自我调用的函数都不应该被调用，直到它所附加的事件被触发，所以即使它是一个 IIF，Vue 也会使它成为一个 thunk，从而延迟执行。

[![](img/d74e6e701149aebb0faf265b81fc2bc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8n_C6Vrb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AgT1_5cDXq4G-Q63eXoYF2A.png)

如果你想更安全，总是值得创建一个返回另一个函数的闭包，因此包装函数只有一个实例，不会导致重新呈现。

## 神奇的 5 号简易提示

这也是一个简单的方法，这方面存在灰色地带，不是一个全面的解决方案。只有在页面上有很多附属内容并且切换组件显示很快时才使用这种方法，

是的，我说的是 v-if 或 v-show 的用法。这两者之间有着巨大的差异。V-if = false 从不将组件呈现为。因此，如果该组件在短时间内切换多次，将会影响性能，因此在这种情况下使用 v-show 效果非常好。

然而，问题是，在你向一个组件添加 v-show，并且该组件需要对它所呈现的`first time`进行繁重的操作的情况下，无论 v-show 是真还是假，该操作都将被执行，值得通过使用 v-if 来延迟它，直到该组件被实际需要。记住 v-show 只设置一个组件的 CSS 显示值来显示:none，组件仍然是“渲染”的。

然而，即使这个组件有很重的初始工作负载，如果它不断切换，并且每次都需要执行那个方法，那么最好还是做一个 v-show。这一切都归结于用户需求。

希望这对大家有帮助！

如果你喜欢这个，一定要跟着我看类似的东西:

推特:twitter.com/[@ veebuv](https://dev.to/veebuv)
LinkedIn:linkedin.com/in/vaibhavnamburi
insta gram:_ veebuv