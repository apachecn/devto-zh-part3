# React - ES6 课堂技巧

> 原文：<https://dev.to/kozakrisz/react---es6-tricks-in-classes-33je>

我阅读了几篇带有 React 示例代码的文章。例如，我刚刚阅读了一篇关于 React 的有状态和无状态组件的短文。我看到了很多“老办法”的解决方案，所以我想和你分享一个关于基于类的组件及其状态的技巧。您现在可以使用 [Create React App](https://facebook.github.io/create-react-app/) 来使用和尝试它们！

## 构造物

仅仅因为函数绑定，你不需要在基于类的组件中定义构造函数。

**🌙**老办法:

```
constructor(props) {
  super(props);
  this.state = {
    value: ""
  };
  this.handleChange = this.handleChange.bind(this);
} 
```

Enter fullscreen mode Exit fullscreen mode

**🌞**新道:

```
class Test extends PureComponent {

  constructor(props) {
    super(props);
    this.state = {
     value: ""
    };
  }

  const handleChange = () => {
    // Handle change...
  }

  render() {
   return(
    <input value={this.state.value} onChange={this.handleChange} />
   );
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

而且这还不是结束！本地状态也可以在类级别上定义:

```
class Test extends PureComponent {

  state = {
    value: ""
  }

  const handleChange = () => {
    // Here comes the change things
  }

  render() {
   return(
    <input value={this.state.value} onChange={this.handleChange} />
   );
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个解决方案，你不需要使用构造函数，也不需要用函数的名字来绑定函数。我知道这是品味的问题，但我认为我们可以用这种方式写出清晰易读的代码。