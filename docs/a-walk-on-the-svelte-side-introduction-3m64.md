# 苗条的一面:简介

> 原文：<https://dev.to/wendell_adriel/a-walk-on-the-svelte-side-introduction-3m64>

## 代码共享

我有一个名为 **[CodeShare](https://codeshare.com.br)** 的项目，用葡萄牙语创建免费的高质量内容，让更多巴西人有机会学习英语。

所以当我在那里写一篇文章时，我也会在这里发表，但是用英语(当然)，所以如果你想查看这篇文章的原始版本(葡萄牙语)，你可以在这里查看！

## 简介

你进入这篇文章可能是因为你在某个地方听过这个名字，但不知道它是什么，或者你已经知道了，但你想知道更多，或者你可能只是问自己类似于**“如果我已经有了 React 和 Vue，为什么我还需要另一个反应式 UI 库？”**。所以，不管你来这里的原因是什么，做好准备，因为我们要开始一次冒险:**走在苗条的一边**是来自 **CodeShare** 的新系列文章，在这里我将展示什么是苗条，它的主要特性，我们将在这个系列中构建示例，最终你将准备好开始在你的项目中使用这个库！

这第一篇文章将更理论化，但知道**苗条**与其他 libs 有什么不同真的很重要。我建议您仔细阅读，以便我们可以在之后深入研究代码。

## 什么是苗条

**Svelte** 像其他 UI 库一样，基于组件，但有一个本质上非常重要的区别:它不使用**虚拟 DOM 区分**像 **[React](https://reactjs.org)** 或 **[Vue.js](https://vuejs.org)** 因为这个库使用声明性结构，这些结构被转换成 DOM 操作，可以稍微过载我们的应用程序的框架以及 **[垃圾收集器(葡萄牙语)](https://codeshare.com.br/garbage-collector-no-javascript)** 。

但是苗条的 T1 是如何工作的呢？所有的工作都是在构建时完成的，这样它会将你的所有组件转换成一个高性能的命令式代码，应用尽可能少的 DOM 操作，让那个**苗条**从你的最终代码中完全“消失”。您将拥有一个纯粹的、高性能的命令式 JavaScript 代码。我们可以突出**苗条**的一些重要方面:

*   非凡的表现
*   小捆
*   库中包含的可访问性方面
*   默认情况下样式封装/隔离
*   库中包含的声明性转换
*   学习曲线

最后，我们问题的答案:**“什么是苗条？”**可以回答说是编译器，因为它的工作就是把组件编译成命令式的 JavaScript 代码。

## 对苗条身材的反应性

Svelte 版本 3 于 4 月 21 日发布，并对 lib 进行了巨大的修改，使编写的代码更加清晰，并改善了组件的反应性。例如，在这个版本之前，要更新我们组件的状态，我们需要类似于:

```
const { clicks } = this.get()
this.set({ clicks: clicks + 1 }) 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经使用过 **React** ，你会注意到我们如何在基于类的组件上处理状态有很大的相似之处(在著名的 **[钩子](https://reactjs.org/docs/hooks-intro.html)** 推出之前):

```
const { clicks } = this.state
this.setState({ clicks: clicks + 1 }) 
```

Enter fullscreen mode Exit fullscreen mode

随着**钩子**的推出，**对组件状态做出反应**的方式发生了显著的变化，一些其他库开始创建他们自己版本的**钩子**。 **Svelte** 不想走这条路，因为在幕后他们给 **[垃圾收集器(葡萄牙语)](https://codeshare.com.br/garbage-collector-no-javascript)** 制造了一些过载，如果你需要在**嵌入式设备**上运行这段代码，或者如果你的应用依赖于许多基于动画的交互，这会导致一些问题。

那么**苗条 3** 是如何处理反应和更新组件状态的呢？以一种非常简单的方式，不使用**代理**或类似的东西。当我们想要改变一个名为`clicks`的状态变量时，我们只需要更新它:

```
clicks += 1 
```

Enter fullscreen mode Exit fullscreen mode

由于 **Svelte 的**工作是在应用程序构建时完成的，作为一个编译器，它只能执行这些更新的检测，而没有任何额外的复杂性。在窗帘后面他做的是:

```
clicks += 1
$$invalidate('count', count) 
```

Enter fullscreen mode Exit fullscreen mode

## 表现在身材苗条上

与其他库如 **React** 和 **Vue** 的主要区别之一是它不使用**虚拟 DOM** ，但是你一定在想:不使用**虚拟 DOM** 它怎么会这么快？如果你使用**前端**(或者甚至使用后端，但是喜欢阅读)，可能你已经听说过**虚拟 DOM** ，并且使用它比使用**真实 DOM** 更有性能。

但是什么是虚拟 DOM？库是如何使用它的？简单地说，**虚拟 DOM** 只是一个定义页面基本结构的 JavaScript 对象。当应用程序的状态发生变化时，例如，一些属性的值被更新，它会创建一个新的对象，库的工作是找出新旧对象之间的差异，并在**真实 DOM** 中应用最少数量的更新。

实际上，如果不首先比较**虚拟 DOM** 的两种状态，就无法对**真实 DOM** 进行任何更改，但是这会导致一些不必要的步骤。 **Svelte** 作为一个编译器工作，在应用程序构建时它已经知道如何改变应用程序的状态，因此它生成最少的可能步骤来管理这种可能的改变，而无需在应用程序执行期间做任何工作。

## 打造我们的第一个超薄组件

苗条的主要特征之一是让我们以简单的方式构建应用程序，编写更少的代码。代码越长，理解它的努力就越大，出现错误的机会也就越大，所以当我们编写更少的代码时，我们的好处是能够更快地理解它，并引入更少的错误。

让我们创建我们的第一个组件，它会非常简单。我们将有两个文本字段，我们可以分别提供我们的名字和姓氏，它将在屏幕上显示我们的全名欢迎消息。**细长的**组件使用`.svelte`扩展，我们声明:

*   我们的组件在一个`<script>`标签中使用 JavaScript 的行为

*   CSS 在`<style>`标签内的组件样式，我们在组件上声明的样式对该组件有一个限制范围，这意味着如果你创建一个规则来改变组件的`<p>`标签的样式，它不会影响组件外的任何其他`<p>`标签

*   我们的 HTML 组件的结构，不需要像我们在 **Vue** 上那样把这个结构封装在`<template>`标签里，也可以有几个根元素，除了 **React** 我们可以只返回一个元素或者使用 **[片段](https://reactjs.org/docs/react-api.html#fragments)** 。

首先，我们有组件的行为部分，这将是一个完全正常和基本的 JavaScript 代码。我们将创建两个变量，并定义一个函数，我们将在定义组件结构时使用这个函数:

```
<script>
    let firstName = ''
    let lastName = ''

    function showGreeting () {
        window.alert(`Welcome, ${firstName}  ${lastName}`)
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

下面我们也将定义我们的组件的样式，它也是一个普通的 CSS 代码，唯一的不同是这里声明的样式将只影响这个组件的元素:

```
<style>
    * {
        font-family: sans-serif;
    }

    p {
        font-weight: 700;
    }

    .warning {
        color: #ff2b56;
    }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

组件的最后一部分是组件的结构，这是用 HTML 完成的，只有一些小细节，我们将在后面检查:

```
<label for="first_name">First name:</label>
<input id="first_name" type="text" bind:value={firstName}>

<label for="last_name">Last name:</label>
<input id="last_name" type="text" bind:value={lastName}>

{#if firstName.length > 0 && lastName.length > 0}
    <p>Hello, {`${firstName} ${lastName}`}</p>
    <p>
        <button type="button" on:click={showGreeting}>Show alert greeting!</button>
    </p>
{:else}
    <p class="warning">Type your first and last name above...</p>
{/if} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们的结构中有一些不是 HTML 代码的细节，但是用来连接组件的结构和它的行为。在我们的`<input>`元素中，属性`value`被更改为`bind:value={variable}`，在这里我们链接了组件的一个状态变量，这样当这个变量被更改时，这个更改将反映在`<input>`元素中，反之亦然。

正如链接状态变量很容易一样，当事件在 DOM 中发生时调用我们定义的函数也很容易，我们使用`on:event={function}`。在我们的代码中，当按钮收到点击时，它将调用上面定义的`showGreeting`函数。

通常我们需要根据组件的状态来显示或隐藏一些内容， **Svelte** 为我们提供了一个简单的方法来实现这个功能:`{#if} {:else if} {:else} {/if}`，这样我们可以用一种简单而干净的方式来控制什么时候显示和隐藏一些内容。

我们的例子可以在下面的**代码沙箱**中看到:

[https://codesandbox.io/embed/84ljk782w0](https://codesandbox.io/embed/84ljk782w0)

正如你在上面的**代码沙箱**中看到的，我们有一个`index.js`文件，它导入我们的组件并在`<body>`中呈现它。这个文件连同`package.json`文件和[卷](https://rollupjs.org)卷的配置，**苗条**使用的默认捆绑器，`rollup.config.js`已经包含在**苗条**的默认模板中。

你可以在 **CodeSandbox** 上玩，不需要在本地创建项目，但是如果你想的话也很容易。我们可以使用 **NPX** 以一种简单的方式创建我们的项目:

```
npx degit sveltejs/template my-app && cd my-app && yarn 
```

Enter fullscreen mode Exit fullscreen mode

这样，您将使用名为 **Degit** 的工具创建一个项目，该工具将创建一个 **[这个库](https://github.com/sveltejs/template)** 的副本，这是一个应用程序的最小默认模板，具有所有需要的配置。如果您喜欢或者想要对这个默认模板进行任何更改，您可以**fork****[this repository](https://github.com/sveltejs/template)**并更改上面的命令以使用您的 Github 用户:

```
npx degit your-github-user/template my-app && cd my-app && yarn 
```

Enter fullscreen mode Exit fullscreen mode

在这个模板的`README.md`文件中，你可以找到如何在本地运行你的项目的说明，以及如何部署你的应用的说明。

## 结论

在这第一篇文章中，我们看到了什么是**svelite**，它的主要特点，我们建立了我们的第一个组件，看看它是如何工作的。在接下来的文章中，我们将更深入地讨论这个库的其他特性，同时我们将创建一些例子来实践我们将要学习的概念。

我希望你喜欢这篇文章，如果你喜欢，不要忘了评论并与你的朋友分享这篇文章！！！再见！😎