# 使用钩子构建更好的 React 组件的三种方法

> 原文：<https://dev.to/bmcmahen/three-ways-to-use-hooks-to-build-better-react-components-je1>

本文提供了三种用钩子替换各种旧的 React 组合模式的方法，以构建更简单、更可重用的组件。在开始之前，有一些与组件合成相关的各种模式的经验是有帮助的，包括使用[子元素](https://benmcmahen.com/mastering-react-component-composition/)，使用[渲染道具](https://benmcmahen.com/using-render-functions/)，[高阶组件](https://reactjs.org/docs/higher-order-components.html)，以及[克隆元素](https://reactjs.org/docs/react-api.html#cloneelement)。如果你对钩子有一个初步的了解会有所帮助。

#### 技巧 1:用钩子替换 cloneElement

你可以经常替换克隆元素的实例，高阶组件，或者用钩子渲染道具。

使用上述技术之一的动机几乎总是为了给子组件提供一些上下文信息，或者向组件公开一些额外的逻辑。React 的`cloneElement`函数可能是实现这一目的的最古老的方法，但它总是有一些缺点:1)它的使用取决于孩子是正确的类型。2)它可以重写道具，迫使你包装潜在的道具，以确保每个道具都被正确应用。3)当使用类似 Typescript 的东西时，很难正确地键入子组件。

让我们使用钩子来探索一个更好的解决方案。假设我们想让一个子组件在开发一个表时知道它在哪个父组件中，这样我们就可以使用正确的标记名，可以是`td`或`th`。考虑实现这一点的`cloneElement`方式:

```
const TableHead = ({ children }) => {
  return (
    <thead>
      {React.cloneElement(children, {
        parent: 'TableHead',
      })}
    </thead>
  )
}

const TableBody = ({ children }) => {
  return (
    <tbody>
      {React.cloneElement(children, {
        parent: 'TableBody',
      })}
    </tbody>
  )
}

const TableCell = ({ parent, children }) => {
  const Component = parent === 'TableHead' ? 'th' : 'td'
  return <Component>{children}</Component>
} 
```

这已经足够好了。我们可以创建一个表，并在每种情况下使用正确的标记名。

```
const Table = () => (
  <table>
    <TableHead>
      <TableCell>Name</TableCell>
      <TableCell>Age</TableCell>
    </TableHead>
    <TableBody>
      <TableCell>Ben McMahen</TableCell>
      <TableCell>Thirty-something</TableCell>
    </TableBody>
  </table>
) 
```

我们可以使用钩子和上下文来提供更灵活的解决方案。让我们重写我们的组件来演示:

```
const SectionContext = React.createContext({ parent: 'TableHead' })

const TableHead = ({ children }) => {
  return (
    <thead>
      <SectionContext.Provider value={{ parent: 'TableHead' }}>
        {children}
      </SectionContext.Provider>
    </thead>
  )
}

const TableBody = ({ children }) => {
  return (
    <tbody>
      <SectionContext.Provider value={{ parent: 'TableBody' }}>
        {children}
      </SectionContext.Provider>
    </tbody>
  )
}

const TableCell = ({ children }) => {
  const { parent } = React.useContext(SectionContext)
  const Component = parent === 'TableHead' ? 'th' : 'td'
  return <Component>{children}</Component>
} 
```

这是一个更灵活的解决方案，因为它不依赖于`TableCell`是`TableHead`或`TableBody`的直接后代。如果你使用 typescript 也很好，因为它不会用父组件提供的道具替换你的`TableCell`道具。

#### 提示 2:将元素绑定到引用

从钩子返回一个绑定函数来引用 dom 元素。

我第一次遇到这种模式是在 [react-spring](https://www.react-spring.io) 中，从那以后我一直在使用它。考虑这样的情况，您希望创建可重用的功能，使引用成为特定的 dom 元素，例如测量 dom 元素或聚焦它们。在我的例子中，我最近需要创建一个可重用的焦点管理器，它绑定到一个特定的元素，如果元素显示了就聚焦它，否则就返回焦点。[聚焦陷阱](https://github.com/davidtheclark/focus-trap)是帮助我们的一个很好的工具。让我们从一个基本的钩子骨架开始。

```
export function useFocusElement(showing, options = {}) {
  const elementRef = React.useRef(null)

  return {
    bind: { ref: elementRef },
  }
} 
```

所以是的，这没什么用。它返回一个包含对我们的`elementRef`的引用的`bind`对象。这将允许我们创建对任何我们想要关注的 dom 元素的引用。`showing`参数将用于决定我们是应该将焦点分配给`elementRef`还是将它返回到最初获得焦点的元素。我们可以如下使用钩子:

```
const Popover = () => {
  const [showing, setShowing] = React.useState(false)
  const bind = useFocusElement(showing)
  return <div {...bind}>Popover!</div>
} 
```

让我们实现钩子的其余部分来利用焦点陷阱:

```
export function useFocusElement(showing, options = {}) {
  const elementRef = React.useRef(null)
  const trapRef = React.useRef(null)

  function focusElement() {
    const trap = createFocusTrap(elementRef.current, {
      escapeDeactivates: false,
      clickOutsideDeactivates: false,
      fallbackFocus: '[tabindex="-1"]',
      ...options,
    })

    trapRef.current = trap
    trap.activate()
  }

  function focusTrigger() {
    trapRef.current.deactivate()
  }

  React.useEffect(
    () => {
      if (showing) focusElement()
      else focusTrigger()
    },
    [showing]
  )

  return {
    bind: { ref: elementRef },
  }
} 
```

下面是正在发生的事情:我们创建两个 refs:我们的`elementRef`绑定到我们的弹出菜单，而我们的`trapRef`引用我们的焦点陷阱实例化。当`showing`道具改变时，我们要么聚焦`elementRef`，要么将焦点返回到触发元素。

这个钩子不会创建任何额外的 dom 元素，而且当出于可访问性的原因需要管理焦点时，在不同的组件中重用它非常容易。我在弹出菜单、模态菜单和下拉菜单中使用过它。我建议在使用 bind 对象时保持一致，它包含类似于`ref`的东西，但也可以包含额外的函数，如`onKeyDown`、`onMouseOver`等。

您可以在 [sancho-ui](https://github.com/bmcmahen/sancho/blob/master/src/Hooks/focus.ts) 中看到`useFocus`钩子的完整实现，并看到它是如何在 [Popover](https://github.com/bmcmahen/sancho/blob/master/src/Popover.tsx) 实现中使用的。

#### 提示 3: useState 接受回调

当使用带有回调的`useState`时，回调只在初始挂载时运行。这对于运行昂贵的计算很有用。比较这两个例子:

```
let counter = 0

function Component() {
  const [number] = useState(counter++)
  return <div>{number}</div>
} 
```

在这个例子中，每当我们的`Component`更新时，我们的`counter`就会增加。请注意，这将*而不是*更新我们的`number`状态，因为默认状态值只能在组件初始安装时设置一次。

让我们将`useState`转换成使用回调。

```
let counter = 0

function Component() {
  const [number] = useState(() => counter++)
  return <div>{number}</div>
} 
```

这只会在组件的整个生命周期中增加一次 uid，即使它重新呈现。和上面的例子一样，我们的`number`会保持不变。

下面的沙盒展示了这些差异:

(这是一篇发表在我 benmcmahen.com 博客上的文章。点击这里[可以在线阅读。)](https://www.benmcmahen.com/using-hooks-for-better-components/)