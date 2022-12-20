# 响应文本

> 原文：<https://dev.to/jacobmparis/responsive-text-2hbk>

偶尔我会发现自己写的文字在桌面上看起来不错，但在手机上却是一堵难以辨认的文字墙，这让托尔金感到自豪。

为了解决这个问题，我开始在段落中的每个句子之间使用`<br />`元素，然后使用媒体查询在更大的屏幕上隐藏它们。

[https://codepen.io/anon/pen/dEyPep](https://codepen.io/anon/pen/dEyPep)

```
<p>
    Spicy jalapeno bacon ipsum dolor amet ball tip meatball burgdoggen cupim buffalo elit beef shank. 
    <br class="minor" />
    Ball tip biltong shoulder, officia tenderloin duis ut voluptate proident cupidatat bacon reprehenderit capicola. 
    <br class="major"/>
    Sunt in in, non filet mignon flank chuck cow eiusmod laboris officia alcatra aute. 
    <br class="minor"/>
    Officia commodo ut in, meatloaf veniam velit eu consequat. 
    <br class="major"/>
    Magna corned beef tail short loin enim, pastrami picanha shank meatloaf in burgdoggen pancetta frankfurter t-bone.
</p> 
```

Enter fullscreen mode Exit fullscreen mode

```
br {
  line-height: 2rem;
  @media screen and (min-width: 500px) {
    &.minor {
      display: none;
    }
  }
  @media screen and (min-width: 800px) {
    &.major {
      line-height: 1rem;
    }
  }
  @media screen and (min-width: 1000px) {
    &.major {
      display: none;
    }
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

您可以调整断点以匹配您的特定布局。

这里演示:[https://codepen.io/anon/pen/dEyPep](https://codepen.io/anon/pen/dEyPep)