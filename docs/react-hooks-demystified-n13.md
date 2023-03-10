# 汇总的反应挂钩

> 原文：<https://dev.to/jovidecroock/react-hooks-demystified-n13>

# 简介

在 React 中我们有两种类型的组件，一个类和一个函数。当我们需要逻辑和/或状态时，类几乎总是我们的选择，因为这在组件函数中是不可能的(在到达挂钩之前，广泛称为无状态功能组件)。

现在时代变了，我们有了钩子！

## 什么是钩子

它不仅意味着将状态引入我们的功能组件，还意味着:

*   替换生命周期以处理副作用
*   引入可变引用
*   构成逻辑

我们正在处理的东西可以帮助我们提高我们的抽象，并使用它们，而不必为它制作更高阶的组件或渲染道具组件。

钩子只能在函数组件内部调用(所以不能在类中调用),并且应该在顶层声明。

### 高阶组件

高阶组件使得重用组件逻辑成为可能。这不是 React 提供的什么神奇的东西，而更像是一种设计模式(就像常规的高阶函数一样，想想去抖)。

大多数库导出一个特设，典型地以“with”开始，为该库注入特定的道具，像 react-router 的`withRouter`。在 withRouter 的情况下，当你把它放在你的组件`withRouter(myComponent)`上时，你的组件将拥有历史、匹配，...注入了它的属性。

### 生命周期

生命周期是对类组件中的呈现做出反应的一种方式，这些是(最重要的):

*   componentDidMount(该组件第一次装入 dom 后)
*   componentDidUpdate(由于道具更改而导致组件更新之后)
*   componentWillUnmount(卸载组件之前)

## 为什么不用一个类

历史证明，类比常规函数更复杂，其中很大一部分由我们神秘的 *this* 扮演。

当使用一个类时，我们必须填充 polyfill，如果我们想支持更年轻的浏览器，这会自动使我们的应用程序比普通函数更重。

# 钩子

在这一部分中，我将详细阐述我认为在日常开发中最有用的挂钩。在这里，我也将包括我的一些指针。

## 使用状态

这与类中的`this.state`相同，它保存一个可以用函数改变的值。

基本用法:

```
const Post = ({ title }) => {
  const [likes, setLikes] = React.useState(0);
  const like = () => setLikes(likes + 1);
  return (
    <div>
      <p>{title} {likes}</p>
      <button onClick={like}>Like!</button>
    </div>
  )
} 
```

我们传递给 useState 的参数是初始值，它返回给我们一个数组[value，alterValue]。这可能不常见，但这是数组析构。这样做的好处是你可以自由命名你的变量。

你也可以惰性的初始化你的初始化状态，让我们来评估一下这个声明。
举个例子，我们的初始状态是这样的:`users.filter(({ age }) => age > 40)`。
每次我们遇到这个函数，它都会被求值，但是如果我们写`() => users.filter(({ age }) => age > 40)`。它只会被执行一次。

setter，在本例中为`setLikes`，有两种不同的使用方式:

*   setLikes(值)
*   setLikes((当前值)= >新值)

第二种方法为您提供了更改最近值的最大确定性。

就我个人而言，我只写单值的 useState，比如一个数字，布尔值，...对于数组和对象，我倾向于使用下一个 *useReducer* 。

## useReducer

这个钩子与 redux 中的 reducer 非常相似，所以`useReducer`接受第一个参数，它是一个函数(reducer 函数),第二个参数是 initialState。

减速器功能示例:

```
function reducer(state, action) {
  switch (action.type) {
    case 'LIKE': {
      return { ...state, likes: state.likes + 1 }
    }
    default: throw new Error('Unknown action received')
  }
} 
```

就我个人而言，我真的喜欢让默认抛出一个错误，因为这些减少器是孤立的。这个钩子再次返回一个数组，第一个参数是当前状态，第二个是一个通常被称为`dispatch`的函数。这是因为你可以给它一个特定类型的对象。这个对象将作为第二个参数传递给 reducer 函数(您可以在上面看到这个函数),并将根据这个动作的类型触发某种状态转换。

```
const Post = ({ title }) => {
  const [state, dispatch] = React.useReducer(reducer, { title, likes: 0 });
  const like = () => dispatch({ type: 'LIKE' });
  return (
    <div>
      <p>{state.title} {state.likes}</p>
      <button onClick={like}>Like!</button>
    </div>
  )
} 
```

因此，每次我们单击按钮时，都会调度一个“LIKE”类型的操作，这与第一种情况相匹配。也就是说我们的赞会增加一个。

## useRef

过去是这样的，当我们想要在一个组件上有一个 ref 时，我们必须创建一个类，现在已经改变了！现在我们有了 React.useRef，它返回给我们一个 refObject，我们可以把它放在一个组件/html 元素上。

