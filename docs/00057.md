# 用 React 钩子管理全局状态。

> 原文：<https://dev.to/charlesstover/manage-global-state-with-react-hooks-2692>

自从 React 16.7 中的实验性挂钩宣布以来，它们已经席卷了 React 社区。

不幸的是，与类组件只管理本地状态相同，内置的`useState` React 钩子只管理功能组件中的*本地*状态。全局状态管理仍然留给更高级的组件和社区贡献的努力。

[`reactn`包](https://github.com/CharlesStover/reactn#readme)也面向类组件，提供 React 钩子来访问和管理功能组件中的全局状态。ReactN 包旨在将全局状态集成到 React 中，就像它是本机功能一样。与像 MobX 和 Redux 这样的库不同，它们是状态管理的状态优先解决方案，ReactN 的目标是成为全局状态管理的反应优先解决方案。

为了阅读更多关于 ReactN 项目的信息或为其做出贡献，[GitHub 知识库欢迎加入社区](https://github.com/CharlesStover/reactn)。要安装 ReactN，使用`npm install reactn`或`yarn add reactn`。

# 一个使用状态概述🏁

与内置的 React 挂钩`useState`类似，ReactN 的`useGlobal`挂钩的行为尽可能相似，只有一些关键的区别。为了清楚地识别这些差异，我将首先提供`useState`的行为。

`useState`函数采用默认值并返回一个 2 项数组，其中第一项是状态值，第二项是更新该状态值的函数。

```
const [ value, setValue ] = useState(DEFAULT_VALUE); 
```

Enter fullscreen mode Exit fullscreen mode

```
import { useState } from 'react';

const MyComponent = () => {
  const [ avatar, setAvatar ] = useState('anonymous.png');
  return (
    <img
      alt="Avatar"
      onClick={() => {
        const newAvatar = prompt("Enter your avatar URL:");
        setAvatar(newAvatar);
      }}
      src={avatar}
    />
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`MyComponent`呈现了一个图像`anonymous.png`(因为这是状态的默认值)。当您单击该图像时，系统会提示您输入新的头像 URL。功能组件的状态用这个新的 URL 更新，并且重新呈现(由于状态改变)，显示您输入的图像。

如果你想只在这个组件中追踪头像*，这非常有用。但是如果你有多个显示用户头像的组件呢？还是同一组件的多个实例？每个`MyComponent`实例都有自己的状态实例，这意味着每个`MyComponent`实例都可以有一个*不同的状态*。在许多类似的情况下，开发人员选择一个全局状态来代替——确保所有组件彼此同步。如果一个组件更新了用户的头像，那么所有其他显示用户头像的组件也需要更新。*

# 全局状态差异🆚

处理全局状态时的一个重要区别是，在实例化状态时拥有一个*默认值*是多么的荒谬。如果依赖于用户头像的每个组件都必须有一个默认值，那么这个值就不是真正的全局值:组件之间不会同步，因为每个组件都有自己不同的值。你可以给它们每个相同的默认值，但是此时你没有使用[干代码](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。每次你想改变默认值，你都要经历在*的每个元件*上改变它的努力。这不仅是一个巨大的烦恼，而且当在变更过程中有一个组件碰巧被忘记时，它还会给你带来错误。

因此，全局状态通常在使用它的组件的之外被实例化*。如果预先给全局状态一个值，那么组件就不需要提供默认值，以防它不存在——它已经存在了。*

# 实例化全局状态🌞

[使用 ReactN，您可以用`setGlobal`助手函数实例化全局状态](https://github.com/charlesStover/reactn#install)。只需提供状态对象，就大功告成了。

```
import { setGlobal } from 'reactn';

setGlobal({
  avatar: 'anonymous.png'
}); 
```

Enter fullscreen mode Exit fullscreen mode

建议这发生在`ReactDOM.render`之前，因为您通常希望状态在任何组件尝试挂载之前存在。

# 使用全局状态🌎

如前所述，使用全局状态就像使用本地状态一样简单。它是一个 React 挂钩，前缀为`use`，位于函数组件的顶部，返回一个 2 项数组，其中第一项是状态值，第二项是更新状态值的函数。因为默认值是在别处实例化的，所以不要将默认值作为参数传递给全局状态钩子；相反，它接收您想要访问的全局状态的*属性名*。全局状态是一个包含许多不同值的对象，您可能希望在整个应用程序中管理这些值，而不是单个值。在实例化的例子中，我们创建了一个`avatar`属性，所以我们将在这里访问它。

```
import { useGlobal } from 'reactn';

const MyComponent = () => {
  const [ avatar, setAvatar ] = useGlobal('avatar');
  return (
    <img
      alt="Avatar"
      onClick={() => {
        const newAvatar = prompt("Enter your avatar URL:");
        setAvatar(newAvatar);
      }}
      src={avatar}
    />
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。我们将`useState`更改为`useGlobal`，并用我们想要访问的属性替换默认状态值。每当全局属性 avatar 被任何组件更新时，所有使用`useGlobal('avatar')`的组件将使用新值重新渲染。

# 我可以访问整个全局状态吗？👪

是啊！如果你不给`useGlobal`提供一个属性，它将返回*整个全局状态*供你随意使用。

```
const MyComponent = () => {
  const [ global, setGlobal ] = useGlobal();
  return (
    <img
      alt="Avatar"
      onClick={() => {
        const newAvatar = prompt("Enter your avatar URL:");
        setGlobal({
          avatar: newAvatar
        });
      }}
      src={global.avatar}
    />
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

与提供特定属性时一样，只有在访问属性时，组件才会重新呈现，而不是在全局状态更新时。如果您希望有条件地订阅某些属性，这可能会很有用。你的组件只有在访问 `global.property`而不是每次`global.property`更新的时候才会重新渲染*。* 

```
const MyComponent = () => {
  const [ global, setGlobal ] = useGlobal();
  if (global.x) {
    return global.x;
  }
  return global.y;
}; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，如果`global.x`是真的，你的组件只会在全局状态的`x`属性更新时重新渲染，而不会在全局状态的`y`属性更新时重新渲染。这是因为全局状态的`y`属性根本不会影响组件的渲染！

如果`x`属性为 falsey，那么无论是`x`还是`y`更新，组件都会更新。这是因为`x`和`y`的变化都会影响组件的渲染。

这里的“神奇”之处在于，当组件访问的全局状态属性*发生变化时，组件会重新呈现。以上，如果`x`为 the，则`y`属性为*从未访问过*。组件在有机会之前返回。如果`x`为假，则`y`属性*被*访问。*

如果你要访问`useGlobal('x')`和`useGlobal('y')`，你将同时访问`x`和`y`属性——即使你要忽略`y`。因此，当未使用的`y`属性改变时，您的组件将会更新。

# 减速器呢？🤔

React 16.7 在`useState`旁边引入了一个被称为`useReducer`的漂亮钩子。`useReducer`钩子允许你传递一个减速函数和初始状态。它返回状态和一个调度函数。与由`useState`返回的`setState`不同，dispatch 函数将您的参数传递给 reducer 函数。

下面是 React 文档演示的缩减器:

```
const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'reset':
      return initialState;
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
  }
}

function Counter() {
  const [ state, dispatch ] = useReducer(
    reducer,
    {count: initialCount}
  );
  const reset = () => dispatch({ type: 'reset' });
  const increment = () => dispatch({ type: 'increment' });
  const decrement = () => dispatch({ type: 'decrement' });
  return (
    <>
      Count: {state.count}
      <button onClick={reset}>Reset</button>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`useReducer`返回状态，默认为`{ count: 0 }`，以及一个将您的参数传递给减速器的函数。reducer 采用当前状态和您的参数来确定新状态应该是什么。因为像`{ type: 'increment' }`这样的动作依赖于当前状态，所以减速器返回当前状态加 1。

ReactN 使用`useDispatch`来处理减速器。上面这个使用全局状态的例子应该是这样的:

```
import { useDispatch } from 'reactn';

setGlobal({ count: 0 });

function reducer(count, action) {
  switch (action.type) {
    case 'reset':
      return 0;
    case 'increment':
      return count + 1;
    case 'decrement':
      return count - 1;
  }
}

function Counter() {

  // Subscribe to changes to count, because our view depends on it.
  const [ count ] = useGlobal('count');

  // Dispatch changes to count using our reducer function.
  const dispatch = useDispatch(reducer, 'count');

  const reset = () => dispatch({ type: 'reset' });
  const increment = () => dispatch({ type: 'increment' });
  const decrement = () => dispatch({ type: 'decrement' });
  return (
    <>
      Count: {count}
      <button onClick={reset}>Reset</button>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

# 总结📝

## 相似之处:

*   `useGlobal`和`useDispatch`是 React 钩子。
*   `useGlobal`返回一个值和一个改变该值的函数。
*   `useDispatch`支撑减速器。
*   `useDispatch`返回改变当前状态的函数。

## 差异:

*   `useGlobal`采用属性名而不是初始值。
*   `useGlobal`可以通过不提供参数来返回整个全局状态。
*   除了一个缩减器之外，还接受一个属性名。
*   `useDispatch`不(当前)返回属性的值，以便不订阅它。
    *   如果您的组件只更新该值，当该值改变时，它不需要重新呈现。它可能根本不显示该值。

要安装 ReactN，使用`npm install reactn`或`yarn add reactn`。

# 结论🔚

改进`useGlobal`和`useDispatch` React 钩子的社区反馈和拉取请求，以及[React n 包](https://github.com/charlesStover/reactn#readme)的过多其他全局状态特性，在[GitHub 库](https://github.com/charlesStover/reactn)上受到赞赏。

如果你喜欢这篇文章，请随意给它一个心形或独角兽。又快又简单，还免费！如果你有任何问题或相关的好建议，请在下面的评论中留下。

要阅读更多我的专栏，你可以在 [LinkedIn](https://www.linkedin.com/in/charles-stover) 、 [Medium](https://medium.com/@Charles_Stover) 和 [Twitter](https://twitter.com/CharlesStover) 上关注我，或者[在 CharlesStover.com](https://charlesstover.com/)上查看我的作品集。