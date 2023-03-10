# 关于反应悬念和并发模式

> 原文：<https://dev.to/pomber/about-react-suspense-and-concurrent-mode-21aj>

在 [React 路线图](https://reactjs.org/blog/2018/11/27/react-16-roadmap.html)上的下一件大事是**并发模式**和**悬念**。它们相互关联，相辅相成，所以人们有时会把它们搞混。但是它们代表了非常不同的概念。

## 并发模式

[![Signs for priority and economy lanes](img/5698706f0366e407e8a1a6e0ae70b40c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vuDQFjsh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wut45acjo0igre3q2ark.jpg)

为了理解并发模式，考虑一下**优先级**。

如果没有并发模式，当 React 开始渲染时，它会一直渲染，直到完成。

在并发模式下，React 将关注其他需要完成的事情，如果有更高优先级的事情，它将暂停正在渲染的内容，让其他事情先完成。“其他事情”可能是:

*   浏览器需要做的事情
*   React 需要呈现的另一个更新
*   来自其他库或应用程序代码的任何其他任务

```
import {
  useState,
  takeYourTimeToRenderThisUpdate,
  iNeedThisUpdateAsSoonAsPossible
} from "fictitious-react";

function SlowButLowPriorityComponent() {
  const [someData, changeData] = useState(0);
  return (
    <div>
      <BigComponentThatTakesVeryLongToRender someProp={someData} />
      <button
        onClick={() => {
          takeYourTimeToRenderThisUpdate(() =>
            changeData(prevData => prevData + 1)
          );
        }}
      >
        Expensive but low priority change
      </button>
    </div>
  );
}

function FastAndHighPriorityComponent() {
  const [someData, changeData] = useState(0);
  return (
    <div>
      <SmallComponentThatRendersFast someProp={someData} />
      <button
        onClick={() => {
          iNeedThisUpdateAsSoonAsPossible(() =>
            changeData(prevData => prevData + 1)
          );
        }}
      >
        Fast and high priority change
      </button>
    </div>
  );
}

function App() {
  return (
    <div>
      <SlowButLowPriorityComponent />
      <FastAndHighPriorityComponent />
    </div>
  );
}

// If the user clicks first the SlowButLowPriorityComponent button
// and then the FastAndHighPriorityComponent button
// React will stop rendering SlowButLowPriorityComponent
// and finish rendering FastAndHighPriorityComponent (with its new state) first
// only then it will continue with the SlowButLowPriorityComponent update 
```

你不需要为每次更新明确设置优先级，如果你没有反应，将尝试猜测正确的。

## 悬念

[![Flight information display](img/4d7c3d0d084977c6a3507322e37c57ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r6U8OYxB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wb4r2b7tzipyr1h2xs3x.jpg)

为了悬念，想想**等待**。

毫无疑问，如果您的组件需要等待任何依赖项(例如，如果它依赖于需要从服务器获取的一些数据)，您需要添加一些状态来跟踪挂起的依赖项，在依赖项挂起时呈现一些内容，并在依赖项就绪时更新状态。

有了悬念，你的组件将能够告诉 React“嘿 React，我没有所有需要渲染的东西，但我会让你知道你什么时候可以再次尝试渲染我”。您的组件不需要保存额外的状态，也不需要在等待时决定呈现什么。

```
import {
  dontRenderMeUntilThisIsReady,
  Suspense as TryRenderTheseChildren
} from "fictitious-react";
import getMyDependency from "fictitious-dependency-fetcher";

function ComponentThatDependsOnSomething(props) {
  const dependency = dontRenderMeUntilThisIsReady(
    getMyDependency(props.dependencyId)
  );
  return <h1>{dependency.data}</h1>;
}

function App(props) {
  return (
    <TryRenderTheseChildren andIfTheyAreNotReadyRenderThis={<ImTheFallback />}>
      <ComponentThatDependsOnSomething dependencyId={1} />
      <ComponentThatDependsOnSomething dependencyId={2} />
    </TryRenderTheseChildren>
  );
} 
```

#### 和现在的情况完全不同

[![Escher drawings at the airport](img/a600d03ca9743506843f766c6451ad65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2fjeVSXl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ap068atnrlknph89npq.jpg)

我在阿姆斯特丹机场，在#ReactAmsterdam 之后，等待我延误的航班，看着埃舍尔的这些画，写了一个帖子，打个比方，我在等待我延误的航班时写了一个帖子。