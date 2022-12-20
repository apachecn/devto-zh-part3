# React-可在 React 挂钩中加载

> 原文：<https://dev.to/ruhulamindev/react-loadable-in-new-react-hooks-3o5>

如果 react-loadable 是用钩子写的会怎么样(react 16.7 的新特性)？

这里有一个用钩子编写的 HOC 来异步加载 reactjs 组件

```
import React, { useEffect, useState } from "react";
import nprogress from "nprogress";
import "nprogress/nprogress.css";

export default (loader, loading) => props => {
  const [loadedComponent, setComponent] = useState(null);

  // this works like componentwillMount
  if (!nprogress.isStarted()) nprogress.start();

  if (loadedComponent) nprogress.done();

  useEffect(() => {
    let mounted = true;

    mounted &&
      loader().then(
        ({ default: C }) => mounted && setComponent(<C {...props} />)
      );

    // componentUnMount
    return () => (mounted = false);
  }, []);

  // return the loaded component
  const Component = loadedComponent || loading || <div>loading..</div>;
  return Component;
}; 
```