# 你认为你需要使用 Reducer 吗？你可能想用方法来代替

> 原文：<https://dev.to/pelotom/think-you-need-to-usereducer-you-might-want-to-usemethods-instead-29h5>

[`useReducer`](https://reactjs.org/docs/hooks-reference.html#usereducer) 的威力是有据可查的。它是 React 钩子中所有状态管理的基础构件，所以最终任何基于钩子的状态管理都依赖于它。但值得一问的是，这是我们能想到的最好的 API 吗？我们必须承认，它迫使我们以一种相当笨拙的风格来编写我们的逻辑。

我们来看一个小例子。`Counters`组件呈现一个计数器列表，每个计数器都可以递增或清除，最后还有一个添加新计数器的按钮。

```
const Counters = () => {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <>
      <Button onClick={() => dispatch({ type: 'ADD_COUNTER' })}>add counter</Button>
      {counters.map(({ id, count }) => (
        <Counter
          key={id}
          count={count}
          onIncrement={() => dispatch({ type: 'INCREMENT_COUNTER', id })}
          onClear={() => dispatch({ type: 'CLEAR_COUNTER', id })}
        />
      ))}
    </>
  );
};

const initialState = {
  nextId: 0,
  counters: [],
};

const reducer = (state, action) => {
  switch (action.type) {
    case 'ADD_COUNTER': {
      const nextId = state.nextId + 1;
      return {
        nextId,
        counters: [...state.counters, { id: nextId, count: 0 }],
      };
    }
    case 'INCREMENT_COUNTER': {
      const index = state.counters.findIndex(counter => counter.id === action.id);
      const counter = state.counters[index];
      return {
        ...state,
        counters: [...state.counters.slice(0, index), { ...counter, count: counter.count + 1 }],
      };
    }
    case 'CLEAR_COUNTER': {
      const index = state.counters.findIndex(counter => counter.id === action.id);
      const counter = state.counters[index];
      return {
        ...state,
        counters: [...state.counters.slice(0, index), { ...counter, count: 0 }],
      };
    }
  }
}; 
```

关于这一点需要注意一些事情:

#### 你所有的逻辑都在一条`switch`语句中

在这个玩具示例中，它看起来并不太糟糕，但是您可以想象，随着更多的操作，它可能会变得很麻烦，您可能希望提取 switch 语句将调用的独立函数。

#### 每个案例必须返回一个新版本的状态

从概念上讲，我们想在`INCREMENT_COUNTER`中做的只是...增加一个计数器！世界上最简单的事情。但是因为状态是不可变的，所以我们需要经历各种各样的困难来产生一个新的副本。这并不是我们问题的结束，因为...

#### 确保在数据结构中实现共享取决于您自己

也就是说，如果从概念上讲，一个动作在当前状态下没有任何效果，那么你就要确保返回相同的*状态，而不仅仅是一个结构上相等的新状态，否则可能会导致不必要的渲染。在这种情况下，我们没有做到这一点，特别是在`CLEAR_COUNTER`的情况下。如果计数器已经在给定的索引处`0`，清除它应该没有任何效果，但是我们的代码将创建一个全新的数组，并重新呈现我们所有的`Counter`子元素，即使它们已经`React.memo`化了！*

#### 将`dispatch`转换为回调取决于你

在某些时候，你需要将你的`dispatch`函数转换成回调函数，这既尴尬又容易破坏记忆。在这里，我们在每次渲染时都会传递新的箭头函数给`Button`和`Counter`组件。所以，再一次地，`React.memo`美化它们是没有用的。解决这个问题的标准选择是要么将整个`dispatch`功能传递给这些子组件，给孩子城堡的钥匙，并强迫他们专用于父母的用例，要么使用`useCallback`进行回调。

## 解决方案:`useMethods`

我就开门见山了:还有一个更好的办法，叫`useMethods`。下面是我们如何用它重写上面的例子:

```
const Counters = () => {
  const [
    { counters },
    { addCounter, incrementCounter, clearCounter }
  ] = useMethods(methods, initialState);

  return (
    <>
      <Button onClick={addCounter}>add counter</Button>
      {counters.map(({ id, count }) => (
        <Counter
          key={id}
          id={id}
          count={count}
          onIncrement={incrementCounter}
          onClear={clearCounter}
        />
      ))}
    </>
  );
};

const initialState = {
  nextId: 0,
  counters: [],
};

const methods = state => ({
  addCounter() {
    state.counters.push({ id: state.nextId++, count: 0 });
  },
  incrementCounter(id) {
    state.counters.find(counter => counter.id === id).count++;
  },
  clearCounter(id) {
    state.counters.find(counter => counter.id === id).count = 0;
  },
}); 
```

看起来干净多了，对吧？需要注意的事项:

*   逻辑现在被很好地封装在单独的方法中，而不是在一个巨大的`switch`语句中。我们可以使用简单的函数参数，而不必从动作对象中提取“有效载荷”。
*   我们可以使用变异的语法来编辑我们的状态。由于 [`immer`](https://github.com/mweststrate/immer) 的魔力，实际上*并没有编辑底层状态，而是产生了一个新的不可变副本。*
*   我们得到的不是一个通用的`dispatch`函数，而是一组细粒度的回调，每个回调对应一个概念上的“动作”。我们可以将这些回调直接传递给子组件；它们只被创建一次，所以它们不会破坏记忆和导致不必要的渲染。不需要`useCallback`,除非我们需要一个回调，这个回调还没有直接映射到我们的一个状态改变动作！

## 结论

下次你需要使用`useReducer`的全部能量时，你可以考虑使用`useMethods`来代替。它同样富有表现力，但没有笨重的动作包袱，开箱即可获得出色的性能。

试试看:[https://github.com/pelotom/use-methods](https://github.com/pelotom/use-methods)

下面是这篇文章中代码的完整工作示例:[https://codesandbox.io/s/2109324q3r](https://codesandbox.io/s/2109324q3r)