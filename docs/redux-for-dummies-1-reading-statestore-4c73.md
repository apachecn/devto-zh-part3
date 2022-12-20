# 好友还原 1:读取状态/商店

> 原文：<https://dev.to/tttaaannnggg/redux-for-dummies-1-reading-statestore-4c73>

好的，我完成了一个利用 React 和 Redux 的项目。我是 React 的初学者，这是我第一次使用 Redux，所以如果你和我情况相似，这篇文章可能会有帮助。我将假设您了解 React 的基础知识，并且能够处理基本任务，例如呈现组件、创建和管理有状态组件，以及在组件之间访问和传递属性。

首先，让我们从广义的、抽象的术语来谈论 Redux。Redux 是一个状态管理库。我们的 React 应用程序可能在几个不同的地方维护“状态”,或者必须将状态传递给需要它的组件，而 Redux 将我们所有的状态保存在一个集中的存储中，React 组件可以访问这个存储。

这在理论上相当简单。你可以想象它的工作方式类似于一个全局对象，一个维护所有我们需要的信息来决定我们应用程序的状态的对象。组件可以“看到”该状态，并根据存储在其中的信息确定它们需要做什么。有些人也可以修改状态。想象一下，提交一个表单或单击一个按钮，页面会随之改变。

然而，为了实现这一点，需要进行大量的配置，而且大部分都是以相当具体的方式完成的。

下面是一般循环的样子:

**存储(状态)** - *定义*->-**前端** - *触发器*->-**事件处理程序** - *发送数据/信号(动作)到*->-**减速器** - *更新*->-**存储(状态)**

### 商店

**Store** 基本上就是一个包含我们状态的对象。因此，我们需要对它进行两个基本操作:

1.  阅读它
2.  写给它

我们没有足够的时间或空间来涵盖这两者，所以我们将讨论如何从我们的**存储**中读取。

然而，作为做这些事情的先决条件，我们必须有一些方法让我们的 React 应用程序可以与商店通信。React Redux 以**提供者**和**容器**的形式为我们提供了这些。

provider 是我们的 react-redux 库中内置的一个元素。我们从 react-redux 导入它，并将我们的整个应用程序包装在其中。它将进入渲染函数，将我们的整个应用程序挂在 DOM 上，就像这样:

```
render(
<Provider store={store}> 
    <App /> 
</ Provider>,  document.getElementById('App')) 
```

因此，Provider 使我们的应用程序能够访问作为“store”属性传入的商店。这个“商店”是通过使用 createStore 创建的数据结构，并给它我们的 reducers 和(可选)一个名为“composeWithDevTools”的函数的输出(该函数允许 Chrome 的 [Redux devtools](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en) 与商店接口)。

正如我之前说过的，学习 Redux 的很多麻烦就是完成所有的配置，并确保所有的部分都能够正确地相互通信。事实上，为了从我们的商店中进行读写，我们还需要一件东西:我们的**容器**。

容器本身并不特别，但是在我们考虑/使用 react-redux 的方式上，它们类似于我们的提供者。基本上，它们是我们选择通过 react-redux **connect** 功能创建与商店连接的组件。

为了从存储中读取值，我们需要在容器中做两件事情。

1.  创建 **mapStateToProps**
2.  调用我们的**连接**

### mapStateToProps

这是一个函数，其目的是作为 react-redux 的 **connect** 函数的回调。从概念上讲，它定义了**连接**的方式，这种方式将使来自我们商店的状态作为[的道具出现在我们的组件](https://reactjs.org/docs/components-and-props.html)上。它返回一个对象，该对象的键是将在容器的 this.props 对象上可用的数据段的名称。

例如，假设我们的商店看起来像这样:`{'userCount':5}`。

mapStateToProps 可能看起来像这样:

```
const mapStateToProps = store => ({
    users: store.users.userCount
}) 
```

通过神奇的**连接**(同样，一个内置的 react-redux 函数)，我们将能够做到这一点:

```
console.log(this.state.users) 
```

并把它记录到我们的控制台上。

一个简单的容器可能看起来像这样:

```
import React, { Component } from 'react';
import { connect } from 'react-redux';
const mapStateToProps = store => ({
    users: store.users.userCount // store looks like: {'userCount':5}
})

const mapDispatchToProps = dispatch =>({
    //we'll fill this in and explain it later!
})

class DisplayUsers extends Component{
    constructor(props){
        super(props);
    }
}
render(){
    <p>{this.props.users}</p> }
export default connect(mapStateToProps, mapDispatchToProps)(DisplayUsers) 
```

因此，如果我们显示这个组件，应该呈现出来的是一个 p 标记，带有一个从我们的存储中读取的值！(具体来说，store.users.userCount)

此时，您可能已经注意到我们正在定义 **mapStateToProps** ，但是从来没有调用过它，并且可能不清楚对象的返回值可能会去哪里或者它会做什么。答案是底层的小函数调用，**连接**。

### 连接

**连接**是让我们整个容器工作的原因。这是我们从 react-redux 库中导入的一个内置方法，它接受两个回调并返回一个函数，然后我们立即用整个组件作为参数调用这个函数。

从它返回的是我们组件的一个版本，该版本具有来自我们的 **mapStateToProps** 的值，这些值可以在它的`this.props`对象上获得。这就是从这个文件中导出的内容，这就是我们在导入和呈现组件时看到的内容。

这可能看起来令人困惑，但是这就是为什么我们能够在我们的组件中有`<p>{this.props.users}</p>`,并且实际上让它呈现来自我们的**商店**的值，尽管从未定义过`this.props.users`,也从未将`users`作为道具传递给我们的组件。

本质上，我们编写 **DisplayUsers** 组件，就好像它已经可以访问我们在 **mapStateToProps** 中映射的值，因为我们知道，在调用 **connect** 之后，我们将处理一个版本的 **DisplayUsers** 组件，其中*可以访问 **mapStateToProps** 处理的映射值。*

注意，一旦我们状态的所需位被映射到我们容器组件的 props 上，我们也可以像传递任何属性一样将它们传递给其他组件。

```
import React, { Component } from 'react';
import { connect } from 'react-redux';
const mapStateToProps = store => ({
    users: store.users.userCount // store looks like: {'userCount':5}
})

const mapDispatchToProps = dispatch =>({
    //we'll fill this in and explain it later!
})

class DisplayUsers extends Component{
    constructor(props){
        super(props);
    }
}
render(){
    <OtherComponent users={this.props.users} /> // imagine we had defined and imported some React component called OtherComponent
}
export default connect(mapStateToProps, mapDispatchToProps)(DisplayUsers) 
```

总而言之:

1.  创建一个函数( *mapDispatchToProps* )，该函数指定我们希望从我们的商店(*‘用户计数’*)中访问的数据，以及它们在 Props 对象上的标签(*‘用户’*，可在 *this.props.users* 中访问)
2.  创建一个组件( *DisplayUsers* )，就像创建任何其他 React 组件一样，只是您可以自由引用来自 **mapDispatchToProps** 的标签，就像它们是您的 Props 对象上的关键点一样
3.  使用映射函数和组件名作为参数调用 **connect** ,因为它将通过访问存储来连接映射值和组件的属性。( *connect(mapStateToProps，mapDispatchToProps)(display users)*)

我发誓我会在下一篇文章中解释 **mapDispatchToProps** 。作为一个引子，你可以知道它类似于 **mapStateToProps** ，但是将为我们提供必要的功能来改变我们的状态/商店，只需要几个中间步骤。