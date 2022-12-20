# React Hooks 尽可能简单地解释道

> 原文：<https://dev.to/phocks/react-hooks-explained-as-simply-as-i-humanly-can-1ggi>

钩子可以让你用函数组件做更多的事情。

* * *

您可以通过`useState()`钩子使用 state。

`const [count, setCount] = useState(0)`例。

现在`count`等于`0`。

用`setCount(1)`更新`count`。

现在`count`等于`1`，组件将会更新。

* * *

你也可以用`useEffect()`钩子触发副作用。

`useEffect(() => console.log(count), [count])`例。

现在它会在每次`count`更新时记录控制台日志。

*提示:使用`[]`仅在组件被挂载时触发一次副作用，并且在任何状态改变后都不使用第二个参数来触发效果。*

提示 2:如果你从副作用中返回一个函数，它将在组件卸载后运行该函数。

* * *

下面是一些代码。

```
import React, {useState, useEffect} from "react";

function Counter(props) {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = count;
  }, [count]);

  function countHigher() {
    setCount(count + 1);
  }

  return (
    <p onClick={countHigher}>
      {count}
    </p>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

差不多就是这样。

如果你想要更深入的了解[，请阅读文档](https://reactjs.org/docs/hooks-intro.html)。