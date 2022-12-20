# 用 JSX 构建 dom 元素

> 原文：<https://dev.to/nickytonline/dom-chef---build-dom-elements-with-jsx-5fi>

我在为将改进的 GitHub 迁移到 TypeScript 的 PR 工作时遇到了 dom-chef。

乍一看，我以为 Refined GitHub 是用 React 构建的，但当我喝第二口咖啡时，我意识到它只是 JS 加了一些 JSX。

[![](img/636213047307eb26b76db1c6e2d92792.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qMcmiuLz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media3.giphy.com/media/2JeyC2DvEhdRu/giphy.gif%3Fcid%3D19f5b51a5c562926446e66326327f7e5)

TLDR:

*   没有 API，JSX 自动转换成实际的 DOM 元素
*   预防 XSS 注射
*   部分 SVG 支持
*   类似 React 的道具命名(包括事件)
*   在里面混合任何 DOM 元素

如果你是 JSX 的粉丝，这肯定很有趣。

签出存储库

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[【vadim demedes】](https://github.com/vadimdemedes)/[【唐老大】](https://github.com/vadimdemedes/dom-chef)

### 🍔使用 JSX 自动构建 DOM 元素

<article class="markdown-body entry-content container-lg" itemprop="text">

# 
[![](img/7ffa0c62cb547aae9f579f36f62d14ef.png)](https://github.com/vadimdemedes/dom-chefmedia/logo.png)

T6】

> 使用 JSX 构建常规 DOM 元素

有了`dom-chef`，你可以使用 Babel 或 TypeScript 将 [JSX](https://reactjs.org/docs/introducing-jsx.html) 转换成普通的旧 DOM 元素，而不用使用不安全的`innerHTML`或笨拙的`document.createElement`调用。

它支持您对 JSX 的所有期望，包括:

*   [SVG elements](https://github.com/vadimdemedes/dom-chef#render-svg)
*   [事件监听器](https://github.com/vadimdemedes/dom-chef#inline-event-listeners)
*   [内联 CSS](https://github.com/vadimdemedes/dom-chef#inline-styles)
*   [嵌套元素](https://github.com/vadimdemedes/dom-chef#nested-elements)
*   [功能元件](https://github.com/vadimdemedes/dom-chef#use-functions)

如果某些东西不被支持(或者不像在 React 中那样有效),请提出问题！

## 安装

```
$ npm install dom-chef 
```

## 使用

确保使用 JSX transpiler(例如 [Babel](https://github.com/vadimdemedes/dom-chef#babel) 、 [TypeScript 编译器](https://github.com/vadimdemedes/dom-chef#typescript-compiler)、 [esbuild](https://esbuild.github.io/content-types/#using-jsx-without-react) ，你只需要其中一个)。

```
import {h} from 'dom-chef';
const handleClick = e => {
    // <button> was clicked
};

const el = (
    <div className="header">
        <button className="btn-link" onClick={handleClick}>
            Download
        </button>
    </div>
);

document.body.
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/vadimdemedes/dom-chef)

照片由[威龙 A](https://unsplash.com/photos/Lhb1DyyNr7U?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/chef?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