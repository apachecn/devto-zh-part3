# 如何在 React 中使用状态

> 原文：<https://dev.to/iam_timsmith/thisstate---how-to-use-state-in-react-omj>

如果你和我一样，反应状态一开始是一个有点混乱的概念。道具很容易掌握，但状态却很难让我理解。是怎么设定的？如果道具可以处理数据，为什么还要用它呢？在本 react.js 教程中，我们将回答这些问题以及更多问题。

> 属性是从父组件传递到子组件的数据，而状态是在组件内控制的数据

### React.js 状态 vs 道具

道具和状态在 react 中都有它们的位置。有这样的例子，其中每一个都是合适的，我无法想象在 react 中没有这两者而构建东西。在我们深入讨论之前，我先解释一下要点:道具是从父组件传递到子组件的数据，而状态是在组件内控制的数据。下面的例子演示了我们如何将一个道具传递给一个组件:

```
<App prop="Some data for a prop" /> 
```

Enter fullscreen mode Exit fullscreen mode

很多时候，状态会被用来通过 props 将数据传递给子组件。甚至有办法从子组件操纵父组件的状态。在这篇文章结束时，你将知道如何做到这两点。

## 状态管理在 React

在我们进入关于在组件中创建状态的细节之前，重要的是要注意状态只能在类组件中创建。这样做的原因是，我们的状态将存放在一个类构造函数中。

你说什么是构造函数？构造器是创建对象的面向对象编程中涉及的概念。这是我们想要为组件设置状态的地方。还应该注意的是，构造函数并不是类组件接收 props 所必需的，所以如果我们在构造函数中没有“做”任何事情，那么我们就不需要有构造函数。

