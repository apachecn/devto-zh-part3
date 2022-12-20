# 重置还是不重置 CSS？

> 原文：<https://dev.to/henrique/reset-or-not-reset-css-1201>

在我最近的 4 或 5 个项目中，我放弃了重置 CSS，只是添加了这个:

```
* {
  box-sizing: border-box;
}

body {
  margin: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

CSS 重置很棒，当然！节省了大量的测试时间...还有很多头疼的事。但是你有没有尝试过从你的项目中移除重置 css，看看它是否有很大的不同？使用重置 CSS 后，你已经用可访问性的观点看了你的项目？