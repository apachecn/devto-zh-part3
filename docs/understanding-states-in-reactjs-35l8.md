# 理解反应中的状态

> 原文：<https://dev.to/93alan/understanding-states-in-reactjs-35l8>

嗨，这是我的第一篇文章，它的目标是让初学者了解组件的状态。

在 ReactJS 中，使用组件中的状态非常有用。

我们将首先在我们的*构造函数*中声明并初始化我们的状态，例如:

```
constructor() {

    this.state = ({ Manipulated: false });
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有进一步的函数或代码需要*改变*或*更新*一个组件的状态，那么我们将使用*设置状态*例如:

```
componentWillReceiveProps(NewProps) {

    var MyBool = NewProps.ManipulativeBool;
    this.setState({ Manipulated: MyBool });
} 
```

Enter fullscreen mode Exit fullscreen mode

*setState* 还将触发受该状态变量影响的任何 DOM 元素的*重新呈现*。

当我们在 render 方法中呈现我们的内容时，组件中的状态变得非常有用，可以根据像这样的特定标准来确定要做什么；

```
render() {

    var Manipulated = this.state.Manipulated;

    return (
        {Manipulated === true ? <p>Our state has been manipulated!</p> : <p>Our state is still false</p>}
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个简单的例子中，我们将根据我们设置的操作布尔值的状态输出和呈现不同的内容。

如果布尔值为真，那么只有这样它才会渲染并允许我们的内容*我们的状态已经被成功地渲染和显示。而在其他情况下它将保持为*我们的状态仍然是假的*。*

这只是对通过使用 ReactJS 中的状态可以获得的强大功能的一个深入了解，希望这是对初学者的一个小技巧。