如果你不确定如何创建一个组件，你可以[在这里](https://www.iamtimsmith.com/blog/how-to-create-a-component/)了解。现在，关于代码...

下面的代码显示了如何设置一个空的构造器。这不应该是我们放入生产代码中的东西，因为我们只希望使用构造函数，如果它们确实在做一些事情的话。类组件不需要构造函数来接收属性，所以除非你有状态或者必须绑定函数，否则你可能不需要它。

```
import React, { Component } from 'react'

class Example extends Component {
  constructor(props) {
    super(props);
  }

  render() {
    return (
      ...
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

添加我们的状态对象非常简单。在构造函数内部，在`super(props);`之后，只需添加`this.state`，并将其设置为等于一个空对象。一旦我们创建了空对象，我们就可以用我们想要的任何键和值对的数据填充它。下面的例子有 3 个不同的数据:一个布尔值，一个字符串，和一个数字。

```
import React, { Component } from 'react'

class Pizza extends Component {
  constructor(props) {
    super(props);
    this.state = {
      isHungry: true,
      topping: "Pepperoni",
      slices: 8
    }
  }

  render() {
    return (
      ...
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的 Pizza 组件中，您可以看到我们有一个状态，其中包含一个布尔值 true 表示`isHungry`，一个字符串值“Pepperoni”表示`topping`，以及整数 8 表示`slices`的编号。我们实际上可以在状态中使用任何数据类型，比如 bool、integer、string、array 或 object。

我知道你在想什么。超级酷，蒂姆。现在怎么办？

[![Now what?](img/83611f2e8fc3dbb8daf2fbe82512d0fd.png)](https://i.giphy.com/media/20KLYSIhq35V4EpVlC/giphy-downsized.gif)

很高兴你问了。这就把我们带到了下一部分:

## 从 render 方法访问 React 状态

在我们的 render 方法中使用 state 非常简单。喜欢*超级*容易。不用我告诉你，你能猜出怎么做吗？我给你几分钟时间。

你拿到了吗？以防万一，我还是要看一遍。假设我们只想输出比萨饼的浇头。我们可以在一个段落标签中这样做:

```
<p>{this.state.topping}</p> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码会在浏览器中输出如下:

```
<p>Pepperoni</p> 
```

Enter fullscreen mode Exit fullscreen mode

[![Awesome!](img/67e774bb4612bd76306883de0fd3dce4.png)](https://i.giphy.com/media/d2Z9QYzA2aidiWn6/giphy.gif)

## 我们如何改变状态？

好，我们有了状态，我们可以输出它。和道具基本一样但是工作量更大对吧？不对。下一部分是真正使状态不同于道具的部分。区别在于改变组件内部状态的能力。下面是一些解释如何做到这一点的代码:

```
this.setState({ item: 'newValue' }) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码调用一个`this.setState`函数，并传入一个带有键值对的对象。如果这个键与我们在 state 中已经有的键相匹配，它将 state 中的值更新为提供的新值。如果这个键在 state 中不存在，它将用给定的值创建。

不如我们给披萨组件添加一个函数，从总切片数中减去一片。下面是实现这一点的代码，可以通过点击按钮或其他动作来触发。

```
import React, { Component } from 'react'

class Pizza extends Component {
  constructor(props) {
    super(props);
    this.state = {
      isHungry: true,
      topping: "Pepperoni",
      slices: 8
    };
    this.eatSlice = this.eatSlice.bind(this);
  }

  eatSlice() {
    const totalSlices = this.state.slices - 1;
    this.setState({
      slices: totalSlices
    })
  }

  render() {
    return (
      ...
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们假设当一个按钮被点击时这个函数将被触发，那么每次用户点击那个按钮，我们的状态切片将减少一(甚至变成负数，因为我们没有创建逻辑来防止这种情况)。每次单击按钮后状态发生变化时，我们的组件将使用新数据重新呈现。

这使得用户可以实时修改页面上的数据，这太棒了。我们还可以将我们的状态作为道具传递给一个子组件。这就把我们带到了下一个主题，那就是从子组件中改变父状态。

[![Changing Parent State from a Child Component](img/4e1bf1ca2efa6637d1a1c22e433813fa.png)](https://i.giphy.com/media/3owzW5c1tPq63MPmWk/giphy.gif)

## 从子组件改变父状态

为了便于演示，让我们创建一个名为`Button`的组件。我们的新组件将允许我们提供一些道具，并让它吐出一个按钮，每次调用时呈现不同的效果。

我们新的`Button`组件没有自己的任何状态，也不会使用任何生命周期方法，因此它将作为一个无状态的功能组件工作。我们想要传入的两个道具是`action`和`label`。

下面是我们新创建的`Button`组件的代码:

```
const Button = ({ action, label }) => (
  <button onClick={() => action()}>{label}</button>
) 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？我们将使用我们的`action`属性传入一个函数，使用我们的`label`属性传入一个字符串，该字符串将设置按钮上的文本。因为我们传入了一个函数 prop，所以我们可以使用我们已经写好的函数来吃一片披萨。我将向你展示这将如何在渲染函数中工作:

```
...
render() {
  return (
    <div>
      <Button action={this.eatSlice} label="Eat a slice" />
    </div>
  )
}
... 
```

Enter fullscreen mode Exit fullscreen mode

披萨吃完了有什么好玩的？不如我们再加一个按钮，再买一片，这样我们就永远不用用完了？因为我们创建了一个已经可以接受函数和标签的按钮组件，所以我们可以简单地重用该组件并为这些属性传入新值。

在我们放入组件之前，我们需要编写`buySlice`函数。下面的代码应该可以做到。它的工作方式与`eatSlice`函数完全相同，只是它会加 1 而不是减 1，然后将`this.state.slices`设置为新值。

下面是函数`buySlice` :
的代码

```
...
buySlice() {
  const totalSlices = this.state.slices + 1;
  this.setState({
    slices: totalSlices
  });
}
... 
```

Enter fullscreen mode Exit fullscreen mode

我们需要记住在构造函数中将`this`绑定到我们的函数。现在我们的披萨组件应该看起来像这样:

```
class Pizza extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      isHungry: true,
      topping: 'Pepperoni',
      slices: 8,
    }
    this.eatSlice = this.eatSlice.bind(this)
    this.buySlice = this.buySlice.bind(this)
  }

  eatSlice() {
    const totalSlices = this.state.slices - 1
    this.setState({
      slices: totalSlices,
    })
  }

  buySlice() {
    const totalSlices = this.state.slices + 1
    this.setState({
      slices: totalSlices,
    })
  }

  render() {
    return (
      <div>
        <Button action={this.eatSlice} label="Eat a slice" />
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们有控制父组件状态的函数，并且我们已经将`this`绑定到该函数，所以我们准备将它传递给子组件，并让子组件调用`buySlice`函数。

让我们创建一个新的按钮来购买一片比萨饼。修改 Pizza 组件中的渲染方法，如下所示:

```
...
render() {
  return (
    <div>
      <p>Slices Left: {this.state.slices}</p>
      <Button action={this.eatSlice} label="Eat a slice" />
      <Button action={this.buySlice} label="Buy a slice" />
    </div>
  )
}
... 
```

Enter fullscreen mode Exit fullscreen mode

为了更容易地看到发生了什么，我添加了一些文本，显示当前可用的切片数量。现在，我们可以单击“吃一片”按钮，将切片数量减少一片，也可以单击“购买一片”按钮，将切片数量增加一片。

## 结论

与国家打交道是一项基本技能。它使我们作为开发人员的生活变得更加容易和易于管理，而不会使事情过于复杂。这也使得快速获得极其动态和流畅的用户体验成为可能。

在这篇文章中，我们讨论了什么是状态，如何创建状态，如何改变状态，以及如何从子组件中操纵状态。在大多数情况下，这些信息足以让我们控制组件中的数据。现在前进，建立酷的东西！

[https://codepen.io/iamtimsmith/embed/xMqdjV/?height=600&default-tab=result&embed-version=2](https://codepen.io/iamtimsmith/embed/xMqdjV/?height=600&default-tab=result&embed-version=2)