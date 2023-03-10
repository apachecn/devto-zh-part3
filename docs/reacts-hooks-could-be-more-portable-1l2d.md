# React 的挂钩可以更加轻便

> 原文：<https://dev.to/colingourlay/reacts-hooks-could-be-more-portable-1l2d>

我已经花了一段时间来掌握 React 的新功能，我绝对喜欢它。我看不出自己会很快重构我的旧的基于类的项目，但是对于未来的项目，我会全力以赴。

我昨天在 useEffect 上读了丹·阿布拉莫夫的[优秀深潜](https://overreacted.io/a-complete-guide-to-useeffect/)，但是今天早上我醒来时脑子里痒痒的，就写了一个叫做`portable-hooks`的小库来挠它。

在我解释它做什么之前，我们先来看一些代码:

```
import React from 'react';
import { useEffect } from 'portable-hooks';

function App({ text }) {
  useEffect(() => {
    document.title = text;
  }, [text]);

  return <h1>{text}</h1>;
} 
```

### *“等等，React 的`useEffect`钩子已经是这么用了！”*

是的，但是如果你想把效果函数移到组件之外，这样你就可以在其他地方使用它了。React 现有的`useEffect`钩子利用组件函数闭包来使用当前的 props &状态。这有效地将效果函数限制在组件内部。如果你想提取设置`document.title`的效果，你必须这样做:

```
import React, { useEffect } from 'react';

function setDocumentTitle(title) {
  document.title = title;
}

function App({ text }) {
  useEffect(() => setDocumentTitle(text), [text]);

  return <h1>{text}</h1>;
} 
```

注意，如果你正确地管理依赖关系，你必须在两个地方写`text`:

1.  作为`setDocumentTitle`的参数，并且
2.  在依赖关系数组中(`useEffect`的第二个参数)

我们为什么要这么做？函数参数*本质上是*依赖关系。

React 要求我们在每次使用这些基于依赖关系的钩子时写出两次这些参数，如果我们想避免错误的话。只把它们写在一个地方不是更简洁吗:

```
import React from 'react';
import { useEffect } from 'portable-hooks';

function setDocumentTitle(title) {
  document.title = title;
}

function App({ text }) {
  useEffect(setDocumentTitle, [text]);

  return <h1>{text}</h1>;
} 
```

### *“这是怎么回事？”*

`portable-hooks`包提供了 React 自己钩子的包装版本，这些钩子以依赖关系作为参数来调用函数。我不知道你怎么想，但我觉得这很优雅。现在，你的函数签名和你的依赖是完全一样的东西，你不太可能遇到错误。

### 这让我们可以做很酷的事情...比如“特效道具”

通过传入效果来定制组件不是很好吗:

```
import axios from 'axios';
import React, { useState } from 'react';
import ReactDOM from 'react-dom';
import { useEffect } from 'portable-hooks';

function App({dataURL, fetchData}) {
  const [data, setData] = useState(null);

  useEffect(fetchData, [dataURL, setData]);

  return <div>{data ? <div>{
    /* use `data` for something here */
  }</div> : 'Loading...'}</div>;
}

async function fetchDataUsingAxios(url, setData) {
  const result = await axios(url);

  setData(result.data);
}

ReactDOM.render(<App
  dataURL="https://..."
  fetchData={fetchDataUsingAxios} />, document.body); 
```

现在你有了一个组件，它期望它的`fetchData`属性是一个匹配特定签名的函数，但是你可以在**中以任何你想要的方式**实现那个函数。

### **咳咳*“对不起，但有时我想对`useEffect`撒谎说什么变了”*

看，[对依赖关系撒谎是一个坏主意](https://overreacted.io/a-complete-guide-to-useeffect/#dont-lie-to-react-about-dependencies)，并且`portable-hooks`非常鼓励你(通过设计)不要对依赖关系撒谎，但是在极少数情况下，它实际上是有用的。别担心，我会掩护你的。

`portable-hooks`中的每个钩子与 React 版本的不同之处在于关心一个额外的可选参数。如果您设置了它，React 的钩子将使用它作为它的依赖列表，并且原始的输入仍然会被传递到您的函数中。

这里有一个(非常人为的)例子，它会从组件挂载到卸载的时候一直向控制台发送垃圾邮件，不管它更新了多少次:

```
import React, { useState } from 'react';
import ReactDOM from 'react-dom';
import { useEffect } from 'portable-hooks';

function logMountDuration(x) {
  let seconds = 0;

  const id = setInterval(() => {
    seconds++;
    console.log(`"${x}" was mounted ${seconds} seconds ago`);
  }, 1000);

  return () => clearInterval(id);
}

function App({ text }) {
  const [count, setCount] = useState(0);

  useEffect(logMountDuration, [text], []);

  return (
    <div>
      <h1>{text}</h1>
      <button onClick={() => setCount(count + 1)}>
        {`I've been pressed `}
        {count}
        {` times`}
      </button>
    </div>
  );
}

ReactDOM.render(<App text="Example" />, document.body);

// > "Example" was mounted 1 seconds ago
// > "Example" was mounted 2 seconds ago
// > "Example" was mounted 3 seconds ago
// ... 
```

## API

`portable-hooks`导出以下钩子(它们都关心依赖关系):

*   `useCallback`
*   `useEffect`
*   `useImperativeHandle`
*   `useLayoutEffect`
*   `useMemo`

正如前面所解释的，它们都是 React 自己的钩子的包装器，并且公开相同的 API(对于那些你想隐瞒依赖关系的情况有一个额外的可选参数)，所以你可以互换使用它们。

这意味着您现有的所有匿名无参数代码已经兼容，您可以通过更新您的导入来开始重构:

```
import React, { useEffect } from 'react';

// ...becomes...

import React from 'react';
import { useEffect } from 'portable-hooks'; 
```

请让我知道你的想法。你可以在 GitHub 上查看`portable-hooks`或者`npm install portable-hooks`给他们试试。感谢阅读！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [科林古雷](https://github.com/colingourlay) / [便携钩](https://github.com/colingourlay/portable-hooks)

### React 钩子的包装器，使它们更便于携带

<article class="markdown-body entry-content container-lg" itemprop="text">

# 便携式挂钩

React 钩子的包装器，使它们更便于携带

[阅读关于 dev.to](https://dev.to/colingourlay/reacts-hooks-could-be-more-portable-1l2d) 的介绍文章

```
$ npm i portable-hooks
```

## 使用

```
import React from 'react';
import { useEffect } from 'portable-hooks';
function App({ text }) {
  useEffect(() => {
    document.title = text;
  }, [text]);
  return <h1>{text}</h1>;
}
```

### *“等等，React 的`useEffect`已经这样工作了！”*

是的，但是如果你想把效果函数移到组件之外，这样你就可以在其他地方使用它了。React 现有的`useEffect`钩子利用组件函数闭包来使用当前的道具&状态。这有效地将效果函数限制在组件内部。如果你想提取设置`document.title`的效果，你必须这样做:

```
import React, { useEffect } from 'react';
function
```

…</article>

[View on GitHub](https://github.com/colingourlay/portable-hooks)