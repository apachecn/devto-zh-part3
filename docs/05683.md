# 使用自定义钩子代替“渲染道具”

> 原文：<https://dev.to/emeka/using-custom-hooks-in-place-of-render-props-38mf>

[![image extracted from https://www.udemy.com/react-hooks-tutorial](img/cc1f7a0ea27224b88273f7c23ddfb7a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8ZFuZgNR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2v9z9mh5dhxm311vxwkq.jpg)

React 的一个有趣但有时难以理解的部分是跨各种组件重用有状态逻辑。我们都喜欢只编写一次这个逻辑，然后在任何需要它的组件中重用它，而不是在我们需要它的时候重写某个有状态的逻辑。实现这一点的常见模式是“渲染道具”。具有渲染属性的组件接受一个函数，该函数返回一个 React 元素并调用它，而不是实现它自己的渲染逻辑。这个组件可以称为“容器组件”，而我们返回的 React 元素或组件可以称为“表示组件”。

```
 // example 1
<Container render={prop => (
 <Presentation {...props} />
)} />

// example 2
<Container children={prop => (
 <Presentation {...props} />
)} />

// example 3
<Container>
 {props => (
    <Presentation {...props} />
  )}
</Container> 
```

上面的三个例子实现了 render props 模式，其中“Container”是我们的容器组件，用于呈现一个表示组件...嗯，实际上。我们可以将任何需要重用的有状态逻辑放在容器组件中，如果需要的话，可以将结果和“更新功能”一起传递给它所呈现的任何其他组件。简而言之，这就是“渲染道具”。

## 有什么替代方案？

如果我们没有容器，而是有一个自定义的钩子来实现这个逻辑，并返回带有“更新功能”的结果，那会怎么样？我说的“更新函数”是指更新容器状态或钩子结果的函数。我们如何实现这一点正是我们在这里的原因。让我们利用一个“猫和老鼠”的例子，我在 React 官方文档中找到了渲染道具。我们将看看“渲染道具”的例子，并尝试重构它来使用一个定制的钩子。

## 渲染道具示例

如果我们有一个组件监听鼠标移动，并将指针位置设置为如下所示的状态:

```
class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>

        {/*
          Instead of providing a static representation of what <Mouse> renders,
          use the `render` prop to dynamically determine what to render.
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
} 
```

任何需要根据鼠标位置呈现元素的组件都可以由我们的鼠标组件来呈现。让我们定义一个 Cat 组件，它呈现一只猫追逐鼠标指针的图像。

```
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: 
      mouse.y }} />
    );
  }
} 
```

我们不需要重写获取指针位置的逻辑，而是可以像这样从鼠标组件扩展这个逻辑:

```
class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
} 
```

这将使猫组件把鼠标的位置作为道具传递下去。我们可以根据需要在尽可能多的组件中重用逻辑。

## 把钩子替代

我们将去掉“鼠标”组件，代之以创建一个钩子来实现鼠标逻辑。

```
export function useMouse(initialValue = {x:0, y:0}) {
  const [position, setPosition] = useState(initialValue);
  const handleMouseMove = (event) => {
    setPosition({
      x: event.clientX,
      y: event.clientY
    });
  }
  return [position, handleMouseMove];
} 
```

我们刚刚定义了一个名为 useMouse 的钩子。按照惯例，函数名应该以“use”开头，这样人们就知道它是一个钩子。我们的 useMouse 钩子返回鼠标的位置和一个更新该位置的函数。让我们看看如何在我们的 Cat 组件中使用它。

```
function Cat() {
  const [position, setMousePosition] = useMouse();

  return (
    <div style={{ height: '100%' }} onMouseMove={setMousePosition}>
      <img src="/cat.jpg" style={{ position: 'absolute', left: position.x, top: 
      position.y }} />
    );
    </div>
} 
```

想到什么词，简单？..整洁吗？..简洁？也许三个都有。任何需要在移动时获取鼠标位置的组件都可以使用这个钩子。使用这种模式提高了复杂的 react 代码的可读性和可维护性，也有助于避免组件树过于庞大和深度嵌套。我们可以通过创建自定义钩子来重用身份验证状态、用户信息甚至表单处理逻辑。它们也可以用来代替 React 中的 hoc(高阶组件)。