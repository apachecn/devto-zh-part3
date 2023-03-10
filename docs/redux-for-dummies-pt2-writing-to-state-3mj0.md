# 好友 Pt2 的还原:写入状态

> 原文：<https://dev.to/tttaaannnggg/redux-for-dummies-pt2-writing-to-state-3mj0>

在我们上次停止的地方，我们对 Redux 循环有了一个大致的了解。我们知道**容器**提供了对我们`store`的访问，我们通过`mapStateToProps`有选择地使用它来水合我们的组件`props`。

作为快速复习，下面是一般循环的样子:

> **存储**(状态)- *定义*->-**前端** - *触发*->-**事件处理**(或其他功能)- *发送数据/信号(动作)到*->-**减速器** - *更新*->-**存储(状态)**

为了方便起见，我们将使用我们编写的代码，用一个将显示我们的`userCount`的`<p>`标签替换我们正在呈现的`otherComponent`。经过修改，现在我们的代码如下:

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

现在，为了让状态更新生效，我们需要从前端发送数据。我们就从这个开始。还记得`mapDispatchToProps`吗？

嗯，`dispatch`允许我们的前端向`store`发送数据。让我们想象一下，我们有一个按钮，我们想点击它来为我们的`usercount`加 1。

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
  <div>
    <p>{this.props.users}</p>
    <button onClick={/* some function */}>add user</button>
  </div> } 
```

所以，为了改变我们的状态，我们需要给我们的`store`添加一个`action`。让我们从谈论什么是`action`开始，从它们是如何被创建的开始。

## 动作创建者/动作

动作创建者是一个函数。它接受一个参数，并返回一个我们称之为`action`的对象。一个`action`只是一个上面有两个属性的对象:`type`和`payload`。

动作创建者可能是这样的:

```
import * as types from '../constants/actionTypes'

export const addUser = (payload) =>({
  type: types.ADD_USER,
  payload: payload  
}) 
```

正如您所看到的，`addUser`返回一个看起来像这样的对象:`{type: types.ADD_USER, payload: /* whatever payload we give it */}`，这是它正在创建的`action`。

那么，什么是`type`？我们希望在我们的`payload`中有什么数据？

### 动作类型

一个`type`实际上只是一个从我们的`actionTypes`文件导入的字符串。按照惯例，我们行为的名字都是大写的。

基本上，我们希望我们的`reducers`到
处理不同类型的状态更新，我们的`type`决定使用哪一种。我们将进一步深入我们的`reducers`，但是，现在，我们将看看我们的`actionTypes.js`文件里面可能有什么:

```
export const ADD_USER = 'ADD_USER'; 
```

它实际上只是一个应用了一些约定的字符串。

所以，至于为什么我们要把这个字符串存储在一个变量中，而不是直接输入它，这首先与 Redux 存在的原因有关:scale。

在规模上，使用动作类型有一些好处。来自丹·阿布拉莫夫自己的话:

> *   This helps to keep naming consistent, because all the action types are concentrated in one place.
>     
>     
> *   Sometimes, you want to check all existing operations before dealing with new functions. Maybe someone in the team has added the action you need, but you don't know.
>     
>     
> *   The list of operation types added, deleted and changed in the pull request helps everyone in the team to track the scope and implementation of new functions.
>     
>     
> *   If you make a mistake when importing an action constant, you will get undefined. When you want to know why nothing happens when scheduling an operation, it's easier to notice than an input error.

对于这种规模的项目来说，这完全是大材小用，但 Redux 也是如此。也就是说，我们将进入我们的`payload`

### 有效载荷

`payload`是我们`action`上的另一处房产。这是我们将发送给`reducers`的实际数据。(如果我们的`action`需要提供数据——将我们的用户数增加 1 不需要除动作类型之外的任何信息)。

我们将需要谈论`reducers`来真正理解`actions`，但是我们将首先谈论我们如何发送`actions`到我们的`reducers`。

## 回到 mapDispatchToProps

因此，我们得到了一个`actionCreator`，它返回一个`action`，后者有一个`type`和`payload`。它叫做`addUser`。让我们导入并发送给我们的`store`！

```
import React, { Component } from 'react';
import { connect } from 'react-redux';

import * as actions from '../actions/actions';

const mapStateToProps = store => ({
    users: store.users.userCount 
})

const mapDispatchToProps = dispatch =>({
    addUser: (e)=>dispatch(actions.addUser(e))
})

class DisplayUsers extends Component{
    constructor(props){
        super(props);
    }
}
render(){
  <div>
    <p>{this.props.users}</p>
    <button onClick={this.props.addUser}>add user</button>
  </div> }

