# CSS 颜色变量可以互相依赖吗？帮我击退 CSS 预处理器。

> 原文：<https://dev.to/itsarnavb/can-css-color-variables-depend-on-each-other-help-me-fight-off-css-preprocessors-4fl9>

这里，-暗-高光是-暗的更亮版本。有没有一种方法可以让-暗-高光依赖于-暗，自动使用相同的色调、饱和度和 alpha 值，但是增加 5%的亮度

```
:root {
  --dark: hsla(209, 28%, 6%, 1);
  --dark-highlight: hsla(209, 28%, 11%, 1);
} 
```

还有，有什么好的 CSS 新特性指南？