# 图像占据了页面的多大部分？

> 原文：<https://dev.to/crenshaw_dev/how-much-of-a-page-is-occupied-by-images-g9g>

我很好奇图片占据了页面的多大面积。所以我拼凑了一个粗略的近似值:

```
function PercentOfPage(selector) {
    return [...document.querySelectorAll(selector)].reduce(
            (a, i) => a + i.offsetWidth * i.offsetHeight,
            0
        ) / (document.body.offsetHeight * document.body.offsetWidth);
} 
```

Enter fullscreen mode Exit fullscreen mode

出于我的目的，我会使用`const imgArea = PercentOfPage('img');`。

它不考虑目标元素是否实际可见(例如，它们可能位于页面外或另一个元素后面)。但对我来说已经足够好了。

请大家提供建议！我很想对这个工具进行更多的微调。

附:本页约 1%图片。