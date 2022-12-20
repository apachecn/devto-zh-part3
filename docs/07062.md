# Flexbox -对齐项目与对齐内容。

> 原文：<https://dev.to/anirudhvarma12/flexbox-align-items-vs-align-content-3knl>

这篇文章最初发表在[我的博客](https://un-defined.netlify.com/align-items-vs-content-flexbox/)

[Flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox) 牛逼。如果你使用过现代的 CSS，不管你是自己开发还是使用 Bootstrap &基金会这样的框架，你都有可能使用 Flexbox 来进行布局和对齐。

> 对齐或调整..这是一个问题

很长一段时间以来，我一直试图弄清楚`align-items`、`align-content`、&、`justify-content`之间的区别以及它们是如何工作的，或者更重要的是*在*工作的时候。

### TL；速度三角形定位法(dead reckoning)

*   如果您将`flex-direction`设置为`row`，那么`justify-content`在水平轴上工作，而`align-*`属性在垂直轴上工作。
*   如果您将`flex-direction`设置为`column`，那么`align-*`属性在水平轴上工作，而`justify-content`在垂直轴上工作。

参见 [CodePen](https://codepen.io) 上阿尼鲁德·瓦尔马
( [@thebigfatpanda12](https://codepen.io/thebigfatpanda12) )的笔[对齐项目 vs 对齐内容](https://codepen.io/thebigfatpanda12/pen/aXqjNq/)。

### 什么分隔了 align-items 和 align-content？

*   `align-items`影响当前行项目的对齐。
*   `align-content`影响柔性容器的跨线对齐。这意味着该属性对单行容器没有影响。

这篇文章的其余部分只是我在试图解决上述问题时学到的所有东西的大脑转储。

## 命名

*   **Flex 容器**:一个`display`值被设置为`flex`的元素。
*   **伸缩项** : *直接*伸缩容器的子容器称为伸缩项。
*   **主轴**:由`flex-direction`属性定义的轴。

- `flex-direction:row`表示 flex 容器中的元素彼此相邻对齐。从技术上讲，这些元素沿着[内嵌轴](https://www.w3.org/TR/css-writing-modes-4/#inline-axis)对齐(就像内嵌元素一样)。

- `flex-direction:column`表示元素沿垂直轴对齐，即一个在另一个下面。技术上称为[块轴](https://www.w3.org/TR/css-writing-modes-4/#block-axis)(就像块元素)

*   **横轴**:垂直于主轴的轴称为横轴。

*   **单行容器** : Flex 容器，其`flex-wrap`属性设置为`nowrap`。默认情况下，每个 flex 容器都是单行容器。

*   **多行容器** : Flex 容器，其`flex-wrap`属性设置为`wrap`

## TL；带命名法的 DR

既然我们知道了行话，我们可以重写 TL；as 博士-

*   `justify-content`在主轴上工作，`align-*`属性在横轴上工作。
*   `justify-content`和`align-items`的行为相似，不同的是`justify-content`在主轴上工作，而`align-items`在横轴上工作。
*   `align-content`仅对多行容器有效，对单行容器无效。

## 来源

1.  w3.org 的 Flexbox 文档非常全面，值得一读。
2.  [MDN](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox) Flexbox 文档。