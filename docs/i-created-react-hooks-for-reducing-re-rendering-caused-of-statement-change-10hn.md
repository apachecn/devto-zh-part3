# 我创建了 react 钩子来减少语句变化引起的重新渲染。

> 原文：<https://dev.to/wonism/i-created-react-hooks-for-reducing-re-rendering-caused-of-statement-change-10hn>

在 React 中，当`state`改变时，组件将被重新渲染。
如果状态变化非常频繁会怎么样？
组件会被频繁地重新渲染。这会影响到性能。

`use-flush`将帮助组件在`n seconds`中重新渲染一次。

现在，让我们来看看它的工作原理和使用方法。

## 解释

我们假设状态会像下面这样非常频繁地改变。

*   `+`表示每隔 **n 秒**。
*   `*`表示状态被**改变了**。

```
+--------+--------+--------+--------
  * *   *  ** *** *  *    * 
```

每隔 n 秒**就会改变一次**刷新状态**，如下图。** 

```
+--------+--------+--------+--------
         *(3)     *(6)     *(2) 
```

## 安装

```
$ npm i -S use-flush 
```

## 用法

如果 2 秒内点击 5 次，`flushedCount`就会是`[0, 1, 2, 3, 4]`。

```
import React, { useState } from 'react';
import { render } from 'react-dom';
import useFlush from 'use-flush';

const appRoot = document.getElementById('app');

const App = () => {
  const [count, setCount] = useState(0);
  const flushedCount = useFlush(count, 2000);

  return (
    <>
      <p>
        FLUSHED COUNT : {flushedCount.join(', ')}
      </p>
      <button onClick={() => { setCount(count + 1); }}>
        CLICK!
      </button>
    </>
  );
};

render(
  <App />,
  appRoot
); 
```

你可以在这个[库](https://github.com/wonism/use-flush)上查看。
欢迎任何公关。:)