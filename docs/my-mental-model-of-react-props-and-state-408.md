# 我的反应道具和状态的心理模型

> 原文：<https://dev.to/umardaraz/my-mental-model-of-react-props-and-state-408>

在这篇短文中，我想向你介绍我对反应状态和道具的看法。这是一个非常简单的模型，除此之外还有很多东西需要支持和陈述。但是我发现用这个模型来思考两个基本概念是很有用的。
可能是你发现像我一样有用。

## 道具

道具就像函数的参数。我们使用这些来使组件动态化，就像我们使用参数来使函数动态化一样。

让我们看一个向哈利波特显示问候的组件。

```
const GreetingHarryPoter = () => (
    <span>Hello Harry Poter</span>
) 
```

现在我想对任何人说问候，不仅仅是哈利·波特，我可以重构我的组件来接受一个道具，即人名。并将我的组件重命名为更通用的名称，因为现在我可以使用该组件向任何人问好。

```
const Greeting = ({name}) => (
    <span>Hello {name}</span>
) 
```

现在我可以向不同的人打招呼，比如

```
const App = () => (
    <Greeting name="Harry Potter">
    <Greeting name="Hermione Granger">
    <Greeting name="Ron Weasley">
) 
```

## 状态

React 应用程序中发生的任何事情，我们都通过改变状态来捕捉。
喜欢

*   用户点击一个按钮，我们改变状态。
*   网络返回数据，我们改变状态。
*   如果发生其他情况，我们会改变状态。

让我们考虑一个简单的例子，我们可以增加或减少计数器。

让我们创建一个没有状态的组件。

```
const Counter = () => {
  return (
    <div>
      <button>-</button>1<button>+</button>
    </div>
  );
}; 
```

让我们介绍一下状态。

我们希望将初始计数器值设置为 1。但是当你点击增量或减量时，我们想要改变计数器的值。为了使计数器动态，我们用 useState 钩子初始化它。
(使用状态由 react 提供，用于将状态引入我们的组件)

```
const Counter = () => {
  const [count, setCount] = useState(1);
  return (
    <div>
      <button>-</button>
      {count}
      <button>+</button>
    </div>
  );
}; 
```

我们用初始值(即 1)调用 useState，它返回给我们值(count)和一个函数(setCount)以在将来更改该值。

现在我们有了一个函数，每当你点击增量(-)或减量(+)按钮来改变数值时，我们都可以调用这个函数。

```
const Counter = () => {
  const [count, setCount] = useState(1);
  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  return (
    <div>
      <button onClick={decrement}>-</button>
      {count}
      <button onClick={increment}>+</button>
    </div>
  );
} 
```

就这样，现在我们有了一个动态计数器，它通过改变状态来响应用户的点击，并负责呈现更新后的状态。

### 总结

在最简单的层面上，我们可以将道具视为函数的参数。我们可以认为状态是更新用户界面的反应方式。