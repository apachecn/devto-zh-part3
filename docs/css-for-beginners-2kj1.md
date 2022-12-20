# 面向初学者的 CSS

> 原文：<https://dev.to/elanandkumar/css-for-beginners-2kj1>

这篇文章将会给你一个了解 CSS 的开端。如何去接近它，以及你如何去深入了解更多的细节。

目标是:

*   什么是 CSS？
*   CSS 的使用方法有哪些？
*   编写 CSS 的语法
*   层叠顺序
*   常用选择器备忘单。

### 层叠样式表

那么，级联是什么意思？“层叠”在这个上下文中意味着通过将更一般的规则层叠到所需的更具体的规则来选择 css 规则。

## CSS 有哪些使用方法？

一般来说，有三种使用 CSS 的方法，如下所示:

### 1。**外部:** `<link>`标签内部`<head>`标签使用外部 CSS 文件。

```
<link rel="stylesheet" href="styles.css"> 
```

### [2](#2-internal-raw-ltstylegt-endraw-tag-mostly-used-in-the-raw-ltheadgt-endraw-tag)。**内部:** `<style>`标签(多用于`<head>`标签)

```
<head>
    <style>
        /* styles goes here */
    </style>
</head> 
```

> 你可能想知道为什么我们在 html 中使用`/* */`。因为，在 CSS 中，注释只有这种方式。无论你是在扩展名为`.css`的外部文件中还是在`<style>`标签中编写 CSS。

### 3。**内联:**在 HTML 元素上使用样式属性

```
<div style="background-color: red;"></div> 
```

请注意上面的`style="background-colour: red;"`文字。分号后可以添加更多的属性。

## 编写 CSS 的语法

只有一种语法，所以你不需要记住这里的很多东西。下面是给出的例子:

```
Selector {
    property: value;
} 
```

#### 一种解释:

*   `Selector`是决定需要应用样式的元素的值。
*   `property`是要使用的样式属性的名称
*   `value`这里表示一个属性应该具有的样式值，它将在带有特定选择器的元素上可见
*   表示特定属性/值对的结束，我们可以在此之后添加另一个属性/值对
*   花括号表示`Selector`的一组样式属性

## 层叠顺序

正如我前面解释的级联，下面是级联发生时选择规则的优先顺序:

*   *优先级 1* :内嵌样式
*   *优先级 2* :外部和内部样式表
*   *优先级 3* :浏览器默认值

这里需要注意的重要一点是，如果一个样式被定义了相同的优先级，那么最后一个具有最高的优先级。(层叠规则)

## 常用选择器备忘单

因此，当我们得出结论时，这里列出了常用的选择器。请按照自己的节奏和时间练习。

> ## *No one in this world is perfect and knows everything. Take time to learn, but don't stop learning.*

以下是清单:

*   `*`
*   `.class`
*   `#id`
*   `element`
*   `element, element`
*   `element > element`
*   `element + element`
*   `element element`
*   `:hover`
*   `:first-child`
*   `:last-child`
*   `!important`(避免使用)

有一个附带的视频解释了这些选择器的用法。这是链接，值得一看，以了解这些选择器是如何编写的，以及它是如何工作的。

[https://www.youtube.com/embed/zynMOm1Mz5g](https://www.youtube.com/embed/zynMOm1Mz5g)

# 总结

仅此而已。

我希望你喜欢这篇文章，并从中学到一些东西。如果你喜欢这篇文章，我会感谢你的分享和喜欢。

PS。标题照片由 [*赛基兰·阿纳加尼*](https://unsplash.com/@_imkiran?utm_source=medium&utm_medium=referral) 上 [*Unsplash*](https://unsplash.com/)