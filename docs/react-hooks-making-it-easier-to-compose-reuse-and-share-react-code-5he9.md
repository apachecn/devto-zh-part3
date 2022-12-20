# React 挂钩:使 React 代码的编写、重用和共享变得更加容易

> 原文：<https://dev.to/basal/react-hooks-making-it-easier-to-compose-reuse-and-share-react-code-5he9>

挂钩是 React 中即将推出的一个特性，它使您能够在不编写类的情况下使用状态和许多其他 React 特性。这对 React 代码的未来有一些重要的影响，特别是关于组件将如何组成。

正如来自脸书 React 团队的官方文档所提供的，钩子的动机是为了解决他们在编写和维护 React 组件的五年中遇到的一些问题。这些问题是:

1.  很难在组件之间重用有状态逻辑
2.  复杂的组件变得难以理解
3.  类混淆了人和机器

在这篇短文中，我们将关注 React 钩子如何解决第一个问题——在组件之间重用有状态逻辑的困难——因为它具有最广泛的影响。

## 重用有状态逻辑

在过去的几年里，React 中共享有状态逻辑的首选方式是高阶组件(hoc)和渲染道具。hoc 和 render props 都需要在应用程序组件树中添加一个额外的组件，而且可以说它们也使得推理代码中的共享逻辑变得更加困难。现在我们可以添加 React 钩子作为共享逻辑的一种方式。

让我们用一个简单的例子来比较 React 中处理横切关注点的选项，突出它们之间的区别。

## 高阶分量

