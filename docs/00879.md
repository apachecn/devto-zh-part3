# 通过重构 React 组件学习公共类字段语法

> 原文：<https://dev.to/guin/learn-public-class-field-syntax-by-refactoring-a-react-component-njh>

当您在阅读 React 代码时，您会遇到完成同一件事情的许多不同的方法。作为一个初学者，这可能是一个容易混淆的道路。您可能看到的差异之一是使用公共类字段语法的 React 类组件。

这种语法不是 React 特有的，但是在编写任何 JavaScript 类时都可以使用。在深入研究之前，注意一下公共和私有类字段声明仍然是 TC39 过程的第 3 阶段的一个特征是很有用的。然而，这个特性可以通过像 Babel 这样的构建系统来使用(如果您正在编写 React 代码，您可能已经在使用它了)。

就我个人而言，当我看到一些代码通过多个步骤重构，而不仅仅是初始和最终重构时，我会更好地将事情内在化。我们有一个筹款组件，它有一些状态并呈现两个组件。一个是带有当前金额和目标两个输入的表单，另一个显示这些数据。我们将重构这个组件以使用公共类字段语法。

这里是原始的和最终的组件，以供参考。

## 原始组件

```
import React from 'react';
import ProgressForm from './ProgressForm.js';
import ProgressBar from './ProgressBar.js';

class Fundraiser extends React.Component {
  constructor(props) {
    super(props);
    this.handleAmountChange = this.handleAmountChange.bind(this);
    this.handleGoalChange= this.handleGoalChange.bind(this);
    this.state = { amount: '',
                   goal: ''
                  };
  }

  handleAmountChange(amount) {
    this.setState({ amount });
  }

  handleGoalChange(goal) {
    this.setState({ goal })
  }

  render() {
    const { goal, amount } = this.state
    return (
      <>
        <ProgressBar amount={amount} goal={goal} />
        <ProgressForm
          onAmountChange={this.handleAmountChange} amount={amount}
          onGoalChange={this.handleGoalChange} goal={goal}
        />
      </>
    );
  }
}

export default Fundraiser; 
```

## 使用公共类字段语法重构

```
import React from 'react';
import ProgressForm from './ProgressForm.js';
import ProgressBar from './ProgressBar.js';

class Fundraiser extends React.Component {
  state = { amount: '',
            goal: ''
          };

  handleAmountChange = (amount) => {
    this.setState({ amount });
  }

  handleGoalChange = (goal) => {
    this.setState({ goal })
  }

  render() {
    const { goal, amount } = this.state
    return (
      <>
        <ProgressBar amount={amount} goal={goal} />
        <ProgressForm
          onAmountChange={this.handleAmountChange} amount={amount}
          onGoalChange={this.handleGoalChange} goal={goal}
        />
      </>
    );
  }
}

export default Fundraiser; 
```

## 让我们看看有什么变化！

在我们最初的组件中，我们有一个类，其中包含一些用于事件处理程序的方法，`handleAmountChange`和`handleGoalChange`、`render`方法和一个`constructor`，我们在其中设置一些状态，并处理正确的 this 绑定，以确保我们的方法在正确的 this 上下文中按照我们的预期工作。

我们可以轻松重构的第一件事是从构造函数中取出状态变量。使用公共类字段语法，我们可以将其直接定义为类的属性或“字段”。

```
...
class Fundraiser extends React.Component {
  constructor(props) {
    super(props);
    this.handleAmountChange = this.handleAmountChange.bind(this);
    this.handleGoalChange= this.handleGoalChange.bind(this);
  }
  state = { amount: '',
            goal: ''
          };
... 
```

现在，构造函数中唯一剩下的就是方法的绑定。我们也可以通过将方法重构为箭头函数来将这些从构造函数中移除，这意味着我们可以完全摆脱构造函数！我们可以这样做，因为 arrow 函数没有自己的`this`关键字，所以`this`关键字是词汇绑定的。

```
...
class Fundraiser extends React.Component {
  state = { amount: '',
            goal: ''
          };

  handleAmountChange = (amount) => {
    this.setState({ amount });
  }

  handleGoalChange = (goal) => {
    this.setState({ goal })
  }
... 
```

我们的代码现在看起来可读性很好，样板文件更少了！

## 但是，我们可能并不总是想使用公共类字段语法...

和往常一样，在编写代码时，我们会对所做的决定进行权衡。当一个方法被添加到一个类中时，本质上与将该方法添加到函数原型是一样的。当一个方法在原型上时，不管你创建多少个类的实例，它都只被定义一次。当我们像上面那样使用类字段来添加方法时，代码可能看起来更易读，但是类字段被添加到实例本身，而不是原型。如果我们创建 100 个实例，我们将创建该方法的 100 个新副本！

如果您有任何问题、意见或反馈，请告诉我。关注每周关于 JavaScript、React、Python 和 Django 的新帖子！