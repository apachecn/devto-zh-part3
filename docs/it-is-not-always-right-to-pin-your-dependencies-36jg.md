# 固定依赖关系并不总是正确的

> 原文：<https://dev.to/lucifer1004/it-is-not-always-right-to-pin-your-dependencies-36jg>

> TL；DR；如果你正在编写一个库/包，确保在固定所有依赖之前及时更新你的依赖。否则，可能会导致问题。

您可能已经使用了类似于 [`renovate`](https://github.com/renovatebot/renovate) 的工具来管理您的 GitHub 库的依赖关系。它要做的第一件事是固定你的依赖关系。

固定依赖关系可以:

*   避免由上游包/库更新引起的潜在错误。因为上游包可能没有固定它们的依赖关系，所以这样的错误仍然是可能的。)

*   帮助您的合作者或对您的项目感兴趣的人复制您的开发环境。(*如果再加上 docker 就更甜了。*)

然而，它有时也会引起问题。我将和你分享我最近的一次经历。

我正在做一个谷歌地图项目，我有两个仓库。(我尝试过`lerna`，但未能让一切顺利进行，不得不放弃。)

一个是围绕谷歌地图 API 的 React 包装器:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[lucifer 1004](https://github.com/lucifer1004)/[react-Google-map](https://github.com/lucifer1004/react-google-map)

### React 项目更容易的谷歌地图集成。

<article class="markdown-body entry-content container-lg" itemprop="text">

# React 谷歌地图

[![version](img/fce6558b984fd7a6bff8b9a3eceb2435.png)](https://www.npmjs.com/package/@lucifer1004/react-google-map)[![License: MIT](img/703c1a9bcacb5b6337daa880686dcc21.png)](https://opensource.org/licenses/MIT)[![codecov](img/b9c0c1b9a5fec85854bd728a3196df50.png)](https://codecov.io/gh/lucifer1004/react-google-map)[![codebeat badge](img/c7b9967f73cbdfff36f188bb8a91cf56.png)T11】](https://codebeat.co/projects/github-com-lucifer1004-react-google-map-master)

React 项目更容易的谷歌地图集成。

> ## 注意
> 
> 这个项目已经移到了 [@googlemap-react](https://github.com/googlemap-react/googlemap-react) 。

[阅读文档](https://react-google-map.gabriel-wu.com)

## 为什么是新包装

已经有了类似的包，如[tomchentw/react-Google-maps](https://github.com/tomchentw/react-google-maps)[Google-map-react/Google-map-react](https://github.com/google-map-react/google-map-react)[fullstackreact/Google-maps-react](https://github.com/fullstackreact/google-maps-react)那么为什么还要写一个新的库呢？

其目的是为 React 用户提供一个更容易使用的谷歌地图库，通过`React`的最新功能(`React >= 16.8.0`是必需的)和`TypeScript`来实现。

## 有什么不同

*   组件位置是自由的(通常)
*   直接访问谷歌地图对象
*   更加统一的 API
*   类型安全

## 用法示例

### 先决条件

*   npm 或纱线

```
yarn add @lucifer1004
/react-google-map
# Or you can use
npm install --save @lucifer1004
/react-google-map
```

*   一个有效的 Google Map API 键(替换下面代码片段中的占位符)

```
import {
  GoogleMapProvider,
  HeatMap,
  InfoWindow,
  MapBox,
  Marker,
  Polygon,
} from '[@lucifer1004](https://dev.to/lucifer1004)
/react-google-map'
// In your component
return (
```

…</article>

[View on GitHub](https://github.com/lucifer1004/react-google-map)

另一个是应用程序:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ lucifer1004 ](https://github.com/lucifer1004) / [抵制](https://github.com/lucifer1004/boycott)

### 一款地图应用。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 抵制

这是一个 Udacity 项目。它被静态地部署在[这里](https://boycott.gabriel-wu.com) via Now。

## 在本地运行它

```
git clone https://github.com/lucifer1004/boycott
cd boycott
yarn install
yarn start
```

然后您可以在`localhost:3000`访问它

## 特征

*   使用 Yelp Fusion API 搜索地点(`cors-anywhere`用于解决 CORS 问题)
*   过滤器选项:全部/开放/高评级/低价格
*   谷歌地图 API 的使用是通过 [`@googlemap-react/core`](https://github.com/lucifer1004/react-google-map) ，这是我自己写的一个谷歌地图的 React 包装器。

</article>

[View on GitHub](https://github.com/lucifer1004/boycott)

对于这两个存储库，依赖项是固定的。一天，令我惊讶的是，我所有的反应钩都失灵了。此类错误消息不断出现:

> React 钩子错误:钩子只能在函数组件体内部调用...

他们根本没有提供任何有用的信息！我在函数组件体内调用钩子。

经过一番艰难的寻找，我终于找到了一些鼓舞人心的东西。**当 React 版本不一致时，会出现此错误。**然后我检查了库回购和应用回购的`package.json`，发现库在使用`React 16.8.1`，而应用在使用`React 16.8.2`。我很快更新了我的库来使用`React 16.8.2`，然后错误就消失了。

现在，我把`react`和`react-dom`移到了`peerDependencies`，把版本限制放宽到了`^16.8.2`。我认为这足以防止类似的问题。

我从这次经历中学到的是，好的东西(固定依赖)并不总是对的。看情况！依赖固定对于应用程序来说非常好。然而，在编写库/包时，您应该更加小心。