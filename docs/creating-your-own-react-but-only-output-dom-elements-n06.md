# 创建自己的“React ”,但只输出 DOM 元素

> 原文：<https://dev.to/merri/creating-your-own-react-but-only-output-dom-elements-n06>

你好！第一个帖子在这里:)

React 已经推出 5 年多了，在很大程度上是好的。在开发东西的时候，把人类行为控制成良好的习惯是很有帮助的。所有的开发工具、热重装等等都非常适合拥有不同才能和经验水平的多个成员的项目。

不过，这并不都是好事。就虚拟 DOM 的本质而言，有许多额外的工作总是在幕后发生，不容易消除。Svelte 很好地提出了这一点，它让编译器为您做优化工作。当应用程序发展到一定规模时，这使得网站表现更好。

但是回到 React 的好的部分。我喜欢 JSX。这通常会产生可读性很强的代码。如果我们把这个偷到我们自己的东西里呢？

## 看看外面有什么

这当然不是一个新的想法！如今，JavaScript 社区非常庞大。每样东西都是以多种方式、多次被发明出来的。然而，做一些实际上能解决问题的东西是一个相当大的挑战，如果你能达到这样一个水平，那就更大了，那就是把所有的东西放在一起，使它们彼此之间产生协同作用。所以让我们看看我们能找到什么！

有`nativejsx`[将 JSX 转换为直接的本地 DOM 方法](https://github.com/treycordova/nativejsx)，但是它有一些明显的缺点。首先，它需要额外的客户端 JS 来扩展 HTMLElements 的原型。给**所有的** DOM 元素添加新特性有点多。不过更糟糕的是，transpiled 代码非常冗长，因为它重复了`document.createElement`和其他代码很多次，导致大量的 JS 压缩得很好，但对于浏览器来说仍然需要解析很多。这有性能损失。我们可能更希望输出尽可能紧凑的语法。

然后就是[直接输出 DOM 节点的`jsx-dom`](https://github.com/glixlur/jsx-dom)。听起来非常接近我们想要的！这个项目模拟了很多 React，并有自己的实现，比如`createRef`。然而，缺点是输出是静态的:一旦你完成了 DOM 树的构建，就不可能再进行渲染了。如何解决这个问题？

## 问题

坏消息是，没有好的方法从 JSX 输出 DOM 节点，这样您就可以再次调用“render ”,只发生变化。如果您在这两者之间包装任何东西，您实质上是在重新实现虚拟 DOM，而作为我们的挑战，我们希望避免这种情况。我们希望去掉 DOM 节点。但是我们也希望他们更新。

为了突出代码的问题，考虑下面的例子:

```
function MyComponent(props) {
    return (
        <div>
            {props.visible ? 'You can see me!' : 'Nope'}
        </div>
    )
}

// React throws away other DOM elements and creates a new one for us
ReactDOM.render(<MyComponent visible={true} />, document.body)
// React does not expose DOM elements so we have to "magically" find it
document.body.querySelector('div').style.backgroundColor = 'black'
// React finds DOM node it owns and updates it
ReactDOM.render(<MyComponent visible={false} />, document.body) 
```

Enter fullscreen mode Exit fullscreen mode

我们最终得到的页面是黑色的 div，文本为“不”。这是可能的，因为 under-the-hood React 的 diffing 注意到我们仍然在使用一个 div，并且重用了页面上已经存在的 DOM 节点。因为 React 没有被告知任何关于`style`属性的信息，所以它没有注意到这个属性。这就是为什么你最后得到的是一个黑色的 div。

那么天真的 DOM 实现会发生什么呢？`MyComponent`将输出一个 DOM 节点。然后`render`清除`document.body`中任何没有反应的孩子，并在那里添加`MyComponent`的结果。

在下一步中，non-React 代码启动并改变 DOM 节点，将背景颜色设置为黑色。到目前为止一切顺利！

但是我们遇到了一个问题:我们第二次调用了`MyComponent`,现在我们已经有了两个 DOM 节点:一个是我们之前创建的，另一个是新的。如果我们继续简单地替换旧的 DOM 节点，那么我们的变异就丢失了:新的 div 不会是黑色的。

有人可能会想:好吧，让我们只区分 DOM 元素！然后，您将看到您需要做什么:您需要有一个所有有效属性的完整列表，还需要检查所有活动的绑定事件。然后是性能方面的考虑:做所有这些工作对 DOM 节点来说已经很繁重了。但是这里真正的杀手是改变 DOM 元素很慢。在上面这种简单的情况下，这并不重要，但是有了一个包含数百个元素的应用程序，你很快就会非常快地耗尽任何手机的电池。

## 与 React 兼容性中断

为了解决这个问题，我们必须让*做点什么*让我们可以随时更新渲染。幸运的是，有一件事可以做到这一点:良好的旧功能！

```
function MyComponent(props) {
    // unlike React the <div /> and code before return is executed only once
    return (
        <div>
            {() => props.visible ? 'You can see me!' : 'Nope'}
        </div>
    )
}

// we want to keep only one DOM node
const App = <MyComponent visible={true} />
// add it to body (oh, we don't clear unknown nodes)
document.body.appendChild(App)
// mutation!
App.style.backgroundColor = 'black'
// render again... using a plain object
render(App, { visible: false }) 
```

Enter fullscreen mode Exit fullscreen mode

因此，在上面的例子中，我们已经更新了`MyComponent`,以便在函数中执行条件代码。我们可以跟踪 DOM 元素及其相关的子函数，这样我们就可以在必要时调用更新——并且不再创建更多的 DOM 元素。除非函数本身输出 DOM 元素，但我们稍后会回到这个话题。

在上面的代码中，为了让它工作，`render`方法除了需要有`App` DOM 元素之外，还需要有一个对原始`props`的引用。这将允许使用`Object.assign()`到**突变**原来的`props`。但是等等！变异是邪恶的！它迟早会导致意想不到的错误。

解决这个特殊问题的一种方法是将 props 直接传递给函数:

```
function MyComponent(props) {
    return (
        <div>
            {props => props.visible ? 'You can see me!' : 'Nope'}
        </div>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

但是这样我们就有了`props`的两个真相:最初最初的道具和`render`传递的那些。还有另一个问题:如果我们在 condition 中有另一个组件，那么我们将被迫在每次渲染时创建一个新的 DOM 元素，这很糟糕，因为如果我们替换一个节点，那么所有现有的 DOM 状态都将丢失。

## 管理那些节点

因此，我们需要一种不同的方式来管理可见性。主要的限制是，如果输出是一个 DOM 节点，我们不能做简单的条件。中间的东西需要负责缓存结果。

React 中是否存在某种可以用于其他目的的东西？

```
function MyComponent(props) {
    return (
        <div>
            <Fragment if={() => props.visible}>
                <span>You can see me!</span>
            </Fragment>
            <Fragment if={() => !props.visible}>
                <span>Nope</span>
            </Fragment>
        </div>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

哦，DOM 片段中的 meet `Fragment` s 比较特殊，因为它们不能存在于 DOM 树中。他们总是顶级父母，他们不可能是孩子。如果你渲染一个片段到 DOM，那么只有它的孩子会去那里，这个片段变成空的。

在我们的需求中，我们可以给片段添加一个条件:当`if`函数的结果为真时，我们可以让孩子被呈现给`Fragment`的父母。否则，如果我们愿意，我们可以将它们捕捉回片段。

这允许我们保存结果的缓存副本，这样当重新渲染发生时，我们只需返回对现有 DOM 元素的引用，而不是生成一个新的元素。情况变得更糟的唯一时间(与 React 的虚拟 DOM 相比)是条件交换的时候:这是我们被迫引入一个新的 DOM 节点的时候。React 的不同之处在于它可以简单地看到一个`span` DOM 元素，并且只更新它的文本。

`Fragment`的问题是，与 React 相比，我们最终使用了更加冗长的语法。至少在这种情况下。我们可以使用更短的组件名，但这样就像`<If truthy={() => ...}>`一样，我不确定这样是否好。这也可能会鼓励实现对之前组件有条件的组件，如`<ElseIf />`和`<Else />`，这将是一种新的复杂性，因为一个组件的渲染结果将被绑定到一个不相关的组件。

## 为实现这个想法所做的事情

四年前我写了 [Nom](https://github.com/Merri/nom) :它只有一个输出原生 DOM 元素的目标，我的大部分精力都放在了让它与 diffing 和 updating 一起工作，拥有短语法和**非常**大的浏览器支持，范围从 IE5 倍。呀。很少有人考虑管理状态以及如何让它更容易使用。

所以最近我回到了这个项目中，并开始用我在过去五年中与 React 一起工作时获得的所有经验和想法来使它现代化。简单地放弃大量的向后兼容性，也许只让东西与原生 ES6 一起工作是很有意义的，所以时间将花在实际制作一些将来看起来和工作起来都很棒的东西上。

这让我想到了 JSX，也想到了我以前没有解决的大问题:国家管理。很多 React 都是关于如何管理状态以及何时以及如何改变它。最近增加了像钩子这样的东西，使得函数比以前更加可行。

但是我已经改进了 NomJS。它的工作方式并不完全像我之前在这篇文章中谈到的那样:它依赖于使用`requestAnimationFrame`的持续更新，因此并不公开`render`。它还会向自己创建的 DOM 节点添加一些东西，这是我不喜欢的，也是我想要删除的。但在很大程度上，它已经发挥了作用。如果你对 Codepen 上的[感兴趣，你可以找到一个测试各种功能的演示应用](https://codepen.io/Merri/pen/bJJMMd)。或者看看 GitHub 上的[源码。目前，代码需要大修，因为我计划删除所有的`Object.defineProperty`内容，转而依靠`Map`来跟踪 Nom 创建的 DOM 节点。](https://github.com/Merri/nomjs)

目前，NomJS 还处于开发中的 alpha 阶段。任何事情都可能改变，因为会有更多的特殊情况需要考虑，会有更多的想法:就像在写这篇文章的时候，我得到了一些新的想法，比如之前我没有想到的`If`的`truthy`道具。此外，状态流真的需要更多的思考:目前突变是非常重要的(如演示中所见)。创建一个`render`方法而不是`requestAnimationFrame`确实有意义。给两者都提供选择可能会很好。还有一些事情，比如生命周期，仍然没有答案:比如`mounted()`、`updated()`、`unmounting()`、`unmounted()`或者其他任何命名约定。

我希望这能激发你的思考。我留下了很多无法解释的东西，比如我以为你知道 JSX 是如何工作的，但我希望这不会太糟糕:)