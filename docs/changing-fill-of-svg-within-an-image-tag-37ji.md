# 更改图像标签中 SVG 的填充

> 原文：<https://dev.to/horus_sky/changing-fill-of-svg-within-an-image-tag-37ji>

### 有没有办法在 svg 本身之外改变“img src”中“svg”的填充？

例如

```
<img srcimg/logo.svg" alt="company logo"> 
```

Enter fullscreen mode Exit fullscreen mode

我知道我可以简单地改变 svg 代码本身的填充。我也知道如果 svg 是内嵌的，我可以使用 CSS 来改变填充。目前，我在更改打印样式表中的填充时遇到了问题。

#### 场景

我正在使用一个 svg 标志，有一个白色的填充。但是我想在打印样式表上把填充从白色换成黑色，这样当客户打印页面时，svg 实际上会显示在白纸上。

在我的打印样式表中添加任何 css 都不会影响 svg。有没有办法完全用 CSS 来改变填充，或者我需要重新格式化我引用 SVG 本身的方式，或者我需要使用 JS？