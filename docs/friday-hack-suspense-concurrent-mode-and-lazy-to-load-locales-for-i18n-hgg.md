# 星期五黑客:悬念，并发模式和延迟加载 i18n 的语言环境

> 原文：<https://dev.to/stereobooster/friday-hack-suspense-concurrent-mode-and-lazy-to-load-locales-for-i18n-hgg>

我有一个关于临桂的小编帖子。我实现了所有与 i18n 相关的特性。我想添加预渲染来提高加载性能，但这看起来并不像想象的那么简单。我不得不“黑掉”`Suspense`、`ConcurrentMode`和`React.lazy`。

正如我所说，这是一个黑客，这是为了好玩。不要在生产中使用这些代码，除非你知道你在做什么。

完整源代码在[这里](https://github.com/stereobooster/react-lingui-example)

## 上一集

我们就此打住: [i18n 与 Lingui.js #3](https://dev.to/stereobooster/i18n-of-react-with-linguijs-3-5h6g) 的反应。我将它部署到 Github pages，并用 [webpagetest](https://www.webpagetest.org) 测量负载性能(来自:弗吉尼亚州杜勒斯- Moto G4 - Chrome - 3G)。

[![filmstrip 1](img/5a63d2f4676d707c8721746692db2cbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Td6qX9BM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x7t42yggrpp6r1hvjqx9.png)

如你所见，刷第一层漆需要很长时间(4-4.5 秒)。解决这个问题最简单的方法是，考虑到我们使用 CRA 并且不想弹出，就使用[反应-快照](https://github.com/stereobooster/react-snap)。

## 借助 react-snap 添加预渲染

```
npm install --save react-snap
 # or using Yarn
yarn add react-snap 
```

在`package.json` :
上增加`postbuild`挂钩

```
"scripts":  {  "postbuild":  "react-snap"  } 
```

你完了！

我还加了

```
"reactSnap": {
  "inlineCss": true
} 
```

[![filmstrip](img/1b2a543992e3603ed866199ce9fa9d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AKMpDf50--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/999fd7o1xyxe55tnbcex.png)

正如你所看到的，第一次上色慢的问题消失了，但是有一个白色屏幕的闪光。

### 白屏的闪光

一方面，我们有预渲染的 HTML，一旦浏览器得到它，它就会开始渲染(在美国平均 3G 大约 2 秒)。另一方面，我们有 React，它将在所有脚本下载完成后立即开始渲染(对于给定的示例，在美国平均 3G 时间约为 3s)。

React 开始渲染时，如果没有加载所有动态资源，它将刷新所有内容，通常这是几乎白色(空白)的屏幕。这就是我们得到“白屏闪光”的地方。动态资源可以是:异步组件(`React.lazy(() => import())`)、区域设置目录(`import("./locales/" + locale + "/messages.js");`)。

为了解决这个问题，我们需要在 React 将更改刷新到 DOM 之前等待所有资源加载完毕。

我们可以用类似于，`react-loadable`或`loadable-components`这样的加载器库来做这件事。查看更多详情[此处](https://github.com/stereobooster/react-snap#async-components)。

或者我们可以用新的`React.lazy`、`<Suspense />`和`<ConcurentMode />`来做这件事。

### 并发模式

`<ConcurentMode />`标记为不稳定(使用风险自担)，所以以后可以更改。在这里阅读更多关于如何使用它和注意事项的信息。

```
const ConcurrentMode = React.unstable_ConcurrentMode;
const RootApp = (
  <ConcurrentMode>
    <Suspense fallback={<div>Loading...</div>} maxDuration={5000}>
      <App />
    </Suspense>
  </ConcurrentMode> );
const rootElement = document.getElementById("root");
const root = ReactDom.unstable_createRoot(rootElement, { hydrate: true });
root.render(RootApp); 
```

这是我们需要的第一次破解。

第二，我们需要重新利用`React.lazy`来等待子资源。React 团队最终会为此添加`Cache`，但目前，我们还是继续黑吧。

```
const cache = {};
export default ({ locale, children }) => {
  const SuspendChildren =
    cache[locale] ||
    React.lazy(() =>
      i18n.activate(locale).then(() => ({
        __esModule: true,
        default: ({ children }) => (
          <I18nProvider i18n={i18n}>{children}</I18nProvider>
        )
      }))
    );
  cache[locale] = SuspendChildren;
  return <SuspendChildren>{children}</SuspendChildren>; }; 
```

*   `i18n.activate(locale)`返回承诺，我们将其“转换为 ES6”模块，例如`i18n.activate(locale).then(() => ({ __esModule: true, ...}))`相当于`import()`。
*   `default: ...` -伪 ES6 模块的默认导出
*   `({children}) => <I18nProvider i18n={i18n}>{children}</I18nProvider>`反应功能成分
*   `<SuspendChildren />`将告诉顶层的`<Suspense />`暂停渲染，直到加载语言目录

`<ConcurentMode />`将启用`<StrictMode />`，它将在`react-router`、`react-router-dom`中抱怨不安全的方法。因此，我们需要更新到测试版，其中的问题是固定的。 [`react-helmet`也与`<StrictMode />`](https://github.com/nfl/react-helmet/issues/426) 不兼容，所以我们需要用`react-helmet-async`来代替它。

不管怎样，但我们“修复”了它。

[![](img/3de0d002670a3a8857043b926f9ab95c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BDb4CK0B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tuchbvoaw0ug0wnphppc.png)

*Pankaj Patel 在 Unsplash 上拍摄的照片*