```
const Input = () => {
  const textInputRef = React.useRef();
  return <input ref={textInputRef} />
} 
```

现在`textInputRef.current`将成为输入元素，这意味着我们可以像 textInputRef.current.focus()这样做来聚焦它。

useRef 也可以用来保存一个值的前一个版本，或者保存一个变量，就像我们在类中使用的一样，this.hasMounted = true(这不是一个好的实践，但是它提供了一个很好的例子)

## 使用回调

在我所有的例子中，你可以看到我在 render 中绑定了一个函数，这使得每次渲染都有一个新的函数。这使得一个 PureComponent 永远不会有浅相等的道具...用 useCallback 对救援做出反应！

只要输入相等，useCallback 就会返回相同的函数，让我们从 useState 示例中改进函数“like”:

```
const Post = ({ title }) => {
  const [likes, setLikes] = React.useState(0);
  const like = React.useCallback(() => setLikes(likes + 1), [setLikes, likes]);
  return (
    <div>
      <p>{title} {likes}</p>
      <button onClick={like}>Like!</button>
    </div>
  )
} 
```

只要 setLikes 和 Likes 不改变，我们的 like 函数将总是有相同的引用，这意味着它等于前面的引用。

## 使用备忘录

这使我们能够记忆昂贵的计算，这使用了与 useCallback 相同的数组输入机制。

假设我们想要过滤一个 40 岁以上的人的列表，我们不想在每次渲染时都这样做，相反，我们希望只有当我们的用户数组发生变化时才这样做。

```
const Persons = ({ people }) => {
  const overForty = React.useMemo(() => people.filter(({ age }) => age > 40), [people]);
  return overForty.map(({ name }) => <p>{name}</p>)
} 
```

## 使用效果

当我们想要调用 redux 动作来获取数据时，或者当一个变量改变时，我们现在可以在功能组件中这样做，这要感谢 useEffect。

```
const PersonsContainer = ({ isLoading, fetchPersons, maxAge }) => {
  React.useEffect(() => {
    fetchPersons({ maxAge });
  }, [maxAge])
  return isLoading ? 'loading' : 'loaded';
} 
```

在本例中，每次 maxAge 改变时，都会触发一次重取。

你也可以返回一个函数给 useEffect，这个函数将在效果被清理后执行，这意味着它非常适合处理退订之类的事情。

## useLayoutEffect

这类似于 useEffect，只是它意味着布局上的副作用，所以假设您想在 mount 上聚焦一个输入，在 unmount 上模糊它(这是一个非常无用的例子，但让我们继续下去)。

```
const Input = () => {
  const textInputRef = React.useRef();
  useLayoutEffect(() => {
    textInputRef.current.focus();
    return () => textInputRef.current.blur();
  }, [])
  return <input ref={textInputRef } />
} 
```

一个空的输入数组永远不会改变，所以它只会在第一次函数执行时运行，并且只有当它不再被挂载时才会被清除。

# 自定义挂钩

钩子是替代渲染道具/高阶组件的理想选择，这意味着这是一种很好的逻辑组合方式。

假设我们要设置文档标题，让它听更改，那么我们可以做一个简单的自定义钩子:

```
export default function useTitle(title) {
  useEffect(() => { document.title = title }, [title])
} 
```

# 一些转换

让我们通过一些常见的场景来看看类是如何转化为钩子的。

## 滚动头

一个通用组件，用于使用户在导航时总是从页面顶部开始。

### 之前

```
class ScrollToTop extends React.Component {
  static propTypes = {
    children: PropTypes.node.isRequired,
    history: PropTypes.shape({
      listen: PropTypes.func.isRequired,
    }).isRequired,
  }

  componentDidMount() {
    const { history } = this.props;
    this.historyUnlisten = history.listen(() => window.scrollTo(0, 0));
  }

  componentWillUnmount() {
    this.historyUnlisten();
  }

  render() {
    const { children } = this.props;
    return children;
  }
} 
```

### [后](#after)

```
const ScrollToTop = ({ children, history }) => {
  React.useLayoutEffect(() => {
    const unlisten = history.listen(() => window.scrollTo(0, 0));
    return () => { unlisten(); };
  }, []);
  return children;
};

ScrollToTop.propTypes = {
  children: PropTypes.node.isRequired,
  history: PropTypes.shape({
    listen: PropTypes.func.isRequired,
  }),
}; 
```

请注意，layoutEffect 只触发一次来侦听，处理器只触发一次来取消侦听，这是一个 didMount，将一次全部卸载。

## 数据表

啊，带有搜索、分页和抓取的经典数据列表...

### 之前

```
const ComponentForX = ({ name }) => <p>{name}</p>;

const LIMITS = [10, 20, 50];

class DataList extends PureComponent {
  state = {
    page: 0,
    limit: 10,
    search: '',
  }

  componentDidMount() {
    const { fetchData } = this.props;
    const { limit, page, search } = this.state;
    fetchData({ limit, page, search });
  }

  componentDidUpdate(prevProps, prevState) {
    const { fetchData } = this.props;
    const { limit, page, search } = this.state;
    if (
      limit !== prevState.limit
      || page !== prevState.page
      || search !== prevState.search
    ) {
      fetchData({ limit, page, search });
    }
  }

  changeLimit = (newLimit) => {
    this.setState({ limit: newLimit });
  }

  onChangeSearch = (e) => {
    this.setState({ search: e.currentTarget.value });
  }

  nextPage = () => {
    const { page } = this.state;
    this.setState({ page: page + 1 });
  }

  prevPage = () => {
    const { page } = this.state;
    this.setState({ page: page - 1 });
  }

  render() {
    const { limit: currentLimit, search } = this.state;
    const { data } = this.props;
    return (
      <div>
        <input
          placeholder="search"
          onChange={this.onChangeSearch}
          value={search}
          type="text" />
        {LIMITS.map(limit => (
          <button
            key={limit}
            className={currentLimit === limit ? 'selected' : null}
            type="button"
            onClick={this.changeLimit.bind(this, limit)}>
            {limit}
          </button>
        ))}
        {data.map(x => <ComponentForX key={x.id} {...x} />)}
        <button type="button" onClick={this.prevPage}>Previous page</button>
        <button type="button" onClick={this.nextPage}>Next page</button>
      </div>
    );
  }
} 
```

### [后](#after)

```
const ComponentForX = ({ name }) => <p>{name}</p>;

const LIMITS = [10, 20, 50];

const DataList = ({ data, fetchData }) => {
  const [currentPage, setCurrentPage] = React.useState(0);
  const [currentLimit, setCurrentLimit] = React.useState(10);
  const [currentSearch, setCurrentSearch] = React.useState('');

  React.useEffect(() => {
    fetchData({ limit: currentLimit, page: currentPage, search: currentSearch });
  }, [currentPage, currentLimit, currentSearch]);

  const changeLimit = React.useCallback((newLimit) => {
    setCurrentLimit(() => newLimit);
  }, []);

  const onChangeSearch = React.useCallback((e) => {
    setCurrentSearch(() => e.currentTarget.value);
  }, []);

  const nextPage = React.useCallback(() => {
    setCurrentPage(p => p + 1);
  }, []);

  const prevPage = React.useCallback(() => {
    setCurrentPage(p => p - 1);
  }, []);

  return (
    <div>
      <input
        placeholder="search"
        onChange={onChangeSearch}
        value={currentSearch}
        type="text" />
      {LIMITS.map(limit => (
        <button
          key={limit}
          className={currentLimit === limit ? 'selected' : null}
          type="button"
          onClick={changeLimit.bind(undefined, limit)}>
          {limit}
        </button>
      ))}
      {data.map(x => <ComponentForX key={x.id} {...x} />)}
      <button type="button" onClick={prevPage}>Previous page</button>
      <button type="button" onClick={nextPage}>Next page</button>
    </div>
  );
}; 
```

请注意，在我们的 setter 中，当状态 setter 改变时，我们不会重新绑定函数，这是因为当您将函数作为参数给出时，您正在使用一个非过时的引用。

# 陷阱

*   输入数组中没有足够的变量，这意味着它可能使用了过时的引用
*   在抽象上，组合几个钩子比做一个大的抽象要好
*   条件钩子或者循环中的钩子，需要的时候可以用数组操作符做条件，参见 useEffect，...

# 最佳实践

*   用 use 和 camel case 开始你的定制钩子，例子:useToggle，useReduxModule，...
*   讨论钩子的顺序，这样你就可以对你的组件有一个一致的概述

## ESLint 插件

如果没有一个很好的方法来帮助我们开发人员理解这些钩子的怪癖，它就不是 React 版本。

这就是为什么 React 团队制作了一个 [ESLint 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks)来帮助避免这些陷阱并执行最佳实践。

# 总结性的

钩子是一种很好的方式来改进我们所拥有的抽象，而不必总是制作包装组件/函数来注入抽象的逻辑。现在我们可以在功能组件的渲染中使用它们。

请注意，关于这个主题的[文档](https://reactjs.org/docs/hooks-intro.html)是顶级的，比这篇文章中涉及的更加深入和用例。

我认为在 React 世界里会有很多伟大的事情发生，这个新概念就是一个伟大的概念。