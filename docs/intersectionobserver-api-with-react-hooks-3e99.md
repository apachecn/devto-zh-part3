# 带有 React 挂钩的 IntersectionObserver API

> 原文：<https://dev.to/billyjacoby/intersectionobserver-api-with-react-hooks-3e99>

# 带 React 挂钩的 IntersectionObserver API

## 一个简单的演示和教程，展示了如何使用 intersection observer API 和 React 钩子

你可以在这里查看我对这个项目[的回购。](https://github.com/billyjacoby/intersection-observer-hooks)

下面是我们将要做的事情的简要概述:

*   创建-反应-应用
*   初始项目设置
    *   交叉点-观察点聚合填充
    *   添加元素，更新 CSS
*   写钩
    *   初始化状态
    *   构造 IntersectionObserver 实例
    *   确保只观察元素相交一次
*   通过控制台展示钩子的动作

如果你想看看它的实际效果，请点击这里查看演示！
(一定要打开开发者控制台)

我们将在一个简单的`create-react-app`骨骼上演示这一过程。

我们要做的第一件事是跑:

`create-react-app intersection-observer-hooks`

`cd intersection-observer-hooks`

初始化之后，我们将为 API 安装 polyfill，以确保对所有浏览器的支持。

`yarn add intersection-observer`

接下来，我们将添加一些元素到我们的应用程序中，并调整 CSS 以使我们能够看到一切是如何工作的

**app . js**T2】

```
...
<header className="App-header">
      <img src={logo} className="App-logo" alt="logo" />
      <p>
        Edit <code>src/App.js</code> and save to reload.
      </p>
      <a
        className="App-link"
        href="https://reactjs.org"
        target="_blank"
        rel="noopener noreferrer"
      >
        Learn React
      </a>
    </header> <div className="full-height one">
      <div className="item-one" />
    </div>
    <div className="full-height two">
      <div className="item-two" ref={elementRef}>
        {inView && <p>I'm in view!</p>}
      </div>
    </div>
    ... 
```

Enter fullscreen mode Exit fullscreen mode

**App.css**

```
... .full-height {
  height: 100vh;
  border: white dotted 1px;
  margin: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

.one {
  background-color: #61dafb;
  color: #282c34;
}

.two {
  background-color: #282c34;
  color: #61dafb;
}

.item-one {
  background-color: #282c34;
  color: #61dafb;
  height: 30%;
  width: 30%;
}

.item-two {
  color: #282c34;
  background-color: #61dafb;
  height: 30%;
  width: 30%;
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将在一个名为`useIntersectionObserver.js`的单独文件中创建我们的钩子

在这个文件中，我们要做的第一件事是配置我们的钩子来获取必要的参数，配置我们的状态，并输出我们想要看到的信息。

**useintersectionobserver . js**

```
import { useState, useEffect } from "react";

export const useIntersectionObserver = (
  ref,
  { threshold, root, rootMargin }
) => {
  // configure the state
  const [state, setState] = useState({
    inView: false,
    triggered: false,
    entry: undefined
  });

  return [state.inView, state.entry];
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个钩子将引用 DOM 节点，以及您希望传递给 IntersectionObserver 对象的选项；阈值、根和根边距。有关这些选项的更多信息，您可以在这里查看 API [上的 MDN 文档。](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver/IntersectionObserver)

现在，我们将配置 IntersectionObserver 对象，并编写回调函数，以便在 DOM 节点的 intersectionRation 大于 0 时更新我们的状态。

**useintersectionobserver . js**

```
...
const [state, setState] = useState({
    inView: false,
    triggered: false,
    entry: undefined
  });

  const observer = new IntersectionObserver(
    (entries, observerInstance) => {
      // checks to see if the element is intersecting
      if (entries[0].intersectionRatio > 0) {
        // if it is update the state, we set triggered as to not re-observe the element
        setState({
          inView: true,
          triggered: true,
          entry: observerInstance
        });
        // unobserve the element
        observerInstance.unobserve(ref.current);
      }
      return;
    },
    {
      threshold: threshold || 0,
      root: root || null,
      rootMargin: rootMargin || "0%"
    }
  );
... 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将使用 React 的 useEffect 钩子来确保 DOM 节点引用存在，并确保 inView 状态没有被触发为 true。这将完成我们的钩子，完成的结果应该如下所示:

**useintersectionobserver . js**

```
import { useState, useEffect } from "react";

export const useIntersectionObserver = (
  ref,
  { threshold, root, rootMargin }
) => {
  // configure the state
  const [state, setState] = useState({
    inView: false,
    triggered: false,
    entry: undefined
  });

  const observer = new IntersectionObserver(
    (entries, observerInstance) => {
      // checks to see if the element is intersecting
      if (entries[0].intersectionRatio > 0) {
        // if it is update the state, we set triggered as to not re-observe the element
        setState({
          inView: true,
          triggered: true,
          entry: observerInstance
        });
        // unobserve the element
        observerInstance.unobserve(ref.current);
      }
      return;
    },
    {
      threshold: threshold || 0,
      root: root || null,
      rootMargin: rootMargin || "0%"
    }
  );

  useEffect(() => {
    // check that the element exists, and has not already been triggered
    if (ref.current && !state.triggered) {
      observer.observe(ref.current);
    }
  });

  return [state.inView, state.entry];
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经写好了钩子，是时候把它导入到我们的应用程序中，看看它是否工作。

**app . js**T2】

```
...
import { useIntersectionObserver } from "./useIntersectionObserver";

function App() {
  // Create the ref to our element
  const elementRef = useRef(null);
  const [inView, entry] = useIntersectionObserver(elementRef, {
    threshold: 0
  });

  // console.log our state everytime its updated to see if it works.
  useEffect(() => {
    console.log(inView);
  }, [inView]);

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
      <div className="full-height one">
        <div className="item-one" />
      </div>
      <div className="full-height two">
        <div className="item-two" ref={elementRef}>
          {inView && <p>I'm in view!</p>}
        </div>
      </div>
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

一旦这些都连接好了，运行`yarn start`并打开你的开发者控制台。当应用程序第一次加载时，您应该看到状态首先是 false，然后当我们滚动到选定的 div 时，状态变为 true！

感谢您的阅读，如果您喜欢这篇教程，请务必告诉我！

不久我将发布另一个演示如何使用 GSAP 将元素动画化到屏幕上。