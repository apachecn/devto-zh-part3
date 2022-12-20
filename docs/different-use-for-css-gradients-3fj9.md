# CSS 渐变的不同用法

> 原文：<https://dev.to/flarex/different-use-for-css-gradients-3fj9>

最近我在做一个小项目，做一个数字钟的 chrome 扩展。我想让设计尽可能的简洁，同时减少扩展的尺寸。因此，我尝试使用 CSS 渐变来代替常规的无聊背景，看看我是否能想出一些东西。

尝试了各种可能的方法，但梯度很差。所以作为最后的手段，我谷歌了一下，看看我是否可以有一个多色的 CSS 渐变，而不需要从一种颜色到另一种颜色的过渡。然后偶然发现了这个帖子:
[https://blog . prototypr . io/CSS-only-multi-color-backgrounds-4d 96 a 5569 a 20](https://blog.prototypr.io/css-only-multi-color-backgrounds-4d96a5569a20)

这里的简单逻辑是，我们在 x%处停止第一种颜色，在 x%或< x%处开始第二种颜色。这将从线性渐变中移除渐变，并在两种颜色之间定义一个清晰的边缘，从而为背景提供一个多色材质效果。你可以在帖子里找到更多。

可以在这里查看扩展:[https://chrome . Google . com/web store/detail/simple-clock/fngkigemfllijloedplghepagidclef](https://chrome.google.com/webstore/detail/simple-clock/fngkigemfllijjloedplghepagidclef)

[![](img/4961e07fcbb0b85c14c1bf21bacf9e4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H23W-IoF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9u5iqhia8r9l4by6nxfx.png)

GitHub 项目链接:[https://github.com/Raja-Krishna/Simple-Clock](https://github.com/Raja-Krishna/Simple-Clock)