# React v16.6:懒，备忘录 y más

> 原文：<https://dev.to/sergiodxa/react-v166-lazy-memo-y-ms-15nk>

React v16.6 问世，带来了一些新的发展，其中包括通过一个名为`lazy`的新功能和另一个避免重复被称为`memo`的功能推出了挂起反应的第一部分。

> https://sdx.im/articles/react-v-16-6 的公共图书馆

## `React.memo`:Evitando doblerenders

此功能可让我们根据元件的`props`储存彩现，并避免在元件未变更时再次彩现。通过扩展“`PureComponent`”已经可以做到这一点，但这样做意味着创建一个具有相应性能开销的类，并使优化功能变得困难。

然后，此新函数将允许我们存储既创建了类又使用了函数的组件。甚至可以记住`React.lazy`的结果。

```
import React, { memo } from "react";
import logo from './logo.svg';

function Logo({ alt }) {
  return <img src={logo} className="App-logo" alt={alt} /> }

export default memo(Logo); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，我们以正常方式创建了该组件并将其传递给了`React.memo`，则该组件将返回我们可以导出的新存储组件。

此外，您可以传递第二个引数至“`React.memo`”以自订验证是否已变更 props 的方式，因为依预设，这会将所有 props 设定为 shallow equal。

```
export default memo(Logo, (prevProps, nextProps) => {
  return prevProps.alt === nextProps.alt;
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`Logo`只会在属性`alt`发生变更时，但如果其他属性发生变更，则会被忽略。这类似于使用“T6”`shouldComponentUpdate`生命周期方法，具有反向工作的特点，如果组件将产生相同的结果，则应返回`true`，如果组件产生不同的结果，则应返回`false`，这意味着我们的功能不应验证组件是否

> *注:*之所以叫`memo`是因为`memoize`，这种短格式是为了避免输入`memoize`这个词时常见的错误。它不是按照最初的设想命名的`pure`或命名为`PureComponent`，因为它不能保证组件的纯度(没有侧效果)，而只是记住了结果。

## `React.lazy`:代码拆分 con 悬疑

此新功能内置于 React 核心中，使您可以对 React 组件执行 split 和 lazy load 代码。使用诸如`react-loadable`或`next/dynamic`(来自 Next.js)等书店到目前为止是可以做到的事情。

此函数易于使用，它唯一的参数是异步函数，该函数返回导入 React 组件的承诺。您可以在此函数中添加更多逻辑。

```
import { lazy } from "react";
import sleep from "sleep";

const Logo = lazy(async () => {
  await sleep(1000);
  return import("./logo.js");
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，返回`lazy`的组件`import`将等待一秒钟，然后才从我们的组件`./logo.js`做`import`。`sleep`在这种情况下，允许我们假装缓慢的加载，以证明组件实际上是异步加载的。

`import`通过您使用的 bundler 模块工作，无论是 webpack、Parcel、Rollup 还是任何其他模块，它们都会创建一个使用此功能的拆分点，并确保在运行此功能时异步加载`./logo.js`模块。

> *注意*:这种方法**在服务器上仍然不起作用，今后的版本将提供。这意味着您不能将其与 Next.js 或 SSR 结合使用。**

### `React.Suspense`

此组件与“`lazy`”相关，如果使用 lazy load，则必须使用，如果不使用，React 将显示一个错误，表示需要使用。

`Suspense`所做的是简单的，将我们的 lazy 组件封装在另一个组件中，如果 lazy 组件尚未完成加载，则呈现回退。

```
import React, { Component, Suspense } from "react";
import LazyLogo from "./lazy-logo.js"; // nuestro componente lazy
import Placeholder from "./placeholder.js"; // un componente que sirva de placeholder

class App extends Component {
  state = {
    alt: "React"
  }

  render() {
    return (
      <Suspense maxDuration={300} fallback={<Placeholder />}>
        <LazyLogo alt={this.state.alt} />
      </Suspense>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当`App`渲染时，它会将其状态更改为`LazyLogo`，从而更改为`Logo`组件，而标志输入时`Suspense`将渲染我们通过属性`fallback`传递的组件`Placeholder`，则该组件既可以是通用的，也可以是微调器

最后，`maxDuration`属性允许我们指示在渲染回退之前应等待多少时间，`Suspense`这有助于我们确保如果某个组件加载足够快，我们不会渲染回退，并防止回退出现不到一秒钟。

> *注*:prop`maxDuration`仅在先前称为异步的反应并行模式启用时才起作用，目前该模式不稳定，因此`maxDuration`只是被忽略。

## `static contextType`:进入最容易的上下文

采用 React 16.3 介绍了使用上下文的稳定 API，使用`React.createContext`。

```
import { createContext } from "react";
export default createContext(); 
```

Enter fullscreen mode Exit fullscreen mode

此 API 虽然实用，但仅允许在组件的 render 方法中使用上下文。哪些组件是函数，不会引起问题，但在扩展“T0”或“`PureComponent`”的类中，会阻止它们在生命周期中使用。

从现在起，还有另一种在课堂上通过`static propTypes`使用上下文的方法。

```
import { Component } from "react";

import MyContext from "./context.js"; // el archivo que creamos antes

class MyComponent extends React.Component {
  static contextType = MyContext;
  componentDidMount() {
    const value = this.context;
    // hacer algo con el contexto acá
  }
  componentDidUpdate() {
    const value = this.context;
    // hacer algo con el contexto
  }
  componentWillUnmount() {
    const value = this.context;
    // hacer algo con el contexto
  }
  render() {
    const value = this.context;
    // user el contexto para hacer render
  }
}

export default MyComponent; 
```

Enter fullscreen mode Exit fullscreen mode

如我们所见，通过传递返回的上下文“`React.createContext`”即可在组件生命周期中的任何位置开始使用它。

> *注意*:使用此方法一次只能订阅一个上下文，要使用多个上下文，应该将我们的类组件包含在使用当前 API 传递上下文作为 prop 的函数中。

## `static getDerivedStateFromError()`:渲染前应对错误

React v16 中还引入了一种使用生命周期方法`componentDidCatch`捕获渲染中发生的错误的方法。此方法会在彩现程式掷回错误时执行，并可让我们更新 UI 中的「以某种方式回应错误」状态。

在更改“反应”状态之前，默认情况下会渲染“`null`”，在某些情况下，如果父零部件不希望丢失任何参照，则可能会打断失败的父零部件。在服务器上渲染时，此方法也不起作用，因为所有名为“`Did`”的方法都仅在浏览器中运行。

从现在起，可以使用新的静态方法`getDerivedStateFromError()`在渲染前获取误差。

> *注意*:这种方法**在服务器上仍然不起作用，今后的版本将提供。这意味着您不能将其与 Next.js 或 SSR 结合使用。**

```
class ErrorBoundary extends React.Component {
  state = {
    hasError: false
  };

  static getDerivedStateFromError(error) {
    // retorna los nuevos cambios al estado
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      // Renderizamos algo en lugar del contenido si hay un error
      return <h1>Something went wrong.</h1>;
    }
    // renderizamos nuestro contenido
    return this.props.children; 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用方法与使用`componentDidCatch`相同，使用`ErrorBoundary`封装元件，其子元件中发生的任何错误都会被`getDerivedStateFromError`抓住，使我们能够对这个错误作出反应。

## 新广告

16.3 版引入了一种严格的反应模式，可以将我们的应用程序与[组件](https://reactjs.org/docs/strict-mode.html)结合使用。

此新版本包括新的功能警告，这些警告在未来将会过时。

*   `ReactDOM.findDOMNode()`。此 API 将来会被删除，如果您从未使用过它，则可以忽略；如果您使用过它，文档中会有一个“T1”指南解释如何更新“”。
*   **前后关联 API**使用`contextTypes`和`getChildContext`。前后关联 API 的反应会比预期的慢且重。建议升级到新 API，以便将来不再支持旧 API。

## 结语

正如您所看到的，这一新版本为 React 生态系统带来了许多有趣的东西，其中大部分是通过外部书店解决的，现在只有 React 能够做到这一点。

不管我们是避免使用`memo`在功能组件中变得不必要，还是使用`lazy`一点一点地反应异步加载，它都给我们提供了越来越多的工具，使开发人员能够更轻松地创建更好的用户体验。

最后，如果你想看一下`lazy`和`memo`的工作原理，你可以在[https://reac-lazy-memo . now . sh](https://react-lazy-memo.now.sh)和[https://github . com/sergiodxa/reac-lazy-memo](https://github.com/sergiodxa/react-lazy-memo)中看到演示。