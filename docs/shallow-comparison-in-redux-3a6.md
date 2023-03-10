# Redux 中的浅层比较

> 原文：<https://dev.to/machy44/shallow-comparison-in-redux-3a6>

一天在工作中，我试图在我的 React/Redux 项目中实现一个新特性。我创建了一个新的减速器，并问自己是否将减速器视为纯粹的功能是理所当然的。我将 reducers 作为一个纯函数来创建，我从未真正问过自己为什么它必须是纯的(我承认我很惭愧)。我认为这样做的唯一原因是为了更容易地完成 redux 中的撤销历史选项。

在这篇博客中，我将尝试解释为什么 reducer 不应该改变应用程序的状态。我将使用扩展操作符语法。

## 浅薄的比较

每次当你改变状态时，你需要创建一个新的对象。这个新对象在内存中将有一个新地址。这意味着我们将通过值传递对象，而不是通过引用。你可以在下一个 JS 代码中看到这意味着什么。

```
//by reference
let first = {a: 1};
let second = first; // second shows on the same space in memory as the first
first.a = 2;
first === second; // shallow comparison will log true

console.log(first); // {a: 2}
console.log(second); // {a: 2}

//by value
let firstValue = {a: 1};
let secondValue = {...firstValue}; // we pass firstValue by the value
firstValue === secondValue; // shallow comparison will log false
firstValue.a = 2;

console.log(firstValue); // {a: 2}
console.log(secondValue); // {a: 1} 
```

在 Redux 中，reducer 是一个完成特定工作的函数(它改变应用程序的状态)。你可以在下面的例子中看到这一点。

```
const initialState = {
    data: [],
    isError: false,
    isLoading: false
}

function exampleReducer(state = initialState, action) {
  switch (action.type) {
    case REQUEST_API:
      return { ...state, isLoading: true }; //creating a new state object
    case RESPONSE_API:
      return { ...state, isLoading: false, data }; //creating a new state object
    default:
      return state;
  }
} 
```

Redux 做浅层比较:

```
oldState === newState; // true or false 
```

如果状态发生了变化(创建了一个新对象)，将返回 false 值。这样，React/Redux 知道是否需要触发组件重新渲染。检查这个比做一个深度对比或者别的更有效率。

有时我们会遇到难以更新的深度嵌套对象。在这种情况下，你可以正常化应用程序的状态，或者你可以使用不可变的库，比如 Immutable.js。

## 结论

最后，更容易测试纯函数。

看着引擎盖下的事物，试着理解事物为什么会是现在这个样子，这总是好的。

如果你有一些想法要分享，或者我错过了什么，欢迎评论。