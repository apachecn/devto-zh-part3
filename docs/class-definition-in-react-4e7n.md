# React 中的类定义

> 原文：<https://dev.to/kayut/class-definition-in-react-4e7n>

React 中的一个类组件需要有一个像下面这个例子的构造函数吗:

```
class App extends React.Component {
    constructor() {
        super()
        this.state = {
            count: 0
        }
    }
render() {
  ...
}
} 
```

或者现在我们可以不用构造函数定义一个类组件，就像下面这个例子？:

```
class App extends React.Component {
        this.state = {
            count: 0
        }
render() {
  ...
}
} 
```

哪个是正确的？我们还需要为一个类组件写构造函数吗？