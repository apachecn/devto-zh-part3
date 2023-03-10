# CSS 右首字母和右自动是做什么的？

> 原文：<https://dev.to/brianmcoates/what-do-css-right-initial-and-right-auto-do-2opp>

今天做代码审查，看到了一些让我感兴趣的代码。

```
.button {
    position: fixed;
    right: initial;
} 
```

有趣的是，我深入研究了 initial 的功能，发现它将该属性设置为浏览器的默认设置。有一篇关于这个的关于 [css 技巧的文章真的很好。](https://css-tricks.com/getting-acquainted-with-initial/)

那么 right 属性的初始值是什么呢？我做了一些调查，发现默认值是 auto(至少对于 chrome 是这样)。这让我很困惑，因为我总是使用`position: fixed`，然后用左、右、上、下把它放在我想放的任何地方。我从未想过如果正确的属性是 auto(默认情况下就是这样)会发生什么。我设置了一个密码笔来演示。有趣的是当你滚动的时候。

[https://codepen.io/FlaminSeraphim/embed/oOrGaO?height=600&default-tab=result&embed-version=2](https://codepen.io/FlaminSeraphim/embed/oOrGaO?height=600&default-tab=result&embed-version=2)

MDN 在这里很好地描述了 right 属性的功能。

当 position 设置为 absolute 或 fixed 时，right 属性指定元素的右边缘与其包含块的右边缘之间的距离

当`right: auto`被设置时，浏览器将计算 right 属性需要被设置成什么，以便子元素的左侧紧挨着父元素的左侧，因为它是固定的，当你滚动时你可以滚动通过包含它的 div，但是固定元素的位置保持不变。

我希望你今天能和我一起学到一些有用的东西。