# 被认为有害的相对字体粗细

> 原文：<https://dev.to/denmch/relative-font-weights-considered-harmful-4f3i>

不完全是，但是当你可以逃避责任的时候，谁能拒绝写一篇“被认为是有害的”文章呢？

真正的危害是，根据呈现的字体，您可以非常容易地隐藏由`font-weight`传达的语义，这并不总是在您的控制之下。这完全取决于您如何定义相对值所涉及的基本权重，以及(1)该基本权重在呈现的字体中是否实际可用，以及(2)如果不可用，则替换哪个值。

让我们打开包装。

## 字体粗细

当我们用简单的术语如`normal`和`bold`来处理时，字体粗细很容易理解。网页上的字体粗细传统上是二进制的，更像是*加粗*和*不加粗*。但是这些关键字实际上对应于数值，并且如果该数值不可用，将解析为另一个权重:

| 关键词声明 |  | 数字声明 |
| --- | --- | --- |
| 字体粗细:正常； | = | 字体粗细:400； |
| 字体粗细:粗体； | = | 字体粗细:700； |

字体可以是多达九种粗细中的任何一种，以 100-900 为整数。毫无疑问，你已经在不同的上下文中见过带有命名粗细的字体，比如“细”、“中”、“超粗体”和“黑色”。每一个都将与九个编号的重量中的一个相对应。

虽然大多数字体只有少量可用的粗细，但有些字体的粗细范围很广，从 100 到 900，随着 web 字体的普及，这种情况越来越普遍。(这本身就是另一个问题，为用户膨胀站点，浪费带宽。)

## 向前 1 步，向后 X 步

如果呈现的字体缺少相应的权重，[浏览器将根据简单的算法](https://developer.mozilla.org/en-US/docs/Web/CSS/font-weight#Common_weight_name_mapping#Fallback_weights)选择最接近的权重。

一种简单的思考方式是这样的:

*   `normal`想要变得正常——或者至少*不大胆*
*   `bold`想变得大胆——或者至少*不薄*

正常人会犯错误，如果大胆不能大胆，它会尝试它最该死的。

`normal`的范围是 400–500。任何高于 600 或更高的值都在`bold`范围内，尽管 700 是最佳值。虽然它没有基础名称，但 100-300 可以被认为是轻的。

对于普通文本，如果呈现的字体缺少权重 400，它将变为 500。如果那个都没有，那它真的不想大胆了，那它除了往下走，到 300 就没别的路了。诸如此类。如果必要的话，它会加粗，但它想成为*而不是加粗*。

同样，对于粗体文本，如果呈现的字体缺少 700 的权重，它会偏向于粗体:800，然后 900，然后它会无可奈何地放下肩膀并向后工作，取第一个可用的权重，即最轻的权重。

这很好。但是相对关键词`lighter`和`bolder`会发生什么，为什么我会取笑它们“被认为是有害的？”

## 小心你的要求

假设您在 CSS 文件中要求一个特定的权重:

```
p {
    font-weight: 300;
}
p strong {
    font-weight: stronger;
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来像是一个不公平的例子，但是这样的事情确实发生了，你认为`strong`文本会比段落的其余部分看起来更粗是情有可原的。

如果渲染的字体有 300 个可用的权重，那么*会显得更强。但是如果没有呢？*

`lighter`和`bolder`关键字[响应继承的权重](https://developer.mozilla.org/en-US/docs/Web/CSS/font-weight#Meaning_of_relative_weights)，并且可以根据声明的权重解析为 100、400、700 或 900。因此，举例来说，我们的示例中的一段`font-weight: 300`包含一个`strong`元素，而`font-weight: bolder;`如下所示:

```
p {
    font-weight: 300;
}    
p strong {
    font-weight: 400;
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们把`300`改成`400`，那么`bolder`就变成等同于`700`。这不是你想要记录的事情，当然也是你永远无法跟上的事情。

但是假设你继续定义你的字体粗细为 300，它是不可用的。根据选择算法，文本将以最接近的可用权重呈现。如果在普通的 Windows 机器上呈现的字体是 Arial，那么可用的权重可能是 500。`bolder`的`strong`声明意味着“比 300 更粗”，因此是“400”，但它也将按照定义的算法以 500 呈现，以解析可用的字体粗细。

```
p {
    font-weight: 300; /* Unavailable: Resolves to 500 */
}    
p strong {
    font-weight: 400; /* Unavailable: Resolves to 500 */
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，视觉语义丢失了。

因此，如果这对你很重要(应该如此)，那么在这里要小心行事。至少，在选择 web 字体的权重时，您应该非常慎重，并且您的权重声明在没有权重声明的情况下会有多好。

* * *

<small>Photo by [Natalia Y](https://unsplash.com/photos/Oxl_KBNqxGA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](/search/photos/fonts?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</small>