# 构建 JSX + DOM 库第 2 部分

> 原文：<https://dev.to/merri/building-a-jsx-dom-library-part-2-kk3>

我们现在已经到达了一个点，与第一部分的[简单相比，复杂性将增加很多。这种复杂性是由两件事造成的:](https://dev.to/merri/building-a-jsx-dom-library-part-1-3ga6)

1.  我们希望在通过单一 JSX 表示对 DOM 树进行更改时像 React 一样。
2.  `dom()`必须只输出 DOM 节点

## 设定目标

在第一部分中，我们以这个应用程序代码结束:

```
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

我们想解决这里的一些问题:

1.  应该没有必要捕获一个本地的`ref`
2.  我们的组件`props`不应该是直接的 DOM 元素属性
3.  `changeColor`不应该需要了解`render`

简而言之，我们希望从纯粹的 *DOM 突变*过渡到*状态突变*，在这种情况下，使用库的开发人员可以专注于他正在做的事情，而不必太关心库。或者换句话说:使用组件来描述事物应该是什么样子，而不是手动编写 DOM 操作代码。

作为图书馆的作者，我们怎样才能篡改 JSX，以便有所作为呢？如果我们看 React，它总是渲染组件渲染方法。因此，我们目前没有渲染方法。我们需要在某处添加功能。那么怎么样...

```
function Component(props) {
    function changeColor() {
        props.dark = !props.dark
    }

    return (
        <div style={() => `background-color: ${props.dark ? 'red' : 'wheat'}; padding: 5px;`}>
            <h1>Hello world!</h1>
            <button onclick={changeColor}>Change color</button>
        </div>
    )
}

const App = <Component dark={false} />

document.body.appendChild(App) 
```

Enter fullscreen mode Exit fullscreen mode

这不好看吗？我们现在在`style`属性中有一个函数，我们可以调用它。我们也有本地状态的组件，我们可以变异，因为它是我们自己的东西。最棒的是，它的语法可读性很强，容易推理，而且没有任何库的迹象。

这确实提出了挑战和问题:难道我们不应该区分像`onclick`和`style`这样的函数吗？我们如何在状态改变后再次渲染？

## 处理函数

从现在开始，有相当多的代码需要处理，所以为了方便起见，下面是第 1 部分的完整代码:

[https://codepen.io/Merri/embed/mYEmaJ?height=600&default-tab=result&embed-version=2](https://codepen.io/Merri/embed/mYEmaJ?height=600&default-tab=result&embed-version=2)

从这里开始，让我们一步一步地调整应用程序代码来添加特性。我们最初的步骤是引入函数！

```
// --- Application ---

function Component(props) {
    function changeColor() {
        props.dark = !props.dark
        render(ref)
    }

    const ref = (
        <div style={() => `background-color: ${props.dark ? 'red' : 'wheat'}; padding: 5px;`}>
            <h1>Hello world!</h1>
            <button onclick={changeColor}>Change color</button>
        </div>
    )

    return ref
}

const App = <Component dark={false} />

document.body.appendChild(App) 
```

Enter fullscreen mode Exit fullscreen mode

我们离我们想要的已经很近了！现在唯一不好的事情是我们有了`render`，我们需要手动跟踪`ref`。我们将在以后处理这些问题。

因此，应用程序现在是“坏的”，因为`style`显然不工作。我们需要开始管理我们的道具，我们的一句话`Object.assign(element, props)`不再适合我们的需求。

我们有两段代码使用了这个调用。这意味着我们需要建立一个新的功能来管理这个特定的任务！我们将称这个方法为`updateProps`。在我们写之前，我们可以更新调用方法，这样就不再需要传递`nextProps`来渲染:

```
// --- Library ---

const propsStore = new WeakMap()

function updateProps(element) {
    const props = propsStore.get(element)
}

function render(element) {
    if (!propsStore.has(element)) return
    updateProps(element)
}

function dom(component, props, ...children) {
    props = { ...props }
    const element = typeof component === 'function'
        ? component(props)
        : document.createElement(component)
    propsStore.set(element, props)
    updateProps(element)
    return children.reduce(function(el, child) {
        if (child instanceof Node) el.appendChild(child)
        else el.appendChild(document.createTextNode(String(child)))
        return el
    }, element)
} 
```

Enter fullscreen mode Exit fullscreen mode

`updateProps`只需要接受`element`，因为我们可以简单地获得对`props`的引用。调用它时没有理由这样做。

`render`将是一个公共方法，而`updateProps`将是库的内部方法。这就是为什么`render`会检查`propsStore`中元素的存在。

是时候写一些逻辑来处理函数了！

```
function updateProps(element) {
    const props = propsStore.get(element)
    Object.entries(props).forEach(([key, value]) => {
        if (typeof value === 'function') {
            // use event handlers as they are
            if (key.slice(0, 2) === 'on') {
                if (element[key] !== value) element[key] = value
                return
            }
            // call the function: use element as this and props as first parameter
            value = value.call(element, props)
        }
        // naively update value if different
        if (element[key] !== value) {
            element[key] = value
        }
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们运行应用程序时，我们应该有一个小麦色的背景。是吗？

[https://codepen.io/Merri/embed/EzymJO?height=600&default-tab=result&embed-version=2](https://codepen.io/Merri/embed/EzymJO?height=600&default-tab=result&embed-version=2)

成功！然而...为什么按钮不起作用？我们必须调试。因此，良好的旧控制台日志记录:`Object.entries`之前的`console.log('updateProps', element, props)`应该向我们显示什么是错误的。

而结果:

```
"<div style='background-color: wheat; padding: 5px;'>...</div>" Object {
  dark: true
} 
```

Enter fullscreen mode Exit fullscreen mode

该死的。我们在这里不再得到`style`道具，取而代之的是得到组件的道具！我们确实需要组件的 props 将它们作为第一个参数传递给函数，因为这对于当前不相关的原因是有用的，但是我们还需要区分组件和元素。

我们要责备的行是在`dom`方法中:在那里我们没有检查我们是否已经有一个引用就设置了`propsStore`。这将被调用两次:第一次是当`dom`创建`div`元素时，第二次是当`Component`被调用时，调用同一个`div`。

一个简单的解决方法是忽略组件:

```
function dom(component, props, ...children) {
    props = { ...props }
    const isFn = typeof component === 'function'
    const element = isFn ? component(props) : document.createElement(component)
    if (!isFn) propsStore.set(element, props)
    updateProps(element)
    return children.reduce(function(el, child) {
        if (child instanceof Node) el.appendChild(child)
        else el.appendChild(document.createTextNode(String(child)))
        return el
    }, element)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的代码有用吗？

[https://codepen.io/Merri/embed/yWJXzR?height=600&default-tab=result&embed-version=2](https://codepen.io/Merri/embed/yWJXzR?height=600&default-tab=result&embed-version=2)

确实如此。按钮现在可以正确地在两种颜色之间切换。这就把我们带到了第二部分的结尾。

还有更多挑战需要解决:

1.  组件属性最好传递给属性属性函数。
2.  我们还是要手动调用`render`，保留`ref`。
3.  如果我们将`style`移动到`h1`元素，那么我们的点击不再有效:(

第一个和第二个具有挑战性；第三个问题应该更容易解决。下一部出来之前能解决吗？

* * *

其他零件: [1](https://dev.to/merri/building-a-jsx-dom-library-part-1-3ga6) 、 [3](https://dev.to/merri/building-a-jsx-dom-library-part-3-2kgc) 、 [4](https://dev.to/merri/building-a-jsx-dom-library-part-4-ref-in-peace-53nf)