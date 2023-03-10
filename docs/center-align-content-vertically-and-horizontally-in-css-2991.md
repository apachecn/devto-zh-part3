# 居中在 CSS 中垂直和水平对齐内容

> 原文：<https://dev.to/paramharrison/center-align-content-vertically-and-horizontally-in-css-2991>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

垂直和水平居中对齐内容是任何网站的常见要求。使用 CSS 有几种方法可以实现它。

*   使用 **CSS 变换**
*   使用**柔性盒**

## CSS 变换

```
.content {
  position: relative;
  top: 50%;
  left: 50%;
  // shifts or translate the center point (X, Y) by (X - 50% of outerWidth, Y - 50% of outerHeight)
  transform: translate(-50%, -50%);
} 
```

[https://codepen.io/Param-Harrison/embed/ErPyva?height=600&default-tab=result&embed-version=2](https://codepen.io/Param-Harrison/embed/ErPyva?height=600&default-tab=result&embed-version=2)

### 局限性

*   它非常适合在✅的两个方向上对具有固定宽度的内容块进行居中
*   对于非固定宽度，内容块假定为父宽度的 100%。它水平扩展父容器的整个宽度。您可以通过删除 codepen 示例❌中的内容宽度来检查这一点
*   如果内容是**行内**级元素，这种技术就不起作用，它只对**块级**元素❌起作用

## 现代 flexbox 方式

```
.parent {
  display: flex;
  // centering along main axis - X axis - Horizontal
  justify-content: center;
  // centering along cross axis - Y axis - Vertical
  align-items: center;
} 
```

[https://codepen.io/Param-Harrison/embed/ZwQBxR?height=600&default-tab=result&embed-version=2](https://codepen.io/Param-Harrison/embed/ZwQBxR?height=600&default-tab=result&embed-version=2)

> **注意**:主轴和横轴取决于`flex-direction`属性。默认情况下，伸缩方向为`row`。如果设置为`column`，则主轴为 Y，横轴为 x

这种方法几乎适用于所有用例

*   内容块✅的固定宽度
*   内容块✅的非固定宽度
*   内容可以是内嵌层元素或块层元素✅

Flexbox 如此强大，你可以使用它轻松开发更多的样式和组件。所有主流浏览器都支持它，没有理由不使用它😊

> 注:本文最初是为[我的博客](https://learnwithparam.com/blog/center-align-content-vertically-and-horizontally-in-css/)写的。我在这里为神奇的开发者社区重新发布它。