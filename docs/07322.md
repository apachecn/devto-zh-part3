# 修复 chrome 72、73+中的 chrome flexbox 布局

> 原文：<https://dev.to/jaga/fix-chrome-flexbox-layouts-in-chrome-72-73-165a>

Chrome 72 在 flexbox 元素的渲染方式上引入了一个变化，这导致了一些奇怪的产品从一天到另一天的损坏，而没有任何代码变化。

[![chrome-before-fix](img/523ab12bfc464c528bc38cca62bce789.png)](///static/fc02cb35b46762ccb2e475c40e37bf2d/7f393/chrome-before-fix.png)

修复很简单，但是很难找到。带有`height: 100%`的 flexbox 元素现在需要设置`min-height`，甚至 0 值也可以。

```
.list {
  display: flex;
}

.item {
  height: 100%;
  min-height: 0; /* add this */
} 
```

[![chrome-after-fix](img/319a4276b677affd017beb616c064d76.png)](///static/15b2702193a9e6066cee1dfbc315be15/7c949/chrome-after-fix.png)

更多信息:[https://developers.google.com/web/updates/2019/01/devtools](https://developers.google.com/web/updates/2019/01/devtools)