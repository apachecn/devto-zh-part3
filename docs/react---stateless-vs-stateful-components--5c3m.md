# React -无状态与有状态组件

> 原文：<https://dev.to/mcarpenter/react---stateless-vs-stateful-components--5c3m>

先决条件——本教程面向开始学习 React 并需要更好地了解组件的初学者。

## 组件简介

组件可以让你将用户界面分割成独立的、可重用的部分，并独立地考虑每一部分。在构建 React 应用程序时，您将有许多不同的组件组成 React 应用程序。有些可能会有状态变化，甚至可能不得不访问您的后端。其他组件可能只是渲染你的用户界面的一部分，通过访问道具来成功渲染。下面我们将通过一个有状态和无状态的例子。

Stateful -建立状态，有方法

```
import React from "react";
import Show from "./show";

class Input extends React.Component {
  constructor(props) { //<----Method
    super(props);
    this.state = { //<----Initialize state
      value: ""
    };
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(event) { //<---- Method/Set State
    this.setState({ value: event.target.value });
  }

  render() { . //<-----Method/Return JSX/HTML
    return (
      <div>
        <input
          name="firstName"
          type="text"
          value={this.state.value}
          onChange={this.handleChange}
        />
        <Show value={this.state.value} />
      </div>
    );
  }
}
export default Input; 
```

Enter fullscreen mode Exit fullscreen mode

以上是一个有状态组件的例子，为什么？三件事，首先我们初始化状态，其次我们在构造函数上有方法，最后我们在我们的`handleChange`方法中设置状态。这是有状态组件的三个关键部分。接下来让我们看一个无状态组件。

无状态——用我们输入的值返回 UI 的一部分

```
import React from "react";

function Show(props) {
  return( //<----Return JSX/HTML
    <p>{props.value}</p>
    )
}

export default Show; 
```

Enter fullscreen mode Exit fullscreen mode

上面是一个无状态组件的例子，它只是简单地返回一个 p 标签，当我们输入时，这个 p 标签将具有我们输入的值。它没有设置任何状态，也没有任何方法。这是保持 react 代码可维护性和可读性的好方法。将它分解成更小的组件，并将道具传递给子组件。我们导出这个组件，并将其包含在父组件中。它唯一的工作是显示输入的值。

如果这是一个充满输入的完整表单，我们的代码将会有数百行代码。如果我们需要显示值，甚至更长，使用这种方法允许我们打破这种可读性和可维护性。

感谢阅读。