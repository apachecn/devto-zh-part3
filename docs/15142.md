# 阻止 Flexbox 溢出的快速提示

> 原文：<https://dev.to/martyhimmel/quick-tip-to-stop-flexbox-from-overflowing-peb>

我遇到了一个问题(又一次😅)我的 flex 项目在一个浏览器中没有缩小到某个点以下，但在另一个浏览器中没有(例如，它在 Chrome 中工作，但在 Firefox 中不工作)。原来，这只是一行代码:将`min-width: 0;`添加到项目中。

```
.flex-container {
    display: flex;
}

.flex-item {
    flex: 1 1 0;
    min-width: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode