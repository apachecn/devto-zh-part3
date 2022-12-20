# 如何在 Div 底部放置箭头

> 原文：<https://dev.to/flippedcoding/how-to-put-arrows-at-the-bottom-of-a-div-4hnn>

你可能在网站上看到过箭头，指示你在完成一个部分后继续向下滚动页面。这些都是简单、超级有效的样式元素，有助于内容的流动。

如果你一直在试图找出如何制作这些元素，或者你正在寻找一种方法来吸引人们对其他信息的注意，请跟随这个简短的教程。

你可能需要 5 - 10 分钟来弄清楚这是如何工作的，它有什么好处，这样你就不会浪费任何时间。

你只需要 HTML 和 CSS 来创建这个元素。

HTML 很简单。您所需要的只是一个带有类名的 div 元素。看起来是这样的:

```
<div class="bottom-arrow"></div> 
```

Enter fullscreen mode Exit fullscreen mode

这就是 HTML 的全部内容。现在是时候看看 CSS 了，这是你必须变聪明的地方。

的样式。底部箭头类很容易编写。

```
.bottom-arrow {
      border-bottom: 5px solid #6A0136;
} 
```

Enter fullscreen mode Exit fullscreen mode

你做箭头的方式才是有趣的地方。首先，您将使用伪类选择器:after 开始。

在选择器内部，您需要为 content 属性设置一个值。如果您没有任何想要显示的文本，空字符串也可以。

然后，您必须设置一个位置值，以便箭头停留在您为 div 设置的底部边框上。如果没有设置这个值，你的箭头将不会出现在你认为它会出现的地方。你自己去看看吧。

接下来，您需要使用 margin 属性将箭头在 div 上居中。如果对您的设计更好，您也可以使用边距使箭头偏离中心。

之后，继续将高度和宽度属性设置为零。你想这样做的原因是，如果你给箭头一个宽度，它就不会看起来像一个箭头，除非你做一些疯狂的 CSS 魔术。只要宽度不变，高度不一定要设置为零，但这样做将有助于避免以后出现令人讨厌的布局问题。

您必须做的最后一件事是设置一些边框属性。您需要设置 border-top 属性。这是你设置箭头高度和颜色的方法。

接下来，您需要设置 border-left 和 border-right 属性。这些控制你箭头的宽度。这里有一个有趣的小插曲，你没有给 border-left 和 border-right 属性颜色。需要将它们设置为透明，因为它们负责创建箭头。

可以把 border-left 和 border-right 属性想象成从一个盒子中剪下一个三角形，这正是正在发生的事情。

下面是我们一直在谈论的 CSS 代码:

```
.bottom-arrow:after {
    content:'';
    position: absolute;
    left: 0;
    right: 0;
    margin: 0 auto;
    width: 0;
    height: 0;
    border-top: 25px solid #6A0136;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
} 
```

Enter fullscreen mode Exit fullscreen mode

没那么糟糕。你只需要知道 CSS 是如何工作的，以及它是如何被操纵的。可能有 100 种其他方法来做同样的事情，但这是我想出的方法。

我希望这个快速浏览有所帮助！

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)