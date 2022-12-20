# 反应钩释放！(最后🎉)

> 原文：<https://dev.to/li/react-hooks-released-finally--1mn3>

经过长时间的等待...

> ![Dan Abramov profile image](img/8410abe414a98a3109390b1a1f7377ee.png)丹阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)今天不为你钩23:00PM-2019 年 2 月 5 日[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1092920897143521287)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1092920897143521287)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1092920897143521287)

我们终于有反应用的钩子了！[https://reactjs.org/blog/2019/02/06/react-v16.8.0.html](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html)

上个月，我还创建了一个自定义钩子来使用滚动位置:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [尼奥](https://github.com/neo) / [反应-使用-滚动-位置](https://github.com/neo/react-use-scroll-position)

### 使用滚动位置的反应挂钩

<article class="markdown-body entry-content container-lg" itemprop="text">

# 反应-使用-滚动-位置

[![npm package](img/e8fb01894b37a58e421e13df7681a03f.png) ](https://www.npmjs.com/package/react-use-scroll-position) [ ![](img/5696d17570d8741b3eab533843b691c2.png)](https://camo.githubusercontent.com/caea9ff41579d75e8f63f493a5d7a6567cf27b1cb6974bb25cb2d85046c80736/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f74797065732f72656163742d7573652d7363726f6c6c2d706f736974696f6e2e737667)

A [反作用挂钩](https://reactjs.org/docs/hooks-intro.html)使用滚动位置。

## 使用

### 在反应功能组件中:

```
import React from 'react';
// Usually you would just need to import one of the following
import { useScrollPosition, useScrollXPosition, useScrollYPosition } from 'react-use-scroll-position';

function Example() {
  const { x, y } = useScrollPosition();
  const scrollX = useScrollXPosition();
  const scrollY = useScrollYPosition();
  return (
    <>
      <p>
        {x} should equal to {scrollX}.
      </p>
      <p>
        {y} should equal to {scrollY}.
      </p>
    </>
  );
}
```

Enter fullscreen mode Exit fullscreen mode

### 在自定义的 React 挂钩中

```
import { useScrollPosition } from 'react-use-scroll-position';
function useYourImagination() {
  const { x, y } = useScrollPosition();
  return getSomethingAwesomeWith(x, y
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/neo/react-use-scroll-position)