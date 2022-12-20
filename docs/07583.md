# 使用 CSS 的渐变文本样式

> 原文：<https://dev.to/paramharrison/gradient-text-style-using-css-11ka>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

渐变背景开创了这一趋势，在现代浏览器广泛支持渐变后，如今网站的许多元素有了更多的渐变颜色。

其中一个方面就是用渐变颜色来设计文本。让我们来看看它的实际应用

```
.gradient-text {
  // set the background color
  background: linear-gradient(to right, #ff8a00 0%, #da1b60 100%);
  // background color masked along the text and clip away the rest
  -webkit-background-clip: text;
  // make fill color to transparent so that masked background color will be shown
  -webkit-text-fill-color: transparent;
} 
```

### 浏览器兼容性

*   这仅在基于 WebKit 的浏览器中受支持。对于其他浏览器，color 属性可以作为后备选项来呈现与渐变颜色相关的颜色。
*   是一个非标准的 CSS，所以有 fallback color 属性来支持每个浏览器，而不会在不支持的浏览器上引起可访问性问题。

[https://codepen.io/Param-Harrison/embed/pGgGxp?height=600&default-tab=result&embed-version=2](https://codepen.io/Param-Harrison/embed/pGgGxp?height=600&default-tab=result&embed-version=2)

除了 IE，所有主流浏览器都支持这个解决方案。希望你学会一个在你的网站上用渐变颜色显示文本的技巧😋

> 注:本文最初是为[我的博客](https://learnwithparam.com/blog/gradient-text-in-css/)写的。我在这里为神奇的开发者社区重新发布它。