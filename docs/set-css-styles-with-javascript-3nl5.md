# 用 Javascript 设置 CSS 样式

> 原文：<https://dev.to/karataev/set-css-styles-with-javascript-3nl5>

假设你有一个段落。

```
<p id="target">rainbow 🌈</p> 
```

Enter fullscreen mode Exit fullscreen mode

你需要用 JS 改变它的颜色。你有什么选择？

## 1。内嵌样式

最直接的途径。从 DOM 中查询元素并更改其内联样式。

```
document.getElementById('target').style.color = 'tomato'; 
```

Enter fullscreen mode Exit fullscreen mode

[![inline styles](img/e58eb52eeceda28b91451ed3f79e51c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uwj49EBr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hd67z2je0m8u7zyf7231.jpg)

简短明了。

## 2。全局样式

另一个选择是创建`<style>`标签，用 CSS 规则填充它，并将标签附加到 DOM。

```
 var style = document.createElement('style');
  style.innerHTML = `
  #target {
  color: blueviolet;
  }
  `;
  document.head.appendChild(style); 
```

Enter fullscreen mode Exit fullscreen mode

[![global styles](img/5348dd94490eab0ca3e2b4c23a3c9fd4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dbqMBUgD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zhkm5phjflfick5xesu2.jpg)

## 3\. CSSOM insertRule

第三种选择不太为人所知。我们将使用[CSS 样式表](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleSheet) `insertRule`方法。

```
 var style = document.createElement('style');
  document.head.appendChild(style);
  style.sheet.insertRule('#target {color: darkseagreen}'); 
```

Enter fullscreen mode Exit fullscreen mode

虽然它可能看起来类似于第二个选项，但它肯定是不同的。
[![insertRule](img/cf712b6cfd5c282b1b8773acc6063e30.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--VJuSGlJt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/trjxmsf01o01w8c7u224.jpg)

正如你在 Chrome devtools 中看到的，`<style>`标签是空的，但是样式(暗海绿色)被应用到元素上。而且颜色不能通过 devtools 改变，因为 Chrome 不允许[编辑动态 CSS 样式](https://bugs.chromium.org/p/chromium/issues/detail?id=387952)。

事实上，这样的行为是我写这篇文章的动机。一个流行的 CSS-in-JS 库[样式化的组件](https://www.styled-components.com/)因为性能原因使用这个特性在生产模式中注入样式。这个特性在特定的项目或环境中可能是不受欢迎的，一些人在项目的问题中抱怨它。

## 4。可构建的样式表(2019 年 7 月更新)

现在可以从 JavaScript 创建`CSSStyleSheet`对象。

```
// Create our shared stylesheet:
const sheet = new CSSStyleSheet();
sheet.replaceSync('#target {color: darkseagreen}');

// Apply the stylesheet to a document:
document.adoptedStyleSheets = [sheet]; 
```

Enter fullscreen mode Exit fullscreen mode

更多详情在[这里](https://developers.google.com/web/updates/2019/02/constructable-stylesheets)。
这个选项只对 Chrome 有效，所以慎用。

你知道用 javascript 添加样式的其他选项吗？这些天你更喜欢什么？

感谢阅读！