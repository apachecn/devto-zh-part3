# 超越 React、Vue 和 Angular 的世界

> 原文：<https://dev.to/areknawo/the-world-beyond-react-vue--angular-3ah0>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

如果你对 web 开发感兴趣，我敢打赌你一定听过或读过类似于*“React vs Vue vs Angular”*的比较。只是太普通了，绕不过去。这些 **MVC 框架**和 **UI 库**变得如此流行，以至于你可以将它们与几年前的 jQuery 流行程度相提并论。当你在构建一个复杂的 web 应用程序，或者在类似的地方工作时，你几乎肯定会遇到或者将要遇到这些工具中的一个。这就是为什么在这篇文章中，我想启发你探索巨大的 JS 生态系统，尝试一些其他的库，并且在这样做的过程中，拓宽你的视野、JavaScript 知识，并且在途中遇到一些新的技术和模式。但首先，让我们探讨一下这三大巨头的一些基本概念，好吗？

# [做出反应](https://reactjs.org/)

我认为这个不需要介绍。React 是脸书开发的 UI 库，利用了所谓的 **JSX** 。它基本上是标准 JS 语法和 HTML 在一个文件中的结合——非常简洁。

```
const element = <h1>Hello, world!</h1>; 
```

Enter fullscreen mode Exit fullscreen mode

但我打赌你很可能已经知道了。我这么说是因为 JSX 语法是 React 的主要卖点和概念之一，由于这个库的持续流行，它激发了许多下面列出的项目。此外，像许多其他人一样，React 基于虚拟 DOM 的概念。通过这种技术，它允许你创建组件——构建块——来创建你的 UI。这些组件可以有自己的状态，可以处理复杂的数据结构，这些数据结构不能用于标准的 DOM。

```
class ButtonClickCounter extends React.Component {
    constructor(props){
        super(props)
        this.state = {clicks: 0}
        this.handleClick = this.handleClick.bind(this);
    }
    handleClick(){
        this.setState({
            clicks: this.state.clicks + 1
        });
    }
    render() {
        return (
            <button onClick={this.handleClick}>
                Clicked {this.state.clicks} times!
            </button>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这也是 React 出色性能背后的原因，即使它有一套令人印象深刻的特性和 API 供您使用。自然地，**对 v16 及以上版本**作出反应，仅仅证明该陈述是正确的。

# [视图](https://vuejs.org/)

Vue 是一个众所周知的渐进式 JavaScript UI 框架。它也是基于虚拟 DOM 的，但是它的实现与 React 有很大的不同。众所周知，它的优化方式略有不同。它针对效率进行了优化，这意味着它只更新那些真正需要的 DOM 元素。代替 JSX，Vue 放置了它的**模板**。这些特性提供了易于使用和易读的语法来创建您的 UI。Vue 还采用了基于组件的方法来实现更好的可重用性。通过将组件和模板组合成所谓的 Vue **单个文件组件**，你可以获得良好的开发体验。

```
<template>
  <div class="example">{{ msg }}</div>
</template>

<script>
export default {
  data () {
    return {
      msg: 'Hello world!'
    }
  }
}
</script>

