# 27 行中的冗余

> 原文：<https://dev.to/selbekk/redux-in-27-lines-2i92>

Redux 已经成为 React 中状态管理的事实标准。它是处理全局状态的一个很好的工具，它的普及意味着您可能会在某个时候想要学习它。

不过，Redux 并不是最容易学习的概念。即使文档很好(并且正在被重写以变得更好)，也很难理解 Redux 的单向数据流、分派、减少、动作等概念。我自己也纠结过，第一次碰到 Redux 的时候。

幸运的是，Redux 并不像看起来那么复杂。事实上，您可以用 27 行代码实现核心部分或 Redux 的工作版本！

本文将带您了解如何自己实现一个类似于 Redux 的 API。不是因为你想这么做，而是因为它会帮助你理解 Redux 是如何工作的！

## Redux 是什么，真的吗？🤔

Redux 商店的核心部分。该存储包含单个状态树。store 允许您读取状态，调度更新状态的操作，订阅和取消订阅该状态的更新，仅此而已。

这个存储在您的应用程序中传递。如果您使用 React，您可能会将您的存储传递给`react-redux`的`<Provider />`组件，通过用`connect()`包装您的组件，它允许您在应用程序的其他部分访问它。

## 我们来实现 Redux 吧！

我们将通过实现`createStore`方法来重新实现 Redux。它做了它在 tin 上所说的事情——它给了我们一个可以玩的 store 实例。商店只是一个物件，上面有几个方法，不需要花里胡哨。

### Step 1: getState

让我们从小处着手，通过实现`getState`方法:

```
function createStore() {
  let state = {};
  return {
    getState() {
      return state;
    }
  };
} 
```

当我们调用`createStore`时，我们创建了一个空的状态对象。这就是你一直听到的单一状态树。我们返回我们的“store ”,它只是一个具有一个属性的对象——一个`getState`函数。调用这个`getState`函数可以访问`createStore`闭包内的`state`变量。

我们是这样使用它的:

```
import { createStore } from './redux';

const store = createStore();
const state = store.getState(); 
```

### 第二步:接受一个减速器

Redux 的核心概念之一就是减速器。Redux reducer 是一个函数，它接受当前状态和一个动作，并返回下一个状态(动作发生后的状态)。这里有一个简单的例子:

```
function countReducer(state = 0, action) {
  if (action.type === 'INCREMENT') return state + 1;
  if (action.type === 'DECREMENT') return state - 1;
  return state;
} 
```

这里——`countReducer`响应两个动作——`INCREMENT`和`DECREMENT`。如果传递的操作也不匹配，则返回当前状态。

为了继续我们理解 Redux 的旅程，我们需要稍作休息，了解 Redux 的数据流:

1.  用户分派一个动作
2.  动作被传递给你的减速器
3.  减速器返回新状态
4.  存储中的状态被更新
5.  任何对新州感兴趣的人都会得到通知。

为了让我们遵循这个流程，我们需要我们的商店有一个减速器！让我们把它作为第一个参数:

```
function createStore(initialReducer) {
  let reducer = initialReducer;
  let state = reducer({}, { type: '__INIT__' });
  return {
    getState() {
      return state;
    }
  };
} 
```

这里，我们接受一个归约器，调用它得到我们的初始状态。我们“触发”一个初始动作，并向我们的状态传递一个空对象。

Redux 实际上让我们在创建存储时传递预先计算好的状态。这可能一直保存在本地存储中，或者来自服务器端。总之，添加对它的支持就像给我们的`createStore`函数传递一个`initialState`参数一样简单:

```
function createStore(initialReducer, initialState = {}) {
  let reducer = initialReducer;
  let state = reducer(initialState, { type: '__INIT__' });
  return {
    getState() {
      return state;
    }
  };
} 
```

太好了！现在我们甚至支持服务器端渲染——这真是太棒了！

### 第三步:调度动作！

我们 Redux 之旅的下一步是给用户一些方法来说明我们的应用程序中发生了一些事情。Redux 通过给我们一个`dispatch`函数来解决这个问题，这个函数让我们用一个动作调用我们的 reducer。

```
function createStore(initialReducer, initialState = {}) {
  let reducer = initialReducer;
  let state = reducer(initialState, { type: '__INIT__' });
  return {
    getState() {
      return state;
    },
    dispatch(action) {
      state = reducer(state, action);
    }
  };
} 
```

从实现中我们可以看出,“分派”一个动作的概念只是用当前状态和我们传递的动作调用我们的 reducer 函数。这看起来很简单！

### 第四步:订阅变更

如果我们不知道状态何时发生，那么改变状态就没有多大价值。这就是 Redux 实现简单订阅模型的原因。您可以调用`store.subscribe`函数，并在状态改变时传递一个处理程序——就像这样:

```
const store = createStore(reducer);
store.subscribe(() => console.log('The state changed! 💥', store.getState())); 
```

让我们来实现这个:

```
function createStore(initialReducer, initialState = {}) {
  let reducer = initialReducer;
  let subscribers = [];
  let state = reducer(initialState, { type: '__INIT__' });
  return {
    getState() {
      return state;
    },
    dispatch(action) {
      state = reducer(state, action);
      subscribers.forEach(subscriber => subscriber());
    },
    subscribe(listener) {
      subscribers.push(listener);
    }
  };
} 
```

我们创建了一个订户数组，开始时为空。每当我们调用我们的`subscribe`函数时，侦听器就会被添加到列表中。最后——当我们调度一个动作时，我们调用所有订阅者来通知他们状态已经改变。

### 第五步:退订变更

Redux 还允许我们取消收听状态更新。无论何时调用`subscribe`函数，都会返回一个取消订阅函数。当你想取消订阅时，你可以调用这个函数。我们可以扩充我们的`subscribe`方法来返回这个`unsubscribe`函数:

```
function createStore(initialReducer, initialState = {}) {
  let reducer = initialReducer;
  let subscribers = [];
  let state = reducer(initialState, { type: '__INIT__' });
  return {
    getState() {
      return state;
    },
    dispatch(action) {
      state = reducer(state, action);
      subscribers.forEach(subscriber => subscriber());
    },
    subscribe(listener) {
      subscribers.push(listener);
      return () => {
        subscribers = subscribers.filter(subscriber => subscriber !== listener);
      };
    }
  };
} 
```

`unsubscribe`函数从内部订户注册表数组中删除订户。就这么简单。

### 第六步:更换减速器

如果要动态加载应用程序的一部分，可能需要更新 reducer 函数。这不是一个很常见的用例，但是因为它是商店 API 的最后一部分，所以让我们实现对它的支持:

```
function createStore(initialReducer, initialState = {}) {
  let reducer = initialReducer;
  let subscribers = [];
  let state = reducer(initialState, { type: '__INIT__' });
  return {
    getState() {
      return state;
    },
    dispatch(action) {
      state = reducer(state, action);
      subscribers.forEach(subscriber => subscriber(state));
    },
    subscribe(listener) {
      subscribers.push(listener);
      return () => {
        subscribers = subscribers.filter(subscriber => subscriber !== listener);
      };
    },
    replaceReducer(newReducer) {
      reducer = newReducer;
      this.dispatch({ type: '__REPLACE__' });
    }
  };
} 
```

这里我们简单地用新的缩减器替换旧的缩减器，并分派一个动作用新的缩减器重新创建状态，以防我们的应用程序需要做一些特殊的响应。

#### 第七步:储存促进剂呢？

我们实际上已经忽略了实现中非常重要的一部分——存储增强器。存储增强器是一个函数，它接受我们的`createStore`函数，并返回它的增强版本。Redux 只附带了一个增强器，即`applyMiddleware`，它允许我们使用“中间件”的概念——让我们在调用`dispatch`方法之前和之后做一些事情的功能。

实现对存储增强器的支持只有 3 行代码。如果有一个被传递了——调用它并返回再次调用它的结果！

```
function createStore(initialReducer, initialState = {}, enhancer) {
  if (enhancer) {
    return enhancer(createStore)(initialReducer, initialState);
  }
  let reducer = initialReducer;
  let subscribers = [];
  let state = reducer(initialState, { type: '__INIT__' });
  return {
    getState() {
      return state;
    },
    dispatch(action) {
      state = reducer(state, action);
      subscribers.forEach(subscriber => subscriber(state));
    },
    subscribe(listener) {
      subscribers.push(listener);
      return () => {
        subscribers = subscribers.filter(subscriber => subscriber !== listener);
      };
    },
    replaceReducer(newReducer) {
      reducer = newReducer;
      this.dispatch({ type: '__REPLACE__' });
    }
  };
} 
```

### 第八步？没有第八步！

就是这样！您已经成功地重新创建了 Redux 的核心部分！你或许可以把这 27 行代码放到你当前的应用程序中，然后发现它已经完全正常工作了。

现在，您可能不应该这样做，因为 Redux 的实现方式为您提供了大量的保护措施、警告和速度优化，但它提供了相同的功能！

如果你想了解更多关于 Redux 实际上是如何工作的，我建议你看一下[实际的源代码](https://github.com/reduxjs/redux/blob/master/src/createStore.js)。你会惊讶于它和我们刚刚写的是多么的相似。

## 外卖

自己重新实现 Redux 实际上没有任何意义。这充其量只是一个有趣的派对把戏。然而，看到它实际上是多么的不可思议，将有希望提高你对 Redux 如何工作的理解！它毕竟不是一个神秘的黑盒——它只是几个简单的方法和一个订阅模型。

希望这篇文章已经固化了你对 Redux 的认识，以及它是如何在幕后工作的。如果你还有问题，请在评论里告诉我，我会尽力回答的！