# React 钩子:将类组件迁移到函数中并使用钩子

> 原文：<https://dev.to/elanandkumar/react-hooks-migrate-class-component-to-hooks-3879>

我们都知道 react hooks 已经准备好了，我们已经开始使用它了。但是，如果我们有任何现有的项目，我们想转移到 react hooks 呢？当然，我们有被写成类组件的组件。

这篇文章将指导你改变一个现有的基于类的组件来反应基于钩子的组件。但是，这篇文章并不打算深入钩子的细节。

这是 react 文档对`hooks`的描述。

> 钩子让我们不用写类就可以使用状态和其他 React 特性。

让我们从一个示例应用程序开始。这是一个简单的计数器应用程序。代码如下所示:

```
const initialData = {
  count: 0,
  maxLimit: 10
};

class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: this.props.initialCount
    };
  }
  onIncrement = () => {
    this.setState({ count: this.state.count + 1 });
  };
  onDecrement = () => {
    this.setState({ count: this.state.count - 1 });
  };
  render() {
    return (
      <div>
        <button
          onClick={this.onDecrement}
          disabled={this.state.count === this.props.initialCount}
        >
          -
        </button>
        <div>{this.state.count}</div>
        <button
          onClick={this.onIncrement}
          disabled={this.state.count === this.props.maxLimit}
        >
          +
        </button>
      </div>
    )
  }
}

class CounterApp extends React.Component {
  render() {
    const { initialData } = this.props;
    return (
      <React.Fragment>
        <h2>Counter Example</h2>
        <Counter
          initialCount={initialData.count}
          maxLimit={initialData.maxLimit}
        />
      </React.Fragment>
    );
  }
}

ReactDOM.render(
  <CounterApp initialData={initialData} />,
  document.getElementById('root')
); 
```

如你所见，我已经创建了一个简单的计数器应用程序。我创建的类组件有:

*   这是主要的应用组件
*   `Counter`:实际计数器组件

请注意，这段代码是为这篇文章准备的，只是为了解释。请忽略您可能看到的任何需要改进的地方。☺

因此，要将一个类组件转换成 functional/hooks 组件，步骤如下:

> 首先选择最小的类组件，即自下而上的方法会更好。

1.  将类组件拥有的所有代码移动到 render 方法中，并使其工作。例如，事件处理函数或任何其他函数或代码。
2.  更新代码，对`props` & `states`使用对象分解。
3.  一旦， **#2** 完成，在`class`语法前添加一行，如下所示。

```
const Counter = () => {};
class Counter extends React.Component {
  /*...code goes here */
}; 
```

1.  将 render 方法中的所有内容移动到更新的函数组件代码块中。

```
const Counter = () => {
  //...code inside render methods of class component goes here
};
class Counter extends React.Component {
  //...Rest of the code
  render() {
    // This will be blank now
  }
}; 
```

1.  将道具的非结构化代码移动到功能组件的附件中。从代码块中移除代码。

```
const Counter = ({ maxLimit, initialCount }) => {
  /*...rest of the code */
}; 
```

1.  更新组件以使用像`useState`这样的钩子或者任何其他可能需要的钩子。例如。

```
const Counter = ({ maxLimit, initialCount }) => {
  //...Rest of the code
  const [count, setCount] = React.useState(initialCount);
  //...Rest of the code
}; 
```

> 如果你有一个以上的状态属性，请将它改为分别使用钩子。

我不会在这里解释如何编写钩子的语法。请阅读相同的官方文档。

1.  最后，更新 return 语句代码块，使用新创建的 props(非结构化的)或使用钩子的状态。、或任何其他改变都应相应地并入。并完全删除类组件代码行。

好吧，这可能看起来太多，不太清楚。让我们来看看这个例子。

## 计数器组件

*   在 render 方法内移动`onIncrement`和`onDecrement`代码块。定义为`const`，将功能`this.onIncrement`和`this.onDecrement`分别更新为`onIncrement`和`onDecrement`。
*   在渲染方法中对`props`和`state`使用对象去结构化。在我们的示例中，您可以执行如下操作:

```
const { maxLimit, initialCount } = this.props;
const { count } = this.state; 
```

*   现在，就在`class Counter extends React.Component {`行之前，创建一个类似如下的行:

```
const Counter = () => {}; 
```

*   从`render`方法中剪切所有内容，并将其粘贴到`const Counter = () => {}`的花括号中
*   将非结构化的`props`移动到括号中。**线 2** 被注释掉**线 1** 现在有道具`{maxLimit, initialCount}`。

```
const Counter = ({ maxLimit, initialCount }) => {
  // const { maxLimit, initialCount } = this.props;
  const { count } = this.state;
  // ....Rest of the code
} 
```

*   接下来，我们需要改变我们的`this.state`。我们将在这里使用`useState`钩子。更新**线 2** 处的代码，如下所示:

```
const Counter = ({ maxLimit, initialCount }) => {
  const [count, setCount] = React.useState(initialCount);
  // ....Rest of the code
} 
```

我们修改的代码是使用钩子的语法。

*   现在是时候更新我们的函数，使用我们用钩子定义的`setCount`。改变我们在`onIncrement`和`onDecrement`中的代码`this.setState`。更新后的代码如下所示:

```
const Counter = ({ maxLimit, initialCount }) => {
  // ....Rest of the code
  const onIncrement = () => {
    setCount(count + 1);
  };
  const onIncrement = () => {
    setCount(count - 1);
  };
  // ....Rest of the code
} 
```

*   现在，相应地更新 return 语句的代码，以使用新的`count`状态(使用钩子创建的)和非结构化的`props`。并且，完全删除`class Counter extends React.Component {}`代码。

组件`Counter`的最终代码应该如下所示:

```
const Counter = ({ maxLimit, initialCount }) => {
  const [count, setCount] = React.useState(initialCount);
  const onIncrement = () => {
    setCount(count + 1);
  };
  const onDecrement = () => {
    setCount(count - 1);
  };
  return (
    <div>
      <button
        onClick={onDecrement}
        disabled={count === initialCount}
      >
        -
      </button>
      <div>{count}</div>
      <button
        onClick={onIncrement}
        disabled={count === maxLimit}
      >
        +
      </button>
    </div>
  )
} 
```

所以，你必须注意到在最后，没有使用`this`,代码看起来也干净多了。

## 计数器 App

主要的`CounterApp`组件没有太多的内容，所以我们可以毫不费力地将其转换成功能组件。`CounterApp`的最终代码如下所示。

```
const CounterApp = ({ initialData: {count, maxLimit} }) => {
  return (
    <>
      <h2>Counter Example</h2>
      <Counter initialCount={count} maxLimit={maxLimit} />
    </>
  )
} 
```

> 想知道`<>`和`</>`是什么？这是一种避免我们以前创建的额外元素层的方法。所以，用这个或者用文章开头所示的`React.Fragment`。

现在，我们已经重构了代码，完整的计数器应用程序代码如下所示:

```
const initialData = {
  count: 0,
  maxLimit: 10
};
// added style to make it look better, in case you want to see
const counterStyles = {
  h1: {
    maxWidth: "185px",
    boxShadow: "5px 5px 4px #888888"
  },
  container: {
    display: "flex",
    alignItems: "center"
  },
  button: {
    width: "30px",
    borderRadius: "50%",
    borderStyle: "none",
    fontSize: "22px",
    backgroundColor: "khaki",
    height: "30px",
    paddingBottom: "5px",
    cursor: "pointer"
  },
  countDiv: {
    minWidth: "80px",
    padding: "0 15px",
    textAlign: "center",
    height: "30px",
    lineHeight: 2,
    border: "1px solid",
    borderRadius: "10px",
    margin: "0 5px"
  }
};

const Counter = ({ maxLimit, initialCount }) => {
  // styles...
  const { container, button, countDiv } = counterStyles;
  const [count, setCount] = React.useState(initialCount);
  const onIncrement = () => {
    setCount(count + 1);
  };
  const onDecrement = () => {
    setCount(count - 1);
  };
  return (
    <div style={container}>
      <button
        style={button}
        onClick={onDecrement}
        disabled={count === initialCount}
      >
        -
      </button>
      <div style={countDiv}>{count}</div>
      <button
        style={button}
        onClick={onIncrement}
        disabled={count === maxLimit}
      >
        +
      </button>
    </div>
  );
};

const CounterApp = ({ initialData: {count, maxLimit} }) => {
  return (
    <>
      <h2 style={counterStyles.h1}>Counter App: Hooks</h2>
      <Counter initialCount={count} maxLimit={maxLimit} />
    </>
  );
};

ReactDOM.render(
  <CounterApp initialData={initialData} />,
  document.getElementById('root')
) 
```

# 总结

通过这篇文章，我们学到了一些关于如何将基于类的组件转换成基于函数和钩子的组件的技巧。我希望这将有助于您迁移现有的代码库。

我还为这篇文章录制了一段视频。以下是链接。

[https://www.youtube.com/embed/OBPhmCHApF0](https://www.youtube.com/embed/OBPhmCHApF0)

欢迎订阅该频道，并探索其他视频。

如果有任何问题/顾虑，请随时联系我们。

快乐学习！