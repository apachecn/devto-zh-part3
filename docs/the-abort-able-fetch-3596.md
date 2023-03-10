# 可中止的提取

> 原文：<https://dev.to/mzubairahmed/the-abort-able-fetch-3596>

Fetch API 已经走了很长一段路，成为一个更加成熟、稳定和支持 Web API。我们在应用程序中广泛使用它。然而，我们没有使用它的可中止功能，即中途停止或终止请求。很久以前，2̶0̶1̶5̶就提出了对可中止提取的支持。然后在 2017 年，它终于被纳入标准，Edge 率先支持它。现在大多数浏览器都支持 T1。

### 聚填

为了支持其他浏览器，我们使用了`whatwg-fetch`，这是一个实现获取 whatwg [规范](https://fetch.spec.whatwg.org/)的 polyfill。除此之外，我们还必须了解浏览器是否支持可中止功能；如果不是，那么我们使用多边形填充提取。

```
import { fetch as fetchPloyfilled } from 'whatwg-fetch';

const isAbortableFetchSupported = 'signal' in new Request('')

const _fetch = isAbortableFetchSupported ? window.fetch : fetchPloyfilled;

// _fetch is then passed down via props to be used in app 
```

### 准备中止

使用 DOM 标准提供的新 API 中止承诺。我们创建了一个新的`AbortController`实例。从这个控制器中，我们使用`AbortController.signal`属性获取对`AbortSignal`对象的引用。然后这个`AbortSignal`可以被传递以获取它的选项。

```
const abortController = new AbortController();
const abortSignal = abortController.signal

..
...

// later in fetch

await this.props.fetch(requestURL, {  
    method: 'GET',
    signal: abortSignal
    }) 
```

### 流产

```
abortController.abort(); 
```

调用上面的方法将停止您的 fetch 承诺并抛出一个错误，您需要捕捉并正确处理这个错误。

```
(err.name === 'AbortError’) {
console.error(’fetch safely aborted for quick stats’, err);
} 
```

### 到底为什么要流产？

您是否熟悉“警告:无法在未安装的组件上调用 setState(或 forceUpdate)”。这是不行的”。如果您的状态依赖于网络调用的后续效果，并且进行调用的上下文丢失，通常会发生这种情况。简而言之:你去了一个页面，这个页面启动了一个网络请求来获取一个列表。然后，您很快导航到另一个页面。可能有一个状态负责获取列表和更新 UI。但是现在，由于您已经离开了，setState 仍然在现在已经卸载的前一个页面上被调用。因此有了这个警告。小心处理它很重要，因为它会导致内存泄漏和不确定性。

> 我们中止组件 WillMount 中的所有网络请求

这样，我们可以确保在获取请求被中止时不会调用 setStates。