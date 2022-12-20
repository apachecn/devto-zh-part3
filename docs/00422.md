# 功能组件中的递增状态

> 原文：<https://dev.to/stephencweiss/incrementing-state-in-functional-components-5fka>

我最近在查看[材质的 UI 文档](https://material-ui.com/components/steppers/#horizontal-linear)，寻找它们的`<Stepper>`组件。在水平线性步进器的实现中，他们使用了`useState` React 钩子来设置状态。

这个实现引起了我的注意，因为它使用了一个没有在其他地方定义的参数`prevActiveStep`。

```
function handleBack() {
  setActiveStep(prevActiveStep => prevActiveStep - 1)
} 
```

深入研究后，我意识到对于类组件来说，`useState`的行为与`setState`方法非常相似。尽管两者都可以为 state 中的特定元素设置值，但它们也可以接受一个函数。

这可能是这样的。

# React 钩子版本

```
import React, { useState } from ‘react’;

function MyComponent() {

  const [activeStep, setActiveStep] = React.useState(0);

  function handleBack() {
    setActiveStep(prevActiveStep => prevActiveStep - 1);
  }

  return (
    ...
    <div>
      <Button onClick={handleBack} >
        Back
      </Button>
    </div>
    ...
}

export default MyComponent; 
```

# React 类组件版本

为了更好地理解这一点，让我们来看看这个类组件是什么样子的。

```
import React, { useState } from ‘react’;

class MyComponent{
  constructor(props) {
    super(props);
    this.state = {
      activeStep: 0,
    }
  }

  function handleBack() {
    this.setState( prevState => ({ activeStep: prevState.activeStep - 1});
  }

  return (
    ...
    <div>
      <Button onClick={handleBack} >
        Back
      </Button>
    </div>
    ...
}

export default MyComponent; 
```

我欣赏这种方法的简洁，虽然只是为了明确，它的工作原理与下面的一样，不需要在`setState`中重新分配状态变量(这是 React 经常抱怨的)。

```
function handleBack() {
  const activeStep = this.state.activeStep - 1
  this.setState({ activeStep })
} 
```

# 资源:

[如何在 React | Stack Overflow](https://stackoverflow.com/questions/39316376/how-to-use-the-increment-operator-in-react)
[Stepper React 组件| Material-UI](https://material-ui.com/components/steppers/#horizontal-linear) 中使用增量运算符