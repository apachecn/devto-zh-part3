# 构建 JSX + DOM 库第 3 部分

> 原文：<https://dev.to/merri/building-a-jsx-dom-library-part-3-2kgc>

在[的前一部分](https://dev.to/merri/building-a-jsx-dom-library-part-2-kk3)中，我们得到了一个挑战:也更新`h1`组件的`style`！

处理这个问题最明显的地方是在`render`。到目前为止，我们只关注了根元素的渲染，忽略了它的子元素。为剩余的子节点添加一个递归调用 render 的循环对我们来说很神奇:

```
function render(element) {
    if (!propsStore.has(element)) return
    updateProps(element)
    for (let child of element.childNodes) {
        render(child)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`render`是因为我们不能保证子元素是由我们的库创建或管理的。同样，调用`render`确保我们也调用子节点的子节点。

为了利用库的这一变化，我们还需要更新我们的应用程序代码。使用红色背景的白色文本颜色可能会很好！

```
 const ref = (
        <div style={() => `background-color: ${props.dark ? 'red' : 'wheat'}; padding: 5px;`}>
            <h1 style={() => `color: ${props.dark ? 'white' : '#333'};`}>
                Hello world!
            </h1>
            <button onclick={changeColor}>Change color</button>
        </div>
    ) 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们的`h1`元素现在应该更新了:

[https://codepen.io/Merri/embed/zQBaLa?height=600&default-tab=result&embed-version=2](https://codepen.io/Merri/embed/zQBaLa?height=600&default-tab=result&embed-version=2)

确实如此:)在 CodePen 示例中，我在`updateProps`中添加了一些`console.log`，这样可以更容易地看到所有应用的突变。您已经可以找到一些需要改进的地方:例如，小麦背景颜色设置了两次，尽管没有有效的更改。目前我们就让它这样吧(当然，你也可以不这样做！).

* * *

现在忽略优化的一个很好的理由是我们还没有一个完整的特性集。我们拥有的代码越多，实现新功能就越难。优化本身往往很棘手:在全面优化之前进行测试是有意义的。

在这一点上，我们仍然处于添加所有基本功能的早期阶段，我们需要有一个“完整的”可用的类似 React 的库。

* * *

那么，接下来我们该去哪里？继续前进，去除最后的烦恼`render(ref)`，并且看起来与我们当前的应用程序代码“功能完整”,它可以真正成为一个独立的组件，具有应用程序端开发人员所需的最少样板文件，这确实让人很痒。

但目前实际上存在组件问题。当我们将`h1`抽象为它自己的组成部分:
时，我们可以揭示这一点

```
function HelloWorld(props) {
    return (
        <h1 style={() => `color: ${props.dark ? 'white' : '#333'};`}>
            Hello world!
        </h1>
    )
}

// and in Component replace h1 with...
<HelloWorld dark={() => props.dark} /> 
```

Enter fullscreen mode Exit fullscreen mode

我们的文字永远是白色的！为什么？如果我们在`HelloWorld`内部调试`props.dark`，我们会注意到一件事:is 是一个函数。这意味着它被原封不动地传递，而不是被管理。我们必须将它作为函数传递给组件，以便能够更新`dark`值。如果由于我们所拥有的有限性，我们不使用一个函数来帮助我们，它将永远不会得到更新。

## 管理组件

我们的组件抽象显然不能胜任这项任务。当我们查看`dom`时，我们注意到我们省略了组件的所有道具管理:`if (!isFn) propsStore.set(element, props)`。此外，我们当前所有的渲染代码都只假设本地 DOM 节点。

我们还有一个我们想要的特性:将组件的道具作为输入传递给属性函数。我们喜欢这样做的一个原因是它允许优化那些函数(比如 memoize)，这在函数执行成本很高的情况下非常有用。

为了管理组件，我们有一些要求:

1.  需要将元素及其相关组件与。
2.  我们需要在某个地方存储组件道具，以便我们可以传递它们。

首先，我们不能使用组件的功能作为参考，因为我们可能会多次使用同一个组件。为了更容易解决这个问题，我们可以后退一步。`dom`需要输出什么？有效的 DOM 节点。有什么我们可以用来包装其他 DOM 节点的东西吗？

**片段！片段是特殊的 DOM 节点，因为它们只存在于树的顶端。片段不能作为子节点存在:它们的子节点总是自动添加，并从片段中删除。**

第二点现在更容易回答了:我们可以使用现有的`propsStore`，使用一个片段作为我们的参考。我们现在可以开始实现一个代码，它将元素标记为属于一个组件，这样我们就可以将组件的属性作为这些元素的属性函数的输入。

哼。这太复杂了！我们现在将对现有的库方法进行大量的更改，并查看一些新的内部帮助函数。

## 修改为`dom`

从现在开始，我将从 Codepen 切换到 Codesandbox，因为代码量开始超过一个文件。代码的库部分将支配`library.js`并将`export`两个方法:`dom`和`render`。

在介绍这些方法之前，我们已经添加了两个新的武器:

```
const componentPropsStore = new WeakMap()
const parentComponents = new WeakMap() 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们继续，看看我们有什么新的。

```
export function dom(component, props, ...children) {
    props = { ...props }
    const isComponent = typeof component === 'function'
    // create the output DOM element
    const element = isComponent
        ? document.createDocumentFragment()
        : document.createElement(component)
    if (isComponent) {
        // remember original props
        componentPropsStore.set(element, props)
        // create new object that gets the updates of function calls
        const exposedProps = updateComponentProps({}, props)
        // store like normal element props
        propsStore.set(element, exposedProps)
        // call component to create it's output
        element.appendChild(component(exposedProps))
        // mark each DOM node created by us to this component
        for (let child of element.childNodes) {
            setParentComponent(child, element, exposedProps)
        }
    } else {
        propsStore.set(element, props)
        updateProps(element)
    }
    // untouched here, so we're gonna have problems at some point :)
    return children.reduce(function(el, child) {
        if (child instanceof Node) el.appendChild(child)
        else el.appendChild(document.createTextNode(String(child)))
        return el
    }, element)
} 
```

Enter fullscreen mode Exit fullscreen mode

一个函数，我们已经引入了两个新函数！

1.  管理调用函数并更新结果状态，然后将结果状态暴露给组件
2.  `setParentComponent`标记被调用组件的所有子组件到那个组件，包括另一个组件

但是我们还没有准备好改变现有的方法。

## 修改为`render`

```
export function render(element, fragment, componentProps) {
    if (!propsStore.has(element)) return
    // detect parent component so that we can notice if context changes
    const parent = parentComponents.get(element)
    if (parent !== fragment) {
        // the context changed
        fragment = parent
        // update component props by calling functions
        const props = componentPropsStore.get(fragment)
        if (props) {
            componentProps = updateComponentProps(
                propsStore.get(fragment),
                props,
                componentProps
            )
        }
    }
    // we now pass the relevant componentProps here!
    updateProps(element, componentProps)
    for (let child of element.childNodes) {
        render(child, fragment, componentProps)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们在渲染时更新组件道具。我们不是一次又一次地创造道具，而是只在组件改变时才工作。

## 修改为`updateProps`

这里发生的变化最小。

```
function updateProps(element, componentProps) {
    const props = propsStore.get(element)
    Object.entries(props).forEach(([key, value]) => {
        if (typeof value === 'function') {
            if (key.slice(0, 2) === 'on') {
                if (element[key] !== value) {
                    element[key] = value
                }
                return
            }
            // no component props known, no game!
            if (!componentProps) return
            value = value.call(element, componentProps)
        } else if (componentProps) {
            // this is an optimization that reduces work
            // but: maybe it introduces bugs later on!
            return
        }
        if (element[key] !== value) {
            element[key] = value
        }
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

在很大程度上，我们只是通过我们感兴趣的道具。

## 新方法

我们有两个新方法，这两个都是:

```
function setParentComponent(element, fragment, componentProps) {
    // already marked to someone else?
    if (parentComponents.has(element)) {
        // check if the parent component of this element has a parent
        const parent = parentComponents.get(element)
        if (!parentComponents.has(parent))
            parentComponents.set(parent, fragment)
        return
    }
    // are we tracking this element?
    if (!propsStore.has(element)) return
    // mark parent and manage props, then continue to children
    parentComponents.set(element, fragment)
    updateProps(element, componentProps)
    for (let child of element.childNodes) {
        setParentComponent(child, fragment, componentProps)
    }
}

function updateComponentProps(componentProps, props, parentProps = {}) {
    return Object.entries(props).reduce((componentProps, [key, value]) => {
        if (typeof value === 'function' && key.slice(0, 2) !== 'on') {
            componentProps[key] = value(parentProps)
        }
        return componentProps
    }, componentProps)
} 
```

Enter fullscreen mode Exit fullscreen mode

这是拼图完成的最后一块。总结已经取得的成就:

1.  组件呈现为片段
2.  组件现在知道它们的每个子组件，包括其他组件
3.  我们可以将组件的道具传递给它们的子函数
4.  组件可以随着其属性的改变而更新

该库现在已经获得了很多功能，而总代码还不到 100 行！让我们来看一个工作应用程序:

[https://codesandbox.io/embed/n02o1r2mql](https://codesandbox.io/embed/n02o1r2mql)

* * *

是时候反思了。我知道本系列文章不是以一种方便的循序渐进的方式进行教学的:我没有过多地关注细节，而是用工作代码来代替。然而，我希望到目前为止的内容已经让我们对一个有经验的开发人员如何处理事情以及如何将一个想法构建成一个完全工作的库有了一些了解。欢迎在评论中提出问题、反馈和批评！

在下一部分中，是时候处理当前应用程序端代码中的最后一个令人烦恼的部分了:去掉`render`和`ref`！

* * *

其他零件: [1](https://dev.to/merri/building-a-jsx-dom-library-part-1-3ga6) 、 [2](https://dev.to/merri/building-a-jsx-dom-library-part-2-kk3) 、 [4](https://dev.to/merri/building-a-jsx-dom-library-part-4-ref-in-peace-53nf)