# 如何用 React 处理输入

> 原文：<https://dev.to/jakesweb/how-to-handle-inputs-with-react-9bp>

在开发我最新的演示应用程序时，我遇到了 React 的问题；我想创造形式。这个简单任务的问题是，我不希望任何后端或 API 来处理表单，相反，我希望它在 React 中工作。我发现了很多复杂的东西，但我认为我创造了一个有效的系统。

## 采集数据

我做的第一件事是创建“表单”为此，我在一个`<div>`中创建了一组输入。输入应该是这样的:

```
<input 
    class="rounded" 
    id="gametitle" type="text" 
    placeholder="Game Title" 
    onChange={this.props.onChange} 
    value="{this.props.gameTitle}"
/> 
```

其中最重要的部分是`onChange`和`value`属性。value 属性将读取由`onChange`生成的状态。`onChange`是用一个叫做`onChange`的道具传入 React 类的方法。这些共同创建一个输入，可用于收集数据，然后清除。

```
onChange = event => {
    switch (event.target.id) {
        case "gametitle":
            this.setState({ gameTitle: event.target.value });
            break;
        ...
    }
} 
```

该方法将收集生成的`event`。然后，它将使用输入的`id`值来匹配正确的大小写。从这里，它用`event.target.value`收集输入的当前值，并将状态设置为该值。`gameTitle`在被使用之前，在 React 类的构造函数中被定义，连同状态的其他位。

## 处理数据

在收集了所有 4 个输入之后，我需要收集数据并为下一轮数据清除输入。为此，需要一个按钮。最后的输入是按钮:

```
<input
    class="rounded"
    id="enter"
    type="submit"
    text="Add"
    onClick={this.props.onSubmit}
/> 
```

按钮有点不同。它使用`onClick`值来观察用户交互(在这种情况下，当用户点击按钮区域时)。该方法通过一个名为 onSubmit 的 prop(onChange 也是如此)传递到类中。

```
onSubmit = event => {
    if (
      this.state.gameTitle === undefined
    ) {
      alert("Error: undefined element");
    } else {
      if (this.state.games == null) {
        this.setState({
          games: [
            {
              gameTitle: this.state.gameTitle,
              date: this.state.date,
              digital: this.state.digital,
              platform: this.state.platform
            }
          ]
        });
      } else {
        this.setState({
          games: [
            ...this.state.games,
            {
              gameTitle: this.state.gameTitle,
              date: this.state.date,
              digital: this.state.digital,
              platform: this.state.platform
            }
          ]
        });
      }
    }

    this.setState({
      gameTitle: "",
      date: todayDate,
      digital: false,
      platform: ""
    });
} 
```

这里发生了一些事情，所以我将从头开始。首先要做的是确保用户已经输入了所有需要的数据。为此，我检查了四个输入的状态值，看它是否是`null`。从那里，我需要查看`games: []`数组中是否已经有一个值。如果没有值，我输入四个输入的值作为第一个对象。当一个先前的值存在时，我将 state 重置为 state 的当前值，然后将数据添加到后面。使用扩展语法`games: [...this.state.games]`修改数组，该语法引用整个数组并允许追加当前状态。这将保持状态不变。最后一部分将输入的状态设置回空白，因为输入使用状态作为它们的值。

你可以在 [GitLab](https://gitlab.com/jcolborn/games-list) 看到所有的代码。