[高阶组件](https://reactjs.org/docs/higher-order-components.html) (HOC)是 React 中广泛使用的重用组件逻辑的模式，它将组件包装在目标组件周围，并通过其 props 将数据传递给目标组件。换句话说，高阶组件是一个函数，它将目标组件作为参数，并返回带有附加数据和功能的目标组件。

下面这个简单的例子展示了一个跟踪 web 应用程序中鼠标位置的高阶组件。

```
function withMousePosition(WrappedComponent) {
  return class extends Component {
    constructor(props) {
      super(props);
      this.state = { x: 0, y: 0 };
    }

    componentDidMount() {
      window.addEventListener("mousemove", this.handleMouseMove);
    }

    componentWillUnmount() {
      window.removeEventListener("mousemove", this.handleMouseMove);
    }

    handleMouseMove = event => {
      this.setState({
        x: event.clientX,
        y: event.clientY
      });
    };

    render() {
      return (
        <WrappedComponent
          {...this.props}
          mousePosition={this.state}
        />
      );
    }
  };
} 
```

在上面的包装类组件中，鼠标位置是通过浏览器窗口提供的 [mousemove 事件 API](https://developer.mozilla.org/en-US/docs/Web/Events/mousemove) 获得的。我们设置了一个事件侦听器，并更新保存鼠标位置坐标的状态。该类封装了功能，现在我们可以与其他组件共享它。

因此，使用高阶组件模式，函数`withMousePosition`将任何目标组件作为一个参数，并返回它的所有现有属性和一个附加属性:`mousePosition`坐标。

```
 function App(props) {
      const { x, y } = props.mousePosition;

      return (
        <div className="App">
          <h1>Higher-Order Component Method</h1>
          <h2>Move the mouse around!</h2>
          <p style={{ background: "orange" }}>
            The current mouse position is ({x}, {y})
          </p>
        </div>
      );
    }

    const AppWithMousePosition = withMousePosition(App); 
```

在这个例子中，我们与一个表示性的`App`组件共享了`mousePosition`坐标数据。动态鼠标位置显示在橙色段落中:

```
 <p style={{ background: "orange" }}>
        The current mouse position is ({x}, {y})
    </p> 
```

包装后的`AppWithMousePosition`组件可以呈现给`DOM` :

```
 ReactDOM.render(<AppWithMousePosition />, document.getElementById("root")); 
```

在下面的 [CodeSandbox](https://codesandbox.io/s/43z216n6y9) 中亲自尝试一下这种特别的方法:

[https://codesandbox.io/s/43z216n6y9](https://codesandbox.io/s/43z216n6y9)

## 渲染道具

一个[渲染道具](https://reactjs.org/docs/render-props.html)是一种在 React 组件之间共享代码的方式，使用一个其值为函数的道具。道具通常被称为`render`，因此术语称为“渲染道具”。

让我们看看前面介绍的鼠标位置示例在使用渲染道具实现时的样子:

```
 class MousePosition extends Component {
      constructor(props) {
        super(props);
        this.state = { x: 0, y: 0 };
      }

      componentDidMount() {
        window.addEventListener("mousemove", this.handleMouseMove);
      }

      componentWillUnmount() {
        window.removeEventListener("mousemove", this.handleMouseMove);
      }

      handleMouseMove = event => {
        this.setState({
          x: event.clientX,
          y: event.clientY
        });
      };

      render() {
        return (
          <div
            style={{ height: "100%", width: "100%" }}
            onMouseMove={this.handleMouseMove}
          >
            {this.props.render(this.state)}
          </div>
        );
      }
    } 
```

鼠标位置的有状态逻辑与我们之前在高阶组件中使用的相同。

HOC 方法和这个 render props 方法的区别在于，我们现在在类组件的 render 方法中指定了一个名为`render`的函数 prop，它将组件的状态作为参数，并将其呈现为类组件的子组件:

```
 render() {
        return (
            <div
            style={{ height: "100%", width: "100%" }}
          onMouseMove={this.handleMouseMove}
        >
          {this.props.render(this.state)}
        </div>
      );
    } 
```

请注意，在提到这种模式时，也使用了术语“作为子模式的功能”。

现在，我们可以用这个`MousePosition`组件包装任何目标组件，并通过`render` prop 传递它来动态呈现鼠标位置。与静态定义的高阶组件相比，这是一种共享有状态逻辑的动态方式。

> 顺便说一下，关于高阶组件和渲染道具的利弊的更多细节，请参见 React Router 的合著者迈克尔杰克逊的这篇优秀文章。

回到我们的例子，我们现在可以通过在其中组合`MousePosition`组件来呈现一个表示性的`App`组件。我们在天蓝色的`<p>`元素中呈现动态鼠标位置，该元素通过`render`属性中的函数传递:

```
 function App() {
      return (
        <div className="App">
          <h1>Render Props Method</h1>
          <h2>Move the mouse around!</h2>
          <MousePosition
            render={mousePosition => (
              <p style={{ background: "skyblue" }}>
                The current mouse position is ({mousePosition.x}, {mousePosition.y})
              </p>
            )}
          />
        </div>
      );
    } 
```

总的来说，与监听`mousemove`事件和存储鼠标位置坐标相关的行为已经封装在`MousePosition`组件中，并且可以通过这种“渲染道具”模式在任何其他组件中灵活使用。这是一个具有可重用、可共享状态逻辑的可组合组件的例子。

在下面的 [CodeSandbox](https://codesandbox.io/s/rjprzkj29p) 中亲自尝试渲染道具的方法:

[https://codesandbox.io/s/rjprzkj29p](https://codesandbox.io/s/rjprzkj29p)

## 反应钩

现在，让我们看看如何使用“钩子”来实现在应用程序中重用有状态逻辑的目标，使用完全相同的鼠标位置示例:

```
 function useMousePosition() {
      const [mousePosition, setMousePosition] = useState({ x: 0, y: 0 });

      function handleMouseMove(event) {
        setMousePosition({
          x: event.clientX,
          y: event.clientY
        });
      }

      useEffect(() => {
        window.addEventListener("mousemove", handleMouseMove);

        return () => {
          window.removeEventListener("mousemove", handleMouseMove);
        };
      }, []);

      return mousePosition;
    } 
```

注意，我们已经创建了一个“[定制钩子](https://reactjs.org/docs/hooks-custom.html)”，这里称为`useMousePosition`。它是一个函数组件，不是类组件，但它确实封装了状态！

对于我们的鼠标位置示例，我们在自定义钩子函数体中使用了两个不同的 React 钩子:

*   [状态挂钩](https://reactjs.org/docs/hooks-state.html) : `useState`
*   [效果钩](https://reactjs.org/docs/hooks-effect.html) : `useEffect`

挂钩让我们可以向函数组件添加 React 状态，而不必将它们转换成类组件。`useState`函数钩子将 state 的初始值作为参数，并返回一个包含状态值(`mousePosition`)的两元素数组，以及一个更新该值的函数(`setMousePosition`)。你可以在函数的底部看到，我们正在从函数中返回`mousePosition`状态值。

`useEffect`钩子允许你在函数组件中执行副作用。副作用的例子包括从 API 获取数据、监听浏览器事件和手动更改 DOM。`useEffect`钩子执行与类组件中的生命周期方法`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`组合执行的任务相同的任务。

`useEffect`将一个回调函数(称为“效果”)作为它的第一个参数，并在组件的每次渲染后运行它。在我们的例子中，效果是在组件被挂载时，在第一次呈现之后设置`mousemove`事件监听器。从效果返回的回调，如果指定的话，在组件被卸载之前起到“清理”的作用。在我们的示例中，我们在卸载时删除了事件侦听器。

```
 useEffect(() => {
      window.addEventListener("mousemove", handleMouseMove);

      return () => {
        window.removeEventListener("mousemove", handleMouseMove);
      };
    }, []); 
```

在效果回调中，我们设置了一个名为`handleMouseMove`的`mousemove`事件监听器，每当用户移动鼠标时，它自己就会用更新后的鼠标坐标调用`setMousePosition`。

`useEffect`函数钩子的第二个参数，如果指定的话，是一个特定状态值的**数组，每当值更新时，效果将在该数组上运行。也就是说，该效果将在对这些特定状态值的更新所触发的组件的每次重新渲染时运行。如果**没有指定数组**，那么默认行为是重新渲染组件，并在任何状态值更新时触发效果。**

在我们的例子中，我们正在传递一个**空数组** `[]`，这意味着该效果不依赖于我们组件中的任何状态值更新，也就是说，我们的效果只在挂载时运行，它将在卸载时清除，但不会在任何`mousePosition`更新时运行。事件监听器已经更新了`mousePosition`，所以当发生这种情况时，没有必要重新呈现组件。

我们的定制钩子完全复制了之前在 HOC 和 render-props 模式中使用的类组件的行为。它以一种**非常紧凑的**、**易于理解的**和**可重用的**方式完全封装了我们需要的行为。

现在，我们可以在任何其他组件中共享这种动态鼠标位置功能。让我们在我们的表示性`App`组件:
中调用我们的定制钩子`useMousePosition`

```
 function App() {
      const { x, y } = useMousePosition();

      return (
        <div className="App">
          <h1>React Hook Method</h1>
          <h2>Move the mouse around!</h2>
          <p style={{ background: "palegreen" }}>
            The current mouse position is ({x}, {y})
          </p>
        </div>
      );
    } 
```

这里，我们用浅绿色的`<p>`标签呈现动态鼠标坐标。

在 [CodeSandbox](https://codesandbox.io/s/548z479m0n) 中亲自尝试钩子方法:

## 总结

现在你已经看到了相同的鼠标位置示例以三种不同的方式实现:**高阶组件**、**渲染道具**和**钩子**。

很明显，迄今为止最优雅的和最容易理解的**代码是在 React 钩子方法中找到的。此外，**需要更少的代码**来实现**相同的结果**。**

钩子使得将有状态的组件逻辑、数据和功能分离到一个封装的结构中变得前所未有的容易，从而方便了重用和共享。这一点的影响不应该被低估。对于 React 和每个使用它的人来说，这是一个巨大而激动人心的发展！