# React 的缺点是什么？

> 原文：<https://dev.to/stereobooster/what-are-the-weak-sides-of-react-2flh>

最近我偶然发现一个想法，专家应该知道他们工具的局限性。这不是一个新的想法，我听过很多次，但我想:

> 嗯，我是 React 开发者，所以我应该知道，对吗？

举例来说，我很容易说出何时使用 RDBMS (PostgreSQL ),何时使用键值存储(Redis ),何时使用图形数据库(Neo4j ),但是在回答 React 的这个问题之前，我停顿了一下。也许我在 React 方面没有丰富经验(所有的任务都是相似的)。

我的回答是:

*   简单的网站，没有太多的交互性/状态，比如登陆页面，不需要 React。它可以很容易地用 HTML、CSS 或者其他东西来构建，比如提高页面切换速度的 Turbolinks
*   高度互动的页面，需要使用 Canvas 或 WebGL 和 WASM，就像这个[https://perspective.finos.org/](https://perspective.finos.org/)。有一些 React 渲染器可以将 React 渲染到画布上。

**你什么时候会更喜欢别的反应？React 有哪些痛点？**

求~~好友~~ React 开发者。