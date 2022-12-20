# 我使用特效，告别我的生命周期，感受🙂

> 原文：<https://dev.to/ma5ly/i-used-effects-said-goodbye-to-my-lifecycles-and-feel--1i4i>

上周终于增加了钩子来应对。现在可以在函数中使用`state`。至少，这是我在观看最初的公告时所记得的。但是生命周期...我不知道他们应该如何工作。我决定阅读[文件](https://reactjs.org/docs/hooks-intro.html)并进行实验。

## 让我们实验一下

让我们试着构建一个小应用程序，它保存一些`state`并根据`state`的变化做一些事情。通常我会依靠我心爱的`componentDidUpdate`,但是她已经走了...嗯，不是真的:`classes`将很大程度上保留 React 的一部分，但仍然...我可能已经和他们说再见了。

好吧，是“应用程序”。让我们用一个`onChange`处理程序创建一个数字`input`字段，用一个`button`将所述的`input`添加到之前的`input`中，最后用一个`button`从暗模式切换到亮模式，反之亦然(不知何故，似乎每个人都喜欢这样)。

在我们的胡克时代，这种情况的初始状态应该是这样的:

```
function App() {
  const [lights, setLights] = useState(true);
  const [input, setInput] = useState(0);
  const [sum, setSum] = useState(0);
} 
```

接下来，我们将把它与 HTML 元素挂钩，就像我们在`classes`中所做的一样。

```
return (
    <div
      style={{
        padding: 15,
        fontFamily: "Arial",
        background: lights ? "salmon" : "black",
        color: lights ? "black" : "white"
      }}
    >
      <h1
        style={{ fontFamily: "Times New Roman", margin: 0, paddingBottom: 15 }}
      >
        Sumple UseEffect
      </h1>
      <div>
        <strong>Sum:</strong> {sum}
      </div>
      <div style={{ marginTop: 15 }}>
        <input
          type="number"
          value={input}
          onChange={e => setInput(e.target.value)}
          style={{ width: 50 }}
        />
        <button onClick={handleSum}>
          +
        </button>
      </div>
      <div style={{ position: "absolute", top: 30, right: 30 }}>
        <button onClick={() => setLights(!lights)}>
          Lights {lights ? "off" : "on"}
        </button>
      </div>
    </div>
  ); 
```

希望你不会觉得这很奇怪。代码中唯一缺少的是`handleSum`函数。看起来是这样的:

```
 function handleSum() {
    setSum(Number(sum) + Number(input));
    setInput(0);
  } 
```

它是在我们的`App`函数中定义的，只是将`input`状态添加到我们的`sum`状态中；然后将`input`设置回零。我们现在应该有一个可用的应用程序了。到目前为止，一切顺利。

## 我们使用特效

现在我们决定要验证数字输入。如果该值为 0 或大于任意选择的数字 480，我们将禁用`+`按钮。当然，我们实际上并不需要额外的状态，但是我们还是做吧。

我们将`valid`创建为一个状态。

```
 const [valid, setValid] = useState(true); 
```

然后我们把`disabled`加到我们的`button`上。

```
 <button disabled={!valid} onClick={handleSum}>
     +
  </button> 
```

如果我们还在使用`classes`的话，我们可能会做这样的事情。

```
componentDidUpdate(prevProps, prevState) {
  if(prevState.input !== this.state.input) {
     if (this.state.input > 480 || this.state.input === 0) {
        this.setState({valid: false});
      } else {
        this.setState({valid: true});
      }
  }
} 
```

但我们没有。所以我们没有`componentDidUpdate`。相反，我们有`useEffect`，React 承诺在每个`render`之后运行的函数。这很方便，因为`input`的每一个变化都会触发一个`render`。所以我们可以使用`useEffect`来进行验证。

```
 useEffect(
    () => {
      if (input > 480 || input === 0) {
        setValid(false);
      } else {
        setValid(true);
      }
    },
    [input]
  ); 
```

我们的新朋友接受一个函数作为第一个参数，一个数组作为可选的第二个参数。我希望这个函数有意义，但是如果你对没有比较`state`和`array`有疑问，我会理解的。幸运的是，这两者是有联系的。

原来，你再也不用比较`props`或者`state`了。React 允许您在第二个参数中提供想要运行`useEffect`的值。所以在我们的例子中,`useEffect`只会在`input`发生变化时运行。

## 我们上马了吗？

所以我们现在有了一种基于`props`或`state`的变化来“做事情”的方法。但是，最初帮助我们设置订阅的生命周期是怎样的呢？还是让我们可以再次清理它们的那个？我说的当然是`componentDidMount`和`componentWillUnMount`。

我们需要一种不管变化都运行`useEffect`的方法，但不是每次变化都运行。最好只在开始时一次，在结束时一次。幸运的是，这是非常可能的。

```
 function handleEscape(e) {
    if (e.keyCode === 27) {
      setSum(0);
      setInput(0);
    }
  }

  useEffect(() => {
    document.addEventListener("keydown", handleEscape);
    return () => document.removeEventListener("keydown", handleEscape);
  }, []); 
```

如果我们在第二个参数中将`array`留空，React 只运行一次代码，并在组件卸载时将`return`视为清理函数。在我们的例子中，我们可以使用它来添加一个监听器，以便在有人按下`escape`时重置我们的`sum`和`input`。

但是我们的另一个`useEffect`呢？我们必须合并它们吗...？

答案是否定的！你可以在同一个组件中使用多个`useEffects`。这很好，因为我们将添加更多内容。我很好奇我换了多少次`lights`或者`sum`。

所以我们设置了另一个`useEffect`，并用`lights`和`sum`填充我们的`array`。现在这个效果只有在`lights`或者`sum`改变的时候才会生效。这很好，但我刚刚意识到我想知道这两个改变了，所以我可以保持更详细的统计数据。

答案其实是做两个`useEffects`，但是我梦回了(昨天)我还在用`classes`的日子，想做一个以前和现在值的比较，于是我自己发现哪个变了。但是如何获得这些先前的值呢？React docs 为我们提供了帮助。

```
function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  });
  return ref.current;
} 
```

是这样用的:

```
 const previousValues = usePrevious({ lights, sum }); 
```

因此，我们将状态值传递给函数，然后将它们保存在`ref`中，并返回 ref。这确保了我们得到的不是最新的值，而是以前的值。这看起来比实际需要的要困难得多，但是请记住，我们可以在第二个参数中使用两个`useEffect`函数，每个函数都有另一个值。

让我们完成我们的单曲`useEffect`:

```
 useEffect(
    () => {
      if (previousValues && previousValues.lights !== lights) {
        const newActions = Object.assign({}, actions, {
          lights: actions.lights + 1,
          total: actions.total + 1
        });
        setAction(newActions);
      } else if (previousValues && previousValues.sum !== sum) {
        const newActions = Object.assign({}, actions, {
          sum: actions.sum + 1,
          total: actions.total + 1
        });
        setAction(newActions);
      }
    },
    [lights, sum]
  ); 
```

那行得通。我们现在有一个已经使用的操作的日志。让我们把它呈现在页面上。

```
 <div>
    <strong>Actions:</strong> {actions.total}
  </div> 
```

我们用尽我们的诡计了吗？我们没有。在每个渲染上运行一个怎么样？这将允许我们计算我们看到了多少渲染，对吗？可能会很有趣。让我们开始吧。

但是等等。即使我们有一个在每次渲染后运行的`useEffect`,我们如何在不触发另一次渲染的情况下保存渲染量呢？我们可以`useRef`为此。

```
 const renders = useRef(0); 
```

我们用 0 初始化`ref`。然后我们创造另一种效果。

```
 useEffect(() => {
    renders.current = renders.current + 1;
  }); 
```

由于`ref`的`current`属性是可变的，我们可以给它赋值。但是你注意到我们的`useEffect`没有第二次争论了吗？我想我之前提到过，但是第二个参数是可选的。如果您不提供它，`useEffect`将在每次渲染时运行。非常适合这个案子。

剩下的就是在屏幕上渲染我们的渲染计数。

```
 <div>
    <strong>Renders:</strong> {renders.current}
  </div> 
```

是的，你可以只渲染`ref`的`current`值。厉害！

## 我幸福吗？

写完这个应用程序后，我想知道我是否对这一切感到满意。说实话，我觉得我是。虽然需要一点时间来适应，但它非常通用。这也可能是它的“问题”。虽然，一旦你真正开始使用`useEffect`你可能会发现这根本不是问题。

我可能还需要一点时间才能真正适应钩子和它们带来的一切，但我已经感受到了它们的潜力。如果您有空间和时间来试验它们，我强烈建议您这样做。不用着急，但是他们很酷。他们真的是🙂。

[完整代码](https://codesandbox.io/s/0p1wx7vxqv)有兴趣的话。