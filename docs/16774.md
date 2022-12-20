# 使用 RxJS 的 DIY Redux:第 2 部分

> 原文：<https://dev.to/onerzafer/diy-redux-with-rxjs-part-2-2lpg>

<figure>[![](img/131b498c317f1787c78d0c2f9e6b4a09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tqdchS1j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArBO29FpCIE8eeZ_hhBhVSA.png) 

<figcaption>照片由[史蒂夫·哈拉马](https://unsplash.com/photos/iVGevPcaJzk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

万一你没有读第一部分，我建议你读一读。第一部分介绍如何实现 Redux 的核心功能。

[![onerzafer image](img/59b8de6741785a8a79ffa3a423d1421e.png)](/onerzafer) [## 用 RxJS = RxDx

### ner za fer 1 月 17 日 195 分钟阅读

#redux #javascript #rxjs #react](/onerzafer/diy-redux-with-rxjs--rxdx-1oa1)

在 DIY Redux with RxJS => RxDx 第 2 部分，我将揭示一些最流行和广泛使用的 **Redux** 中间件的内部。正如我在文章的第一部分提到的——你可以在上面找到——我的目标是做一个实验，将 RxJS 和所有广泛使用的 Redux 中间件组合成一个库，可以在下面找到:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[oner zafer](https://github.com/onerzafer)/[rxdx](https://github.com/onerzafer/rxdx)

### 基于 rxjs 的 react 类 redux 库

<article class="markdown-body entry-content container-lg" itemprop="text">

# RxDx

RxDx 是一个基于 Rxjs 的状态管理库，它包含了所有必要的工具。

*重磅灵感来自各大库 [redux](https://github.com/reduxjs/redux) 、 [react-redux](https://github.com/reduxjs/react-redux) 、 [redux-observable](https://github.com/redux-observable/redux-observable) 和 [ngrx](https://github.com/ngrx/platform)*

## 动机

这个库背后的基本思想是将 RxJS 的全部功能带入 react 世界，使用一个熟悉的 Redux withiot 接口，依赖于一些中间件库。

## 装置

```
npm install rxdx --save
```

或者

```
yarn add rxdx
```

## 如何使用

### 商店

与 react-redux 不同，RxDx 不依赖于上下文，因此不需要在 App.js 上引入 Provider。但相反，它需要一个导出的存储才能运行。在这些示例代码中，建议使用 saperate store.js 来初始化存储，并在必要时对其进行改进。createStore 的界面很像 react-redux 界面。

```
//store.js
import {
  createStore,
  applyMiddleware,
  combineReducers,
  devToolsMiddleware,
  effectsMiddleware
} from "rxdx/lib/rxdx";
import { todoEffects
```

…</article>

[View on GitHub](https://github.com/onerzafer/rxdx)

在进入主题之前，回顾一下 redux 中间件所需的签名总是有好处的。

```
(storeAPI) => (next) => (action) => state 
```

在我开始之前，我需要承认，当我完成 createStore 函数时，我满怀信心地认为 redux 的所有可用中间件**都将适用于我的版本。实际上，它工作了，但不是对大多数来说，不是对广泛使用的来说:(所以我必须通过重新实现它们或者至少实现类似的来提供这些库/中间件。我们走吧。**

 *### 还原可观察的不同方法

正常情况下， **Redux** 不实现/无意实现 [TC39 Observable](https://github.com/tc39/proposal-observable) ，因此默认不兼容 RxJS。

这里我不得不提到可观测量的用例；如果您需要对事件流进行操作，那么使用可观察值可能更好。我说的是哪种手术？将两个流合并为一个流，过滤、映射或重定向一个流。更多技术细节请查看*[_ 此处*([https://rxjs-dev.firebaseapp.com/guide/observable](https://rxjs-dev.firebaseapp.com/guide/observable))。_

当实现一个 **Redux** store 时，你肯定会注意到你必须创建许多动作。我不得不承认，动作和事件非常相似，在运行时，它们会被触发数百次。所以这一定是 **redux-observable** 捕捉 **Redux** 作为流事件源的点。通过这样做，它为那些愿意在每个选择的动作上创建一些异步副作用的开发人员创造了一个好机会。关于 **redux-observable** 的详细文档，你可以在这里查看[。TL；神奇的事情发生在一个名为 **createEppicsMiddleware** 的函数中，它的签名如下:](https://redux-observable.js.org/) 

```
const createEpicsMiddleware = (rootEpic) => (storeAPI) => (next) => (action) => state 
```

老实说，当我查看文档时，我不喜欢我们使用这个库的方式。对我来说，NgRx 的 [**效果**](https://ngrx.io/guide/effects) 的用法更经典:)所以我决定不模仿 redux-observable 的 API，但功能应该在那里。所以我最后做了如下的事情。*