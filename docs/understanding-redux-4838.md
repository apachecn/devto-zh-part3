# 了解 Redux

> 原文：<https://dev.to/daleljefferson/understanding-redux-4838>

在少数大型应用程序上使用 Redux 之后，这是我对应该如何使用它的理解。

## 简单的例子

```
const todos = (state = [], {type, payload}) => {
  switch (type) {
    case "ADD_TODO":
      return [...state, payload];
    default:
      return state;
  }
};

const store = createStore(todos);
store.dispatch({type: "ADD_TODO", payload: "Learn Redux"}); 
```

> 您的应用程序的整个状态存储在一个存储区内的对象树中。

那么这个应用程序的“整体状态”是什么呢？我认为我们首先需要明白的是**状态**和**数据**的区别。将所有东西放入 redux 状态，并从 Redux 状态派生出 visible 状态是非常方便的。

```
const mapStateToProps = todos => ({
  todos: todos.filter(activeTodos).take(5) // Take the top 5 todos
}); 
```

因此，我们将用户创建的每一个待办事项(可能有数千个)保存在本地设备的内存中，这听起来不会有好结果。

我认为用户的待办事项是数据，而不是应用程序状态。那么什么是应用程序状态呢？让我们看看屏幕上显示的内容，并尝试确定描述它所需的应用程序状态。

1.  选择了哪个过滤器(全部、活动、完成)
2.  文本输入
3.  剩余项目总数
4.  可见 todos 的标题

如果我要检查这个应用程序的 Redux 状态，我应该会看到。

```
const state = {
  textInput: "Drink Coffee",
  filter: "Active",
  total: 2,
  visableTodos: ["Learn Redux", "Write Article"]
}; 
```

> 在编写任何代码之前，最好先考虑它的形状。作为一个对象，你的应用程序状态的最小表示是什么？

因此，为了构建这个用户界面，我们不需要用户已经创建的所有待办事项，我们只需要他们当前正在查看的待办事项。那么用户数据存放在哪里呢？我们很久以前就解决了这个问题，数据存在于数据库或远程服务器中。

Redux 是当前最小的活动应用程序状态，数据库/服务器是所有用户数据的真实来源。有了这些知识，让我们用异步操作创建器重写 todo 示例。

```
const addTodo = todo => async (dispatch, getState) => {
  dispatch(resetTextInput());

  await api.post("/todos", todo);

  const {filter} = getState();

  const result = await api.get(`/todos?filter=${filter}`);

  dispatch(updateVisableTodos(result.todos));
  dispatch(updateTotal(result.total));
}; 
```

当用户创建一个新的 todo 时，我们将它发送到服务器进行存储，然后我们查询 API 来获得更新的 todo 列表。如果这是一个离线应用程序，我们将把它保存在本地存储中。当用户改变过滤器时会发生什么？

```
const changeFilter = newFilter => async dispatch => {
  dispatch(changeFilter(newFilter));

  const {filter} = getState();

  const result = await api.get(`/todos?filter=${filter}`);

  dispatch(updateVisableTodos(result.todos));
  dispatch(updateTotal(result.total));
}; 
```

我发现大多数应用程序逻辑存在于异步动作创建器中，因为 reducers 必须是纯同步的。我将大部分逻辑提取到一个存储库中。

```
const TodoRepository = {
  addTodo: todo => api.post("/todos", todo),
  getTodos: filter => api.get(`/todos?filter=${filter}`)
}; 
```

我要求你看看你的 Redux 状态，看看你有多少状态与用户当前正在做的事情无关。

## 其他例子

1.  一个读书 app，用户在看书的时候应用状态是:页码，总页数，字体大小，当前页面文字。我们不会把用户读过或可能读过的每一本书都存储在 Redux 里，也不会把书的每一页都存储起来。
2.  一个购物 app，用户在搜索可乐的时候，应用状态是:搜索词可乐，结果数，结果标题。我们不会将库存中的每个产品都存储在 Redux 中，也不会存储产品细节。