<style>
.example {
  color: red;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

对于那些支持关注点分离的人来说，这是一个更好的解决方案。在这种形式中，您还可以使用像作用域样式等功能。应该指出的是，Vue 也包括对 JSX 的支持，但它在 Vue 生态系统中没有任何大的人气。最重要的是，Vue 通常被认为**更容易学习**，以至于它真的被推荐给需要正确快速完成工作的小型团队。

# [棱角分明](https://angular.io/)

Angular 是另一个流行的 web 框架，这次来自 Google。要知道，这里我说的是**角度 v2 和向上**，而不是**角度**。所以，Angular 是一个真正的框架。与上面两个不同，它有**路由**、 **i18n** ，并支持所有其他开箱即用的东西。这就是为什么它的体积要大得多。对于您来说，这个工具的一个决定性事实是，它是基于 Typescript 构建的。换句话说，你必须接受这种语言才能使用 Angular v2 和更高版本。对一些人来说，这可能是一个艰难的决定，但它可能是值得的。Typescript 的使用给表带来了类型正确性和许多现代 ES 特性，比如在所有角度代码中使用的 decorators。Angular 使用了众所周知组件、虚拟 DOM 和模板的概念。

```
@Component({
  selector: 'root',
  template: `
    <h1>{{title}}</h1>
    <h2>{{subtitle}}</h2>
    `
})
export class AppComponent {
  title = 'Angular';
  subtitle = 'v7';
} 
```

Enter fullscreen mode Exit fullscreen mode

模板代码也可以放在单独的 HTML 模板文件中。Angular 中的新东西是一个关于模块 T1 和 T2 服务 T3 的概念。这些只是为了给我们的代码提供一个更好的结构。模块允许把你的 web 应用程序分成可以独立工作的小块。它们封装了组件、服务等。致力于同样的任务。另一方面，服务也有其提供**模块化**的目的，通过将任何不直接连接到视图的逻辑分组到专门的类中。所以，**代码结构**是整个 Angular 中最重要的方面之一。这可以被视为许多大型成熟框架的趋势。这就是为什么 Angular 被推荐用于创建真正大的应用程序。

# 总结一下吧

我如此关注这些框架/库的原因是为了展示它们共享的概念和技术。组件的思想和 **HTML-JS 集成**在它们都可以观察到。这使得该工具使用起来更加容易和舒适。除此之外，React 有状态组件的实例也出现在许多库中。至于角度模块化和可用性，提供特性(模块和服务)在任何大框架中都是很好的补充。事实上，许多软件架构的想法是共享的，这使得在不同的工具之间切换变得更加容易。当然，这有它自己的缺点，主要是停滞和这种东西。这就是为什么一些新概念非常值得一看，因为它们为我们提供了一些新鲜的东西。

# 替代品

既然我们已经讨论了最重要的细节，让我们继续看一些其他的库，甚至是处理 UI 的完整框架。有些你可能很熟悉，有些你可能不熟悉。有些可能为给定的问题提供了一种新的方法，而其他的就不那么好了。最后要注意的是，我不会包括任何已知的 React 替代库，如 **Preact** 。这是因为如果这些仅仅是 React 的更好或更差的克隆，它们并没有给列表带来比 React 本身更多的价值。现在，让我们继续看列表。

[![img](img/a5984bb0a9edef23b0fe175e9d52686d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YB_sds8y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-16-20-51-38.png)

## [Hyperapp](https://github.com/jorgebucaran/hyperapp)

Hyperapp 是一个围绕虚拟 DOM 和组件系统构建的微框架。对于已经使用 React 的人来说，这似乎很熟悉。你可以在你的组件中访问 JSX 和 state(顺便说一句，这些只是函数),但是占用空间要小得多( **~1.6 KB** ) minzipped。Hyperapp 的新特点是一个关于**动作**的概念。在 React 中，你只能用`setState()`方法改变你的状态，在 Hyperapp 中你可以使用动作。这些允许您定义不同的方法来更新和更改您的状态。它给你的组件和代码增加了大量的结构。

[![img](img/90a10e1be029e15d198264116b52548b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--69iGGVCS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-16-20-52-55.png)

## [HyperHTML](https://viperhtml.js.org/hyperhtml)

HyperHTML 与所有其他列出的 UI 库非常相似。但是它引入了一个突破性的概念——使用**标记的模板文字**。基本上，它允许你这样做:

```
hyper`<div>HyperHTML</div>` 
```

Enter fullscreen mode Exit fullscreen mode

这是多么直观啊！你可以使用类似 JSX 的语法，同时省略任何编译器！它不仅提高了性能，还消除了在编译过程中降低代码速度的可能性。绝对值得一探究竟。

[![img](img/39ecb088351c65ce79d1200631cd20db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ifYFFR7A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-16-20-53-33.png) 登月页面

## [月亮](https://kbrsh.github.io/moon)

月球是另一个小型图书馆。这次迎接我们的是模板。月球有自己的 **MVL** 语言用于特定目的。也许这里没有任何新的想法，但是这样小的内存占用( **~3 KB** minzipped)结合良好的关注点分离值得注意。

[![img](img/0025ddd28790876f6d977faef266587a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BjJXu9Sh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-16-20-54-32.png)

## [秘银](https://mithril.js.org/)

Mithril 的目标是为 spa 提供一个快速而小巧的框架。它有**【XHR】**和路线，很不错。有一个关于 JSX 支持的说明，但是 Mithril 更推荐使用它的**标签**(至少它让我感觉如此)。和...这可能不是一个坏主意。

```
m("div", {class: "title"}, "Mithril")
// <div class="title">Mithril</div> 
```

Enter fullscreen mode Exit fullscreen mode

考虑到这种形式不需要任何编译步骤，有时这可能是一个好的选择。但事实上，这取决于你的个人喜好。

[![img](img/2a0e16ef304b0581a7dfc43782da3125.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ECA28jEm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-16-20-55-30.png)

## [Cycle.js](https://cycle.js.org/)

有了 Cycle.js，我们的新概念和新技术数量惊人。Cycle.js 鼓励你拥抱**功能**和**反应式编程**。它允许你将你的用户界面视为一个**数据流**，跟随一系列**输入**和**输出**，最终形成视图。这样，你的代码变得高度可组合，因为你可以从不同的来源提供数据。为了向你展示基本的想法，这里是取自 Cycle.js 网站的第一个例子。

```
import {run} from '@cycle/run'
import {div, label, input, hr, h1, makeDOMDriver} from '@cycle/dom'

function main(sources) {
  const input$ = sources.DOM.select('.field').events('input')

  const name$ = input$.map(ev => ev.target.value).startWith('')

  const vdom$ = name$.map(name =>
    div([
      label('Name:'),
      input('.field', {attrs: {type: 'text'}}),
      hr(),
      h1('Hello ' + name),
    ])
  )

  return { DOM: vdom$ }
}

run(main, { DOM: makeDOMDriver('#app-container') }) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码所做的是从用户交互的输入元素中获取值，并将其作为`name`传递给数据流。至少对我来说，这是一种全新的创建 UI 并将其与给定数据集成的方法。很有意思。我说过 JSX 也得到支持吗？

[![img](img/98bb7bc5198b1dfdf4c049da1fa42ee5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0v_HBcjy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-16-20-56-15.png)

## [身材苗条](https://svelte.technology/)

其哲学与许多其他框架的细微差别。它所做的通常是在构建时转换成普通的 JS、CSS 和 HTML。这样你可以获得更高的性能，因为你的应用不需要在运行时做那么多工作。这也使得对 **SSR** 的支持更加容易。除此之外，Svelte 还包括一套标准的概念——组件、状态、自己的模板语法以及——新增功能——内置的**状态管理**。

[![img](img/5ad3ea7d2e30780c9035658852aa1505.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yx6LrPeO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/01/Screenshot-from-2019-01-16-20-57-24.png)

## [Ember.js](https://www.emberjs.com/)

最后但同样重要的是 Ember.js，这个名字你可能听起来很熟悉。这是因为 Ember.js 是创建 web 应用程序的最受欢迎的选项之一。就像 Angular 一样，它是一个成熟的框架，包含了所有需要的功能。它的特色之一是它的模板语言- **手柄**。它让你能够用不同的**助手**、**动作**和数据创建动态 UI。

# 总结起来

这就结束了列表。当然，每天都有新的 JS 框架和库出现，还有更多的 JS 框架和库等着我们去使用。我主要想关注那些用不同的、新的和有趣的方法来创建你的 UI 的。我想我是对的。如你所见，有大量的替代反应，Vue 和 Angular。也许上面列出的其中一款以其**独特的设计**吸引了你？也许，只是也许你会考虑把它用于另一个项目，或者只是玩一会儿，学习一些新的东西。反正目前就这些了。如果你想收到更多**最新**内容，可以考虑在 [*Twitter*](https://twitter.com/areknawo) 或者我的 [*脸书页面*](https://www.facebook.com/Areknawos-Blog-770620059945105) 上关注我。