export default connect(mapStateToProps, mapDispatchToProps)(DisplayUsers) 
```

我们做了四个改变。

*   正如我们上面定义的，我们正在导入`actions`。
*   我们向由`mapDispatchToProps`返回的对象添加一个属性，这是一个接受名为`dispatch`的东西的函数。
*   `mapDispatchToProps`返回一个对象，该对象的属性和方法将在我们的`this.props`中通过下一件事变得可用:
*   我们使用`connect`使`mapStateToProps`上的属性和`mapDispatchToProps`上的方法在我们的`this.props`上可用，方法是对所有这些东西运行一个函数并导出结果。

`dispatch`和`store`是由 Redux 提供的，依靠的是我们从它那里导入的`connect`方法。`dispatch`最终接受了一个`action`，但是在本例中，我们使用一个动作创建器来生成该动作，提供了与使用常量动作类型类似的好处。

现在我们已经完成了所有这些，我们终于可以开始`reducers`了。

## 减速器

在 redux 的实现中，我们将有一个`index.js`文件作为我们的 reducer 的入口点——也就是说，我们将所有的 reducer 导入到`index`中，并让 Redux 决定使用哪一个。我们还将有一个`usersReducer.js`，在那里我们将编写我们的功能。

`index.js`会是这样的:

```
import { combineReducers } from 'redux';
import usersReducer from './usersReducer';
const reducers = combineReducers({
  users: usersReducer
})

export default reducers 
```

首先，注意我们用来存储`usersReducer`的`users`的键名。如果你看看我们的`mapStateToProps`，我们正在抢占`store.users.userCount`。这就是我们为什么/如何塑造我们的`store`，它只是一个包含对象的对象，这些对象包含与它们各自的缩减器相关联的状态。

那么，我们的`usersReducer`长什么样呢？

`usersReducer`应该是一个接受两个参数的函数，其中一个是我们的`store`包含的`state`，另一个是一个`action`(比如我们已经用我们的动作创建器创建并通过我们的`dispatch`发送到商店的那些)。我们的`usersReducer`的主体本质上应该是一个返回一个对象的大 switch 语句，它将包含我们的`store`的新的`state`，从而完成这个过程。

让我们写下我们的`usersReducer`。我们将创建一些初始状态，如果什么都没发生，我们的函数将返回它。

```
import * as types '../constants/actionTypes';
const initialState = {
  userCount: 0
}

const usersReducer = (state = initialState, action) =>{
  switch (action.type){
    default:
      return state;
  }
} 
```

一个`action`的`type`将决定`switch`的哪个部分被发射出去。让我们添加一个案例，当我们接收到`ADD_USER`作为我们的`type`时。

```
import * as types '../constants/actionTypes';
const initialState = {
  userCount: 0
}

const usersReducer = (state = initialState, action) =>{
  switch (action.type){
    case types.ADD_USER:{
      const userCount = state.userCount + 1;
      return {
        ...state,
        userCount
      }
    }
    default:
      return state;
  }
} 
```

让我们来分析一下我们在这里做了什么。

首先，我们为`userCount`创建一个`const`。我们这样做是为了保持我们的代码有条理，当我们试图创建一个新的`state`时，这是一个更重要的因素，这个新的【】具有与旧的不同的多个属性。接下来，我们简单地返回一个新对象，它扩展了我们以前的状态，然后用一个新值覆盖旧值`userCount`。

我们这样做部分是为了将旧的`state`合并到新的`state`中，部分是为了生成一个全新的`state`对象。在生成新的`state`对象时，我们确保 React 认识到我们已经更改了我们的`state`，并适当地重新呈现其组件。(如果您需要更多关于我们为什么这样做的信息，我将写一点关于 JS 中的原始和复合数据类型的内容，稍后在这里链接)

无论如何，现在我们已经处理了我们的`reducer`，这是整个 Redux 循环的结束。我们已经写信给我们的`store`！

## 让我们走一遍

1.  用户点击“添加用户”
2.  “添加用户”触发了一个版本的`addUser`，它已经被`mapDispatchToProps`映射到了`dispatch`
3.  `addUser`接收我们的`ADD_USER`动作创建者返回的`action`并触发我们的`reducers`
4.  我们的`reducers`为`ADD_USER`找到一个匹配的`case`，然后生成一个新的`state`对象。
5.  新的`state`对象被`mapStateToProps`传递回我们的前端组件

这就是这个特定 Redux 实现的流程。有很多其他的方法可以做到这一点，它的价值只有在规模上才能真正看到。感谢您的关注！