# 我们什么时候应该用合适的方式来设置状态？

> 原文：<https://dev.to/briang123/when-should-we-use-the-appropriate-way-to-set-state-3hh0>

我仍然是 React 的新手，并试图理解我们什么时候会使用 React 团队所说的设置状态的正确方法。这两种方法我都见过，但是当一种方法比另一种方法更有用时，我会感到困惑。

为什么我们不总是使用下面的选项 2？

[这是本节中 React 文档的链接](https://reactjs.org/docs/state-and-lifecycle.html#using-state-correctly)

### 不直接修改状态(选项 1)

```
this.setState({comment: 'Hello'}); 
```

Enter fullscreen mode Exit fullscreen mode

### 状态更新可能是异步的(选项 2)

```
this.setState((state, props) => ({
  counter: state.counter + props.increment
})); 
```

Enter fullscreen mode Exit fullscreen mode

[![alt text](img/48c945b05e02c188a6f6fdca73664658.png "Wondering...")](https://i.giphy.com/media/5XRB3Ay93FZw4/giphy.gif)