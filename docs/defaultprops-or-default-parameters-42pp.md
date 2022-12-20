# 默认属性或默认参数

> 原文：<https://dev.to/raerpo/defaultprops-or-default-parameters-42pp>

> TLDR；当您使用默认参数在 React 组件中设置默认属性时，您不会得到属性类型验证，并且您还需要在您想要使用这些属性的每个类方法中定义这些默认参数。

最近，在我做的一次代码审查中，我在一个 React 组件中看到了这样一段代码:

```
render() {
  const {
    count = 0
  } = this.props;
  return <div>{ count }</div> } 
```

我的第一个想法是，这是错误的，因为你应该通过添加一个名为`defaultProps`的属性或者使用一个静态方法来定义默认属性。

```
// Setting a defaultProps property
class App extends React.Component {
  render() {
    const {count} = this.props;
    return <div>
      {count}
    </div>
  }
}
App.defaultProps = {
  count: 0
}

// Adding an static method to the component
class App extends React.Component {
  static defaultProps = {
    count: 0
  }
  render() {
    const {count} = this.props;
    return <div>
      {count}
    </div>
  }
} 
```

但是在尝试了代码之后，令我惊讶的是，它成功了！所以我想知道这在 React 组件中是否是一个有效的实践，因为我还没有在任何地方看到它。即使这行得通，也有一些事情行不通，但它们不会马上变得如此明显。

## 属性类型验证

根据反应文件:

> propTypes 类型检查发生在 defaultProps 解析之后，因此类型检查也将应用于 defaultProps。

这意味着，当您定义 proptypes 时，它的验证是在您使用`static defaultProps`或`defaultProps`方法设置的属性和默认属性中进行的，而不是在您使用默认参数时进行的。

例如，如果我们这样做:

```
class App extends React.Component {
  render() {
    const { count } = this.props
    return <div>{ count }</div>
  }
}

App.propTypes = {
  count: PropTypes.number
}

App.defaultProps = {
  count: 'hello'
} 
```

我们会得到这个警告:

```
index.js:1446 Warning: Failed prop type: Invalid prop `count` of type `string` supplied to `App`, expected `number`. 
```

但是如果我们使用默认参数:

```
class App extends React.Component {
  render() {
    const { count = 'hello' } = this.props
    return <div>{ count }</div>
  }
}

App.propTypes = {
  count: PropTypes.number
} 
```

我们不会得到任何错误或警告，因为 React 无法进行运行时分析。

## 类方法之间的 props 值可能不一致

当我们定义`defaultProps`时，它内部定义的值将在 React 组件内部的每个方法上可用，但是如果我们使用默认参数，我们必须在每个方法上定义默认值。我举个例子来解释一下。

```
class App extends React.Component {
  componentDidMount() {
    const { count } = this.props
    console.log('count in CDM: ', count)
  }
  render() {
    const { count } = this.props
    console.log('count in Render: ', count)
    return <div>{ count }</div>
  }
}

App.propTypes = {
  count: PropTypes.number
}

App.defaultProps = {
  count: 10
} 
```

如果我们运行这段代码，我们将得到:

```
count in Render:  10
count in CDM:  10 
```

正如你所看到的，每个方法的默认值都是一样的，所以我们可以确定，如果没有传递属性，所有地方的默认值都是一样的。

相比之下，如果使用默认参数，每个类方法的属性可能不同。

```
class App extends React.Component {
  componentDidMount() {
    const { count } = this.props
    console.log('count in CDM: ', count)
  }
  render() {
    const { count = 10 } = this.props
    console.log('count in Render: ', count)
    return <div>{ count }</div>
  }
}

App.propTypes = {
  count: PropTypes.number
} 
```

在这个例子中，我们将得到这个:

```
count in Render:  10
count in CDM:  undefined 
```

同样的道具在组件的不同部分有不同的值是非常糟糕的。

## 最后的想法

这类事情提醒我，几乎总是**有可能写出正确的代码**,所以我们需要理解我们在编写应用程序时所做决定的影响。当您可以密切关注正在发生的一切时，这可以被视为小组件的问题，但是一旦应用程序变大，就很难跟踪这种错误。