# 构建 JSX + DOM 库第 4 部分

> 原文：<https://dev.to/merri/building-a-jsx-dom-library-part-4-ref-in-peace-53nf>

在《T2》第三部中，我们修正了很多组件行为。虽然还不完美，但我们终于可以让第二部分中介绍的梦想成真了:一个不需要手动保存`ref`和调用`render`的组件！

这是我们现在的目标应用程序代码:

```
function HelloWorld(props) {
    return (
        <h1 style={() => `color: ${props.dark ? 'white' : '#333'};`}>
            Hello world!
        </h1>
    )
}

function Component(props) {
    return (
        <div
            style={() =>
                `background-color: ${
                    props.dark ? 'red' : 'wheat'
                }; padding: 5px;`
            }
        >
            <HelloWorld dark={() => props.dark} />
            <button onclick={() => (props.dark = !props.dark)}>
                Change color
            </button>
        </div>
    )
}

const App = <Component dark={false} />

document.body.appendChild(App) 
```

Enter fullscreen mode Exit fullscreen mode

最酷的部分是:

1.  `changeColor`不叫`render`！现在是单线箭头功能！
2.  没有`ref`的本地副本！

## 计划

我们已经进入了状态管理中的一个经典问题:何时更新？当研究其他解决方案时，我们可以看到，在经典的 React 中，我们被引导使用`this.setState`。这使得 React 的作者可以优化渲染，这样整个树都不需要改变，只需要改变当前的分支。不幸的是，这也增加了一些额外的样板文件，例如您必须管理`this`。

另一方面，这种状态改变优化也可能在 React 中被破坏！例如，在 pre-hooks Redux 中，每次状态存储改变时都会调用每个连接的组件:尽管添加了 diff 检查来阻止实际呈现，但这仍然是额外的工作。其他人已经在他们自己的状态解决方案中解决了这个问题，例如 [Storeon](https://evilmartians.com/chronicles/storeon-redux-in-173-bytes) 允许有针对性的重新渲染。

* * *

但是...如果我们看看我们的应用程序是什么样子，**没有什么**！唯一处理状态的是`props`。我们也很邪恶，因为我们让它变异了。在 React、Redux 和 Storeon 中，我们鼓励您像处理不可变状态一样处理状态。而我们却在这里，什么都没做！

然而，如果我们考虑实际问题，我们不会像 React 那样渲染。在这里，虚拟 DOM 树是在每次渲染调用时构建的，当下一次渲染发生时，渲染函数保存的任何状态都将丢失。我们没有虚拟 DOM，取而代之的是该函数仍在使用，并且可以作为状态的来源，允许我们使用`props`。

这导致了相对于 React 的性能优势。我们不是使用单一的大型渲染函数，而是针对单一的属性，并在许多小型渲染函数的帮助下进行渲染。这些函数不会浪费时间来处理虚拟 DOM:它们会导致直接突变。

这意味着，即使我们实现了最不理想的渲染策略，每次渲染整棵树，我们也可能比类似的 React 应用程序做更少的工作——特别是当应用程序很大的时候。

因此，继续编写一个简单的更新策略似乎是可行的！

## 执行

通过实际的代码，我们可以实现一个简单的渲染队列:调用`requestAnimationFrame`从每个变化中重新渲染，并且只在队列中保留一个即将到来的渲染，忽略任何进一步的再次渲染请求，直到渲染完成。

我们也在走一条非常天真的路线:简单地捕获**所有** DOM1 事件处理程序(`onclick`等)。)并添加一个调用来将一个渲染排队到我们的应用程序的根目录。唯一需要注意的特殊情况是，我们可能同时运行多个应用程序，所以我们需要允许为每个应用程序排队一个渲染。

```
const queuedRenders = new Map()

function queueRender(element) {
    if (!propsStore.has(element)) return
    // find the top-most element in the tree
    while (element.parentNode && propsStore.has(element.parentNode)) {
        element = element.parentNode
    }
    // find component, and if element is not in component then use that
    const root = parentComponents.get(element) || element
    if (queuedRenders.has(root)) return
    queuedRenders.set(root, requestAnimationFrame(function() {
        // allow for new render calls
        queuedRenders.delete(root)
        // if equal then not wrapped inside a component
        if (root === element) {
            if (document.documentElement.contains(root)) {
                render(root)
            }
        } else {
            // find all siblings that are owned by the same component and render
            for (let child of element.parentNode.childNodes) {
                if (root === parentComponents.get(child)) render(child)
            }
        }
    }))
} 
```

Enter fullscreen mode Exit fullscreen mode

有一些事情需要注意:

1.  片段组件目前没有它们的子组件的完整记录，只有反过来，所以我们必须循环检查元素的父组件是否是同一个组件。有点丑，但是足够好了。
2.  是的，我们甚至允许重新渲染而不包装成一个组件！或者，我们会，但有一个问题要解决。我们稍后会谈到这一点！

既然我们可以对渲染进行排队，那么我们也应该利用这个队列！来更新一部分`updateProps`...

```
const queueFunctions = new WeakMap()

function updateProps(element, componentProps) {
    const props = propsStore.get(element)
    Object.entries(props).forEach(([key, value]) => {
        if (typeof value === 'function') {
            if (key.slice(0, 2) === 'on') {
                // restore cached version
                if (queueFunctions.has(value)) {
                    const onFn = queueFunctions.get(value)
                    if (element[key] !== onFn) {
                        element[key] = onFn
                    }
                } else {
                    // wrap to a function that handles queuein
                    const newOnFn = (...attr) => {
                        value.call(element, ...attr)
                        queueRender(element)
                    }
                    // cache it
                    queueFunctions.set(value, newOnFn)
                    element[key] = newOnFn
                }
                return
            }
            value = value.call(element, componentProps)
        }
        if (element[key] !== value) {
            element[key] = value
        }
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当按下按钮时，应用程序更新！然而，我确实提到了一个问题...

## 重构错误

首先，这里是你可能在任何地方都能找到的最短的可读计数器样本:

```
let count = 0
document.body.appendChild(
    <p title={() => count}>
        <button onclick={() => count++}>+</button>
        <button onclick={() => count--}>-</button>
    </p>
) 
```

Enter fullscreen mode Exit fullscreen mode

它使用了`title`属性，因为我们还没有管理动态子节点。反正就是**短**！我们想让它工作——事实上，当`updateProps`取消了对`componentProps`的检查时，我们确实让它工作了。

触及这个问题让我开始研究如何设置父母，我注意到我在如何设置循环孩子时有点傻。相反，一个随时知道父组件的简单堆栈使得父组件的管理更加容易。

所以，我们完全抛弃了`setParentComponent`。然后我们更新`dom`如下:

```
const parentStack = []

export function dom(component, props, ...children) {
    props = { ...props }
    const isComponent = typeof component === 'function'
    const element = isComponent
        ? document.createDocumentFragment()
        : document.createElement(component)
    // if no parent component then element is parent of itself
    const parent = parentStack[0] || { component: element, props: {} }
    parentComponents.set(element, parent.component)
    if (isComponent) {
        componentPropsStore.set(element, props)
        // fixed a bug here where initial props was unset
        const exposedProps = updateComponentProps({ ...props }, props)
        propsStore.set(element, exposedProps)
        // increase stack before calling the component
        parentStack.unshift({ component: element, props: exposedProps })
        // the following will cause further calls to dom
        element.appendChild(component(exposedProps))
        // work is done, decrease stack
        parentStack.shift()
    } else {
        // is element independent of a component?
        if (parent.component === element) {
            componentPropsStore.set(element, parent.props)
        }
        propsStore.set(element, props)
        updateProps(element, parent.props)
    }
    return children.reduce(function(el, child) {
        if (child instanceof Node) el.appendChild(child)
        else el.appendChild(document.createTextNode(String(child)))
        return el
    }, element)
} 
```

Enter fullscreen mode Exit fullscreen mode

结果我们减少了一点代码！我们现在对状态有了更清晰的管理，其中`componentProps`总是可用的，因此避免了不在组件内的元素的“无初始状态”问题。

在这里，看看目前的应用程序-包括超级短计数器的例子！

[https://codesandbox.io/embed/2pq238ojoj](https://codesandbox.io/embed/2pq238ojoj)

相反的例子表明我们没有好好照顾我们的孩子。虽然还存在其他问题，例如元素属性的管理可以改进很多，但是最好还是认真对待我们的孩子。所以这将是我们的下一个话题！

* * *

其他零件: [1](https://dev.to/merri/building-a-jsx-dom-library-part-1-3ga6) 、 [2](https://dev.to/merri/building-a-jsx-dom-library-part-2-kk3) 、 [3](https://dev.to/merri/building-a-jsx-dom-library-part-3-2kgc)