# 在 9 行中保持您的反应状态

> 原文：<https://dev.to/selbekk/persisting-your-react-state-in-9-lines-of-code-9go>

这个周末我在玩一个来自 [Frontend Mentor](https://beta.frontendmentor.io) 的项目，我用 React hooks 实现了这个主题切换器。我突然意识到，在重新加载之间坚持我选择的主题将是一个很好的特性。所以让我们建立一个钩子来提供这个功能！

本文将带您完成创建一个可重用的定制钩子的过程，这个钩子将我们的状态保存到本地存储中。

## 入门

我们将创建一个名为`usePersistedState`的定制钩子来将我们的状态存储到本地存储器中。我们的函数应该接受一个键来存储状态，以及默认值(以防我们还没有保存任何东西)。它将返回与`useState`相同的 API(一个状态元组和一个更新函数)。这里是我们的钩签名:

```
function usePersistedState(key, defaultValue) {
  // Some magic
  return [state, setState];
} 
```

Enter fullscreen mode Exit fullscreen mode

尽管我们将状态存储在本地存储中，但是我们在常规的`setState`调用中保留了一个本地运行时副本。这是为了让我们可以触发重新渲染，并稍微改善访问时间(访问本地存储可能没有那么快)。最后，如果 localStorage 由于某种原因不可用，我们仍然有一个工作挂钩(尽管它不会持久化设置)。

```
function usePersistedState(key, defaultValue) {
  const [state, setState] = React.useState(defaultValue);
  return [state, setState];
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在本地存储中保存数据

接下来，让我们从本地存储开始读取！`localStorage` API 内置在您的浏览器中，允许您通过使用字符串键调用`getItem`函数来访问值。

```
function usePersistedState(key, defaultValue) {
  const [state, setState] = React.useState(
    localStorage.getItem(key) || defaultValue
  );
  return [state, setState];
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们将调用`useState`的缺省值设置为我们已经存储在 localStorage 中的值，或者我们作为参数传入的`defaultValue`。接下来，让我们也实现本地存储的更新。我们将为此使用一个`useEffect`钩子:

```
function usePersistedState(key, defaultValue) {
  const [state, setState] = React.useState(
    localStorage.getItem(key) || defaultValue
  );
  useEffect(() => {
    localStorage.setItem(key, state);
  }, [key, state]);
  return [state, setState];
} 
```

Enter fullscreen mode Exit fullscreen mode

很聪明，对吧？每次更新状态时，我们都应该更新本地存储中存储的内容。如果键改变了，我们也想在新的键下存储我们的当前状态。

## 复数值呢？

虽然本地存储 API 很棒，但是只能存储字符串值。这是一种痛苦——但是我们可以通过在更新状态时将 JavaScript 对象序列化为 JSON 来绕过这个限制。我们用 [`JSON.parse`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) 和 [`JSON.stringify`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 函数来实现。

```
function usePersistedState(key, defaultValue) {
  const [state, setState] = React.useState(
    JSON.parse(localStorage.getItem(key)) || defaultValue
  );
  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(state));
  }, [key, state]);
  return [state, setState];
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们也支持复杂的数据结构！

## 最后一次性能优化

我们当前的实现有一个性能缺陷——每次渲染时我们都从本地存储器中读取数据！更糟糕的是——我们这样做只是为了获取我们的`useState`调用的初始值！幸运的是，有一种方法可以解决这类问题。**通过向`useState`传递一个函数，默认值将只运行一次！**

让我们来实现这个:

```
function usePersistedState(key, defaultValue) {
  const [state, setState] = React.useState(
    () => JSON.parse(localStorage.getItem(key)) || defaultValue
  );
  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(state));
  }, [key, state]);
  return [state, setState];
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结！

就是这样！我们用几行代码实现了一段非常简洁的可重用代码。这非常适合本地设置，如主题、字体大小或任何您希望在两次访问之间保持的 UI 状态。

这是我最初提到的项目，用这个钩子来保存选中的主题。试试吧！

[https://codesandbox.io/embed/qm7k80y9](https://codesandbox.io/embed/qm7k80y9)

你最喜欢的可重复使用的挂钩是什么？