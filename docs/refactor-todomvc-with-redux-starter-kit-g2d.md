# 使用 Redux Starter Kit 重构 TodoMVC

> 原文：<https://dev.to/magarcia/refactor-todomvc-with-redux-starter-kit-g2d>

到现在为止，我已经和 React 合作两年多了。我开始了一个相当大的项目，使用 [Redux](https://redux.js.org/) 已经做了很多工作。刚开始做了这么多事情，我有点不知所措，尤其是对于一个我不太了解的框架。但是过了一段时间后，我变得更加舒适和有经验了。

最近我发现了 Redux 项目的同一个团队的 [Redux 初学者工具包](https://redux-starter-kit.js.org/)。这是一个简单的工具集，它提供的实用程序可以使 Redux 的工作变得非常简单和容易。事实上，它提供的工具之一，`createReducer`，是我已经使用了一段时间的模式，它在减少样板代码和加速我的开发(特别是在新项目中)方面帮助了我很多。

所以为了更好地学习和使用它，我决定用 Redux 迁移一个已经存在的代码库，使用这个工具集。显然，作为一个前端框架的示例项目，我选择了无所不在的 [TodoMVC](http://todomvc.com/) ，具体来说就是 Redux 在 [his repository](https://github.com/reduxjs/redux/tree/master/examples/todomvc) 中作为示例提供的版本。

## 起点

对于不知道这个 app 在 Redux 里长什么样的人来说，它有两个主减速器`visibilityFilter`和`todos`；两者都有他各自的行动，行动创造者和选择者。

## 可见性过滤器

我从最“简单”的减压器开始，从小的开始，然后转移到更复杂的状态。

### 减速器

缩减器，因为它来自 Redux 示例，所以已经非常简单和容易理解了。

```
// reducers/visibilityFilter.js
import { SET_VISIBILITY_FILTER } from '../constants/ActionTypes';
import { SHOW_ALL } from '../constants/TodoFilters';

export default (state = SHOW_ALL, action) => {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return action.filter;
    default:
      return state;
  }
}; 
```

为了创建减速器，Redux Starter Kit 提供了一个函数`createReducer`，正如我之前提到的，这是一个我已经使用过的模式，我对它非常满意。

这个想法很简单，不需要创建一个内部带有`switch case`语句的 reducer 函数，这个函数期望初始状态作为第一个参数和一个对象，其中键是动作类型，值是这个动作的 reducer(`(state, action) => { /* reducer code */`)。

它减少了一些样板文件，并且总是将`default`语句设置为`return state`。但是对我来说，最大的好处是提供了可读性。

这是使用`createReducer` :
的可见性过滤减少器的样子

```
// reducers/visibilityFilter.js
import { createReducer } from 'redux-starter-kit';
import { SET_VISIBILITY_FILTER } from '../constants/ActionTypes';
import { SHOW_ALL } from '../constants/TodoFilters';

export default createReducer(SHOW_ALL, {
  [SET_VISIBILITY_FILTER]: (state, action) => action.filter
}); 
```

### 动作创建者

现在是采取行动的时候了。可见性过滤器只有一个动作`SET_VISIBILITY_FILTER`，创建者非常简单:

```
// actions/index.js
import * as types from '../constants/ActionTypes';

/* ... Other actions ...*/
export const setVisibilityFilter = filter => ({
  type: types.SET_VISIBILITY_FILTER,
  filter
}); 
```

对于操作，这个工具集可能非常固执己见。它提供了函数`createAction`，该函数只期望动作类型作为参数。结果，我们得到了一个动作创建者。

```
// actions/index.js
import * as types from '../constants/ActionTypes';

/* ... Other actions ...*/
export const setVisibilityFilter = createAction(types.SET_VISIBILITY_FILTER); 
```

这个动作创建器可以带参数运行，也可以不带参数运行。在我们发送参数的情况下，这将被设置为动作的有效载荷。以下是如何工作的一些例子:

```
const setVisibilityFilter = createAction('SET_VISIBILITY_FILTER');

let action = setVisibilityFilter();
// { type: 'SET_VISIBILITY_FILTER' }

action = setVisibilityFilter('SHOW_COMPLETED');
// returns { type: 'SET_VISIBILITY_FILTER', payload: 'SHOW_COMPLETED' }

setVisibilityFilter.toString();
// 'SET_VISIBILITY_FILTER' 
```

因此，现在过滤器被设置到动作的`payload`键中，这意味着对减速器的
重构，因为我们使用了键过滤器，但幸运的是
很容易改变。

```
// reducers/visibilityFilter.js
import { createReducer } from 'redux-starter-kit';
import { SET_VISIBILITY_FILTER } from '../constants/ActionTypes';
import { SHOW_ALL } from '../constants/TodoFilters';

export default createReducer(SHOW_ALL, {
  [SET_VISIBILITY_FILTER]: (state, action) => action.payload
}); 
```

### 选择器

对我来说，使用选择器是任何人在使用 React 时可以采取的最佳选择之一，因为它使得重构状态看起来非常简单，而不必改变消耗这部分状态的所有组件。

可见性过滤器的选择器是最简单的选择器之一:

```
// selectors/index.js
const getVisibilityFilter = state => state.visibilityFilter;

/* ... Other selectors ...*/ 
```

而且使用函数`createSelector`也不会改变太多。实际上，我们现在比以前的版本拥有更多的代码，但是相信我，它会更简单。继续读下去。

```
// selectors/index.js
import { createSelector } from 'redux-starter-kit';

const getVisibilityFilter = createSelector(['visibilityFilter']);

/* ... Other selectors ...*/ 
```

### 切片

到目前为止，我们所做的唯一一件事就是使用不同的*创建者*将一些简单的函数变成更简单的函数。但是现在我发现了这个工具集的真正威力:`createSlice`。

`createSlice`是一个函数，它接受一个初始状态，一个充满 reducer 函数的对象，以及一个可选的“片名”，并自动生成准备使用的动作创建器、动作类型和选择器。

现在我们可以抛出所有我们做过的代码。

为可见性过滤器创建一个切片是非常干净和容易理解的，并且由于我们可以丢弃所有之前的代码，我们重构最终的结果，这就去除了许多样板文件。

```
// ducks/visibilityFilter.js
import { createSlice } from 'redux-starter-kit';

export default createSlice({
  slice: 'visibilityFilter',
  initialState: SHOW_ALL,
  reducers: {
    setVisibilityFilter: (state, action) => action.payload
  }
}); 
```

现在我们有了一个单独的对象，它包含了使用 Redux 正常工作所需的所有内容。这是它的用法:

```
const reducer = combineReducers({
  visibilityFilter: visibilityFilter.reducer
});

const store = createStore(reducer);

store.dispatch(visibilityFilter.actions.setVisibilityFilter(SHOW_COMPLETED));
// -> { visibilityFilter: 'SHOW_COMPLETED' }

const state = store.getState();
console.log(visibilityFilter.selectors.getVisibilityFilter(state));
// -> SHOW_COMPLETED 
```

到目前为止所做的所有更改都在[提交](https://github.com/magarcia/todomvc-redux-starter-kit/commit/ae78e0aacd4827786a63f29db4d6f4e0a2079422)中。

## Todos

todos reducer 更复杂，所以我不打算一步一步地展示重构。相反，我将解释最终结果是怎样的，但是如果你感兴趣的话[直接进入最终结果](https://github.com/magarcia/todomvc-redux-starter-kit/blob/ba531a2ea7c2c5ee8148e2a1ab491e7e0a31e819/src/ducks/todos.js)。

第一部分是定义初始状态:

```
// ducks/todos.js
const initialState = [
  {
    text: 'Use Redux',
    completed: false,
    id: 0
  }
]; 
```

为了使切片创建更具可读性，我提取了不同函数中不同的 reducer 动作:

```
// ducks/todos.js
const addTodo = (state, action) => [
  ...state,
  {
    id: state.reduce((maxId, todo) => Math.max(todo.id, maxId), -1) + 1,
    completed: false,
    text: action.payload.text
  }
];

const deleteTodo = (state, action) => state.filter(todo => todo.id !== action.payload.id);

const editTodo = (state, action) =>
  state.map(todo =>
    todo.id === action.payload.id ? { ...todo, text: action.payload.text } : todo
  );

const completeTodo = (state, action) =>
  state.map(todo =>
    todo.id === action.payload.id ? { ...todo, completed: !todo.completed } : todo
  );
const completeAllTodos = state => {
  const areAllMarked = state.every(todo => todo.completed);
  return state.map(todo => ({
    ...todo,
    completed: !areAllMarked
  }));
};

const clearCompleted = state => state.filter(todo => todo.completed === false); 
```

现在我们可以把所有的放在一个新的切片中:

```
// ducks/todos.js
const todos = createSlice({
  slice: 'todos',
  initialState,
  reducers: {
    add: addTodo,
    delete: deleteTodo,
    edit: editTodo,
    complete: completeTodo,
    completeAll: completeAllTodos,
    clearCompleted: clearCompleted
  }
}); 
```

默认情况下，`createSlice`返回的选择器非常简单，它们只是从状态返回值(例如:`todos.selectors.getTodos`)。但是在这个应用程序中，我们需要定义更复杂的选择器。

例如，`getVisibleTodos`需要知道当前可见性过滤器和待办事项。`createSelector`作为第一个参数获取一个带有字符串(从状态中选择的路径)或其他选择器的数组，作为第二个参数获取一个函数，该函数将实现我们希望基于所选过滤器选择 todos 的逻辑。

```
// ducks/todos.js
const { getVisibilityFilter } = visibilityFilter.selectors;

todos.selectors.getVisibleTodos = createSelector(
  [getVisibilityFilter, todos.selectors.getTodos],
  (visibilityFilter, todos) => {
    switch (visibilityFilter) {
      case SHOW_ALL:
        return todos;
      case SHOW_COMPLETED:
        return todos.filter(t => t.completed);
      case SHOW_ACTIVE:
        return todos.filter(t => !t.completed);
      default:
        throw new Error('Unknown filter: ' + visibilityFilter);
    }
  }
);

todos.selectors.getCompletedTodoCount = createSelector(
  [todos.selectors.getTodos],
  todos => todos.reduce((count, todo) => (todo.completed ? count + 1 : count), 0)
); 
```

正如你在前面的代码中注意到的，我在`todos`片的`selectors`对象中创建了新的选择器，所以现在我们可以在同一个地方访问所有的选择器。

## 创建商店

库提供的最后两个函数是`configureStore`和
T1。

`configureStore`是对标准 Redux `createStore`
函数的抽象。没有提供比`createStore`更多的功能，但它使事情更容易阅读，像启用开发者工具，这只是一个布尔值。

`getDefaultMiddleware`返回开发中的
`[immutableStateInvariant, thunk, serializableStateInvariant]`和生产中的
`[thunk]`的中间件列表。

*   `redux-immutable-state-invariant`:它可以检测调度过程中减少器的突变，也可以检测调度之间发生的突变(例如:选择器或组件)。
*   它会深入检查你的状态树和你的动作中不可串行化的值，比如函数、承诺等等。

```
// store.js
import { configureStore, getDefaultMiddleware } from 'redux-starter-kit';
import { combineReducers } from 'redux';
import { visibilityFilter, todos } from './ducks';

const preloadedState = {
  todos: [
    {
      text: 'Use Redux',
      completed: false,
      id: 0
    }
  ]
};

const reducer = combineReducers({
  todos: todos.reducer,
  visibilityFilter: visibilityFilter.reducer
});

const middleware = [...getDefaultMiddleware()];

export const store = configureStore({
  reducer,
  middleware,
  devTools: process.env.NODE_ENV !== 'production',
  preloadedState
}); 
```

## 最后的想法

Redux Starter Kit 看起来很有趣，它减少了样板代码，使代码更干净，更容易理解。但是它也使得开发新代码变得非常快。

源代码:[https://github.com/magarcia/todomvc-redux-starter-kit](https://github.com/magarcia/todomvc-redux-starter-kit)

* * *

*最初发表于 2019 年 1 月 26 日 [magarcia.io](https://magarcia.io/2019/01/26/todomvc-redux-starter-kit/) ..*