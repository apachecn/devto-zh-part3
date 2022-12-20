# 关于 TypeScript 的注释:React 钩子

> 原文：<https://dev.to/busypeoples/notes-on-typescript-react-hooks-28j2>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找如何在特定情况下利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### 反应钩

在“关于 TypeScript 的注释”系列的这一部分中，我们将看看如何用 TypeScript 来键入 React 钩子，并在这个过程中学习更多关于钩子的一般知识。

我们将参考关于钩子的官方 React 文档，这是一个非常有价值的资源，当我们需要学习更多关于钩子的知识或者需要特定问题的特定答案时。总的来说，在 16.8 中增加了钩子来反应，并使开发者能够在函数组件中使用状态，这在之前只有在类组件中是可能的。文档说明有基本的和附加的钩子。
基本挂钩有`useState`、`useEffect`、`useContext`，附加挂钩有`useReducer`、`useCallback`、`useMemo`、`useRef`。

#### 使用状态

让我们从`useState`开始，这是一个基本的钩子，顾名思义，它应该用于状态处理。

```
const [state, setState] = useState(initialState); 
```

Enter fullscreen mode Exit fullscreen mode

看上面的例子，我们可以看到`useState`返回一个状态值以及一个更新它的函数。但是我们如何输入`state`和`setState`？
有趣的是，TypeScript 可以推断类型，这意味着通过定义一个`initialState`，可以为状态值和更新函数推断类型。

```
const [state, setState] = useState(0);
// const state: number
const [state, setState] = useState("one");
// const state: string
const [state, setState] = useState({
  id: 1,
  name: "Test User"
});
/*
  const state: {
    id: number;
    name: string;
  }
*/
const [state, setState] = useState([1, 2, 3, 4]);
// const state: number[] 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子很好地证明了我们不需要做任何手工输入。但是如果我们没有初始状态呢？当试图更新状态时，上面的例子会中断。
我们可以在需要时使用`useState`手动定义类型。

```
const [state, setState] = useState<number | null>(null);
// const state: number | null
const [state, setState] = useState<{id: number, name: string} | null>(null);
// const state: {id: number; name: string;} | null
const [state, setState] = useState<number | undefined>(undefined);
// const state: number | null 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，与类组件中的`setState`相反，使用 update hook 函数需要返回完整的状态。

```
const [state, setState] = useState({
  id: 1,
  name: "Test User"
});
/*
  const state: {
    id: number;
    name: string;
  }
*/

setState({name: "New Test User Name"}); // Error! Property 'id' is missing
setState(state => {
  return {...state, name: "New Test User Name"}
}); // Works! 
```

Enter fullscreen mode Exit fullscreen mode

另一个值得注意的有趣的事情是，我们可以通过向`useState`传递一个函数来延迟启动状态。

```
const [state, setState] = useState(() => {
  props.init + 1;
});

// const state: number 
```

Enter fullscreen mode Exit fullscreen mode

同样，TypeScript 可以推断状态类型。

这意味着我们在使用`useState`时不需要做太多的工作，只是在我们没有初始状态的情况下，因为实际的状态形状可能会在最初渲染时计算出来。

#### 使用效果

另一个基本钩子是`useEffect`，它在处理副作用时很有用，比如日志记录、突变或订阅事件监听器。一般来说，`useEffect`需要一个运行效果的函数，该函数可以选择返回一个清理函数，这对于取消订阅和删除监听器非常有用。另外，`useEffect`可以提供第二个参数，包含一个值数组，确保效果函数只在这些值之一发生变化时运行。这确保了我们可以控制何时运行一个效果。

```
useEffect(
  () => {
    const subscription = props.source.subscribe();
    return () => {
      subscription.unsubscribe();
    };
  },
  [props.source]
); 
```

Enter fullscreen mode Exit fullscreen mode

以文档中的原始示例为例，我们可以注意到，当使用`useEffect`时，我们不需要任何额外的输入。
当我们试图返回某个不是函数或者在效果函数内部未定义的东西时，TypeScript 会报错。

```
useEffect(
  () => {
    subscribe();
    return null; // Error! Type 'null' is not assignable to void | (() => void)
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

这也适用于`useLayoutEffect`，只是效果运行的时间不同。

#### 使用上下文

`useContext`需要一个上下文对象，并返回所提供上下文的值。当提供者更新上下文时，会触发重新呈现。看一下下面的例子应该就清楚了:

```
const ColorContext = React.createContext({ color: "green" });

const Welcome = () => {
  const { color } = useContext(ColorContext);
  return <div style={{ color }}>Welcome</div>; }; 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们不需要做太多关于类型的事情。类型是推断出来的。

```
const ColorContext = React.createContext({ color: "green" });
const { color } = useContext(ColorContext);
// const color: string
const UserContext = React.createContext({ id: 1, name: "Test User" });
const { id, name } = useContext(UserContext);
// const id: number
// const name: string 
```

Enter fullscreen mode Exit fullscreen mode

#### useReducer

有时我们处理更复杂的状态，这也可能依赖于先前的状态。`useReducer`接受根据先前状态和动作计算特定状态的函数。下面的例子摘自[官方文件](https://reactjs.org/docs/hooks-reference.html#usereducer)。

```
const [state, dispatch] = useReducer(reducer, initialArg, init); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们看文档中的例子，我们会注意到我们需要做一些额外的输入工作。查看稍加改编的例子:

```
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter({initialState = 0}) {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

目前`state`无法正确推断。但是我们可以通过为 reducer 函数添加类型来改变这一点。通过在减速器函数中定义`state`和`action`，我们现在可以推断出`useReducer`提供的`state`。让我们改编一下这个例子。

```
type ActionType = {
  type: 'increment' | 'decrement';
};
type State = { count: number };
function reducer(state: State, action: ActionType) {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以确保类型是在`Counter` :
中推断出来的

```
function Counter({initialState = 0}) {
  const [state, dispatch] = useReducer(reducer, initialState);
  // const state = State
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

当试图分派一个不存在的类型时，我们会遇到一个错误。

```
dispatch({type: 'increment'}); // Works!
dispatch({type: 'reset'});
// Error! type '"reset"' is not assignable to type '"increment" | "decrement"' 
```

Enter fullscreen mode Exit fullscreen mode

`useReducer`也可以在需要时延迟初始化，因为有时可能必须先计算初始状态:

```
function init(initialCount) {
  return {count: initialCount};
}

function Counter({ initialCount = 0 }) {
  const [state, dispatch] = useReducer(red, initialCount, init);
  // const state: State
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

从上面的例子中可以看出，由于正确类型化的`reducer`函数，类型是用延迟初始化的`useReducer`来推断的。

关于`useReducer`，我们不需要知道太多。

#### 使用回调

有时候我们需要记住回调。`useCallback`接受一个内联回调和一个输入数组，仅当其中一个值发生变化时才更新记忆。我们来看一个例子:

```
const add = (a: number, b: number) => a + b;
const memoizedCallback = useCallback(
  (a) => {
    add(a, b);
  },
  [b]
); 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，我们可以用任何类型调用 memoizedCallback，而不会看到 TypeScript 抱怨:

```
memoizedCallback("ok!"); // Works!
memoizedCallback(1); // Works! 
```

Enter fullscreen mode Exit fullscreen mode

在这种特定情况下，`memoizedCallback`处理字符串或数字，尽管`add`函数需要两个数字。为了解决这个问题，我们需要在编写内联函数时更加具体。

```
const memoizedCallback = useCallback(
  (a: number) => {
    add(a, b);
  },
  [b]
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要传递一个数字，否则编译器会报错。

```
memoizedCallback("ok");
// Error! Argument of type '"ok"' is not assignable to argument of type 'number'
memoizedCallback(1); // Works! 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用备忘录

`useMemo`与`useCallback`非常相似，但是返回一个记忆化的值，而不是记忆化的回调。以下内容来自文档。

```
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]); 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果我们基于上面的内容构建一个例子，我们会注意到我们不需要做任何关于类型的事情:

```
 function calculate(a: number): number {
  // do some calculations here...
}

function runCalculate() {
  const calculatedValue =  useMemo(() => calculate(a), [a]);
  // const calculatedValue : number
} 
```

Enter fullscreen mode Exit fullscreen mode

### useRef

最后我们再来看一个钩子:`useRef`。
当使用`useRef`时，我们可以访问一个可变的引用对象。此外，我们可以向`useRef`传递一个初始值，用于初始化由可变 ref 对象公开的`current`属性。这在试图访问函数 f.e .内部的一些组件时很有用。同样，让我们使用[文档](https://reactjs.org/docs/hooks-reference.html#useref)中的例子。

```
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus(); // Error! Object is possibly 'null'
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到 TypeScript 在抱怨，因为我们用`null`初始化了`useRef`，这是一种有效的情况，因为有时设置引用可能会在稍后的时间点发生。
这意味着，我们在使用`useRef`时需要更加明确。

```
function TextInputWithFocusButton() {
  const inputEl = useRef<HTMLInputElement>(null);
  const onButtonClick = () => {
    inputEl.current.focus(); // Error! Object is possibly 'null'
  };
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

通过定义实际类型`useRef<HTMLInputElement>`更具体地使用`useRef`仍然不能消除错误。实际上检查一下`current`属性是否存在，可以防止编译器抱怨。

```
function TextInputWithFocusButton() {
  const inputEl = useRef<HTMLInputElement>(null);
  const onButtonClick = () => {
    if (inputEl.current) {
      inputEl.current.focus(); // Works!
    }
  };
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

`useRef`也可以作为[的实例变量](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables)。
如果我们需要能够更新`current`属性，我们需要使用具有通用类型`Type | null` :
的`useRef`

```
function sleep() {
  const timeoutRefId = useRef<number | null>();

  useEffect(() => {
    const id = setTimeout(() => {
      // ...
    });
    if (timeoutRefId.current) {
      timeoutRefId.current = id;
    }
    return () => {
      if (timeoutRefId.current) {
        clearTimeout(timeoutRefId.current);
      }
    };
  });
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

关于 React 挂钩，还有一些更有趣的东西需要学习，但不是特定于 TypeScript 的。如果对这个话题有更多的兴趣，请查阅关于钩子的官方 React 文档。
在这一点上，我们应该很好地理解如何输入 React 钩子。

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)