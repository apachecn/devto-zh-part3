# 带有 React 的类属性

> 原文：<https://dev.to/daleljefferson/class-properties-with-react-3aa6>

在 JavaScript 中处理`this`绑定可能是一件非常痛苦的事情。在本文中，我将向您展示三种避免绑定问题的方法。Class Properties 是新的 JavaScript 语法，用于创建具有正确绑定的处理函数。

## 匿名箭头功能

这将在每次渲染时创建一个新的函数，可能会使任何记忆按钮的努力无效。

```
class AwesomeProject extends Component {
  constructor() {
    super();

    this.state = {n: 0};
  }

  onPress() {
    this.setState({n: this.state.n + 1});
  }

  render() {
    return <Button onPress={() => this.onPress()} />;
  }
} 
```

## 绑定

相同的函数在每次渲染中重复使用，这是 ES6 的最佳实践。

```
class AwesomeProject extends Component {
  constructor() {
    super();

    this.state = {n: 0};
    this.onPress = this.onPress.bind(this);
  }

  onPress() {
    this.setState({n: this.state.n + 1});
  }

  render() {
    return <Button onPress={this.onPress} />;
  }
} 
```

## 类属性

我们不再需要构造函数，并避免手动绑定处理程序。我个人认为这是最干净的做法。

```
class AwesomeProject extends Component {
  state = {n: 0};

  onPress = () => {
    this.setState({n: this.state.n + 1});
  };

  render() {
    return <Button onPress={this.onPress} />;
  }
} 
```