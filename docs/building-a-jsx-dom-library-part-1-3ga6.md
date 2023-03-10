# 构建 JSX + DOM 库第 1 部分

> 原文：<https://dev.to/merri/building-a-jsx-dom-library-part-1-3ga6>

当一个人在创造新的东西时，休息一会儿总是一个好主意。虽然在短时间内让自己陷入问题很好，但停下来，保持一定距离，多学习，寻找类似的解决方案也同样重要。

不久前，我又开始着手一个已经四年没有碰过的项目。我开始快速应用新的想法，并找到了一些不错的解决方案，但其他一些事情感觉并不太好，其中很多都与代码很旧并且是在极其广泛的浏览器支持下编写的这一事实有关。

然后我突然想到:当世界上到处都是常青树浏览器的时候，用一个全新的库来支持大型传统浏览器有意义吗？过多地回顾过去，我不就是在限制自己吗？五年前，这还是有意义的。但是现在网络完全不同了:IE11 正接近它的生命周期的尽头。这是我们唯一还在使用的非常青树浏览器。至于浏览器引擎，我们只有 Firefox、Chromium 和 Safari。

## 入门

在这个我希望有一天能够完成的文章系列中，我将从头开始重建我用 NomJS 所做的事情。其目的是解决一个挑战:你能创建一个使用 JSX 语法的 DOM 库，并拥有类似 React 的具有生命周期和合理状态管理的组件吗？这意味着**虚拟 DOM 被禁**！

这应该会给我们带来超越 React 的性能优势——只要该库的潜在未来用户的开发人员体验不会过多地鼓励不良的性能扼杀习惯。

首先，如果你需要向 JSX 介绍自己，如何使用定制的 pragma 或者如何进行设置，你可以[阅读 Aleks@ITNEXT](https://itnext.io/lessons-learned-using-jsx-without-react-bbddb6c28561) 的这个小教程。这些基础知识对于阅读来说是非常必要的，而且它也引入了代码问题:创建一些可以持续呈现更新状态的东西并不容易！

**从这一点上来说，你需要有一个与巴别塔的开发环境，在那里你可以使用`/*​* @jsx dom */`** (例如 [CodePen](https://codepen.io) 工作良好)。

## 知道自己的局限

我们的第一个函数是`dom()`。这与`React.createElement`有相同的语法，因此我们可以使用 JSX 语法来获得它提供的所有易于阅读的类似 HTML 的优点。

这个函数有一个明确的规则:它必须输出本地 DOM 节点。禁止输出任何其他内容。出来的东西一定是`appendChild`之类的有效输入。

实现第一个简单版本很简单:

```
/** @jsx dom */

function dom(component, props, ...children) {
    // make sure props is an object
    props = { ...props }
    // make DOM element
    component = document.createElement(component)
    // apply props as attributes
    Object.assign(component, props)
    // add children
    return children.reduce(function(el, child) {
        // in both cases make sure we output a valid DOM node
        if (child instanceof Node) el.appendChild(child)
        else el.appendChild(document.createTextNode(String(child)))
        return el
    }, component)
}

// to make sure it works...
document.body.appendChild(
    <div style="background: gray; padding: 5px;">
        <h1>Hello world!</h1>
        <p>This is a test</p>
    </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

虽然这适用于许多简单、静态的情况，但它不适用于我们想要做的许多其他事情。它只输出新的 DOM 节点，这就是它所能做的。

如果我们想渲染不同的东西呢？我们能改变孩子吗？我们可以改变属性/道具吗？

最简单的方法是求助于原生 DOM 方法:只需使用`appendChild`和`removeChild`并直接设置属性，就像“老办法”一样。然而，这并没有带来 React 在控制你能做什么和你什么时候能做时所提供的好处。我们想做得更好。

## 更换道具

所以，我们要更新道具。最简单的，我们可以把它抽象成如下内容:

```
// --- Library ---

const propsStore = new WeakMap()

function render(element, nextProps) {
    if (!propsStore.has(element)) return
    const props = Object.assign(propsStore.get(element), nextProps)
    Object.assign(element, props)
    return element
}

function dom(component, props, ...children) {
    props = { ...props }
    const element = document.createElement(component)
    // remember a reference to our props
    propsStore.set(element, props)
    Object.assign(element, props)
    return children.reduce(function(el, child) {
        if (child instanceof Node) el.appendChild(child)
        else el.appendChild(document.createTextNode(String(child)))
        return el
    }, element)
}

// --- Application ---

const App = (
    <div style="background: gray; padding: 5px;">
        <h1>Hello world!</h1>
        <p>This is a test</p>
    </div>
)

document.body.appendChild(App)

render(
    App,
    { style: 'background: red; padding: 5px;' }
) 
```

Enter fullscreen mode Exit fullscreen mode

上面我们添加了一个允许改变道具的`render`方法。如果我们的示例有更多的道具，它现在将更新所有其他给定的道具，而不仅仅是`style`。然而，这将是我们唯一的优势:我们仍然不能更新内部组件的道具。或者嗯，我们可以:

```
render(
    App.querySelector('h1'),
    { style: 'color: white; font-family: sans-serif;' }
) 
```

Enter fullscreen mode Exit fullscreen mode

但是这并没有真正导致可维护的代码。这也很冗长，如果我们只是调用`App.querySelector('h1').style = 'color: white; font-family: sans-serif;'`，这几乎是一样的。我们遗漏了一些东西！

## 支持组件

到目前为止，我们只支持字符串元素。也就是说，您只能创建`div`、`br`和所有其他本地 DOM 元素。这对于简单的情况来说很好，但是从前面的代码示例中可以看出，我们目前受到了很大的限制。我们不能在任何地方控制州政府！

为了解决这个问题，我们可以使用一个简单的本地 JavaScript 机制:函数！在函数内部，我们可以在局部变量或者外部变量中保存一些状态，尽管这通常不是一个好主意。

让我们扩展我们的`dom`方法来支持函数组件！

```
function dom(component, props, ...children) {
    props = { ...props }
    const element = typeof component === 'function'
        ? component(props)
        : document.createElement(component)
    propsStore.set(element, props)
    Object.assign(element, props)
    return children.reduce(function(el, child) {
        if (child instanceof Node) el.appendChild(child)
        else el.appendChild(document.createTextNode(String(child)))
        return el
    }, element)
} 
```

Enter fullscreen mode Exit fullscreen mode

必须注意，我们没有错误检查，并假设函数返回一个原生 DOM 元素。上面的代码仍然有效，现在你可以做以下事情了！

```
// --- Application ---

function Component(props) {
    function changeColor() {
        render(ref, { style: 'background: red; padding: 5px;' })
    }

    const ref = (
        <div style={props.style}>
            <h1>Hello world!</h1>
            <button onclick={changeColor}>Change color</button>
        </div>
    )

    return ref
}

const App = <Component style="background: gray; padding: 5px;" />

document.body.appendChild(App) 
```

Enter fullscreen mode Exit fullscreen mode

好的一面是，我们现在已经将所有相关的代码包含在一个单一的范围内。它在一个组件中。这给了我们一些实际上开始类似于 React 组件的东西，但是有相当多的缺点:例如，突变仍然非常直接，因为我们还没有修复`render`。

扔掉`nextProps`进行渲染开始看起来不是个好主意。我们必须以不同的方式控制状态，但我们如何做到这一点呢？我们不能再运行`<Component />`了，因为这会给我们一个全新的实例！我们将`props.style`传递给根节点`div`，但是那一行只执行一次，所以即使`props.style`改变了，我们也不会得到它的更新。

* * *

在下一部分，我们开始管理道具更新。我会在这些文章写好的时候发布它们，所以这可能需要一段时间——为什么不同时尝试找出一个解决方案呢？:)

1.  使组件`div`在`gray`和`red`背景之间切换
2.  你能让组件感觉更像 React 吗？
3.  你能避免使用本地的`ref`引用吗？

* * *

其他部分: [2](https://dev.to/merri/building-a-jsx-dom-library-part-2-kk3) 、 [3](https://dev.to/merri/building-a-jsx-dom-library-part-3-2kgc) 、 [4](https://dev.to/merri/building-a-jsx-dom-library-part-4-ref-in-peace-53nf)