# 用这一招大大减轻巴布亚新几内亚的体重

> 原文：<https://dev.to/jamesthomson/greatly-reduce-png-weight-with-this-one-trick-4p0g>

前言:在本文中，我假设您已经熟悉了 SVG。如果没有，现在是让你自己[熟悉](https://www.w3schools.com/graphics/svg_intro.asp)的好时机！SVG 的力量确实令人惊叹。

很可能你已经发现自己处于这种情况...你得到一个设计，它很漂亮，充满了色彩和图像。

然后，设计人员会通知您，英雄横幅的文本会发生变化，背景颜色也会发生变化。

这是英雄横幅的样子(虽然可能设计得更好，但是，嘿，它表达了重点):

[![Ice cream hero banner design](img/2d7ac8dd75058b0b38ad7d81139e5b82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AoRufD45--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tpr6l76i9im41y9hc0uu.jpg)

好的，没问题，这意味着我们不能只使用一个背景图像就完事了，我们需要把英雄分成几个部分，这样它的一些部分就可以是动态的。

通常你会用 PNG 来获取冰淇淋蛋卷的图片。但是，png 通常很大，尤其是当我们开始迎合高 DPI 屏幕时。我们的 PNG 冰淇淋将会达到惊人的 1.9 兆字节！因此，相反，我们将使用一个技巧与 [SVG 面具](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/mask)。

<figure>

[![Isolated ice cream image and mask](img/4c80133247a641e25a7fe61890c5ab8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m4hyYAm0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lltqlgebz3qwcenhqfz2.jpg)

<figcaption>Jpg (left) 292kb, 8-bit PNG (right) 17kb</figcaption>

</figure>

首先，我们将把冰淇淋保存为 jpg 文件。我将使用黑色背景来代替透明。

接下来，我们将另一个图像保存为 8 位 png，用作蒙版图像。任何黑色的部分都是不可见的，任何白色的部分都是可见的。

我们将在 SVG 中一起使用这两张图片，如下所示:

```

  <defs>
    <mask id="mask">
      <image width="938" height="1912" xlink:hrefimg/ice-cream-mask.png"/>
    </mask>
  </defs>
  <image mask="url(#mask)" width="938" height="1912" xlink:hrefimg/ice-cream.jpg"/>
 
```

Enter fullscreen mode Exit fullscreen mode

你可以引用上面代码中的图片，或者你甚至可以将它们内联为 [base64](https://www.base64-image.de/) ，你可以在这个代码栏中看到。

[https://codepen.io/getreworked/embed/QPzxwB?height=600&default-tab=result&embed-version=2](https://codepen.io/getreworked/embed/QPzxwB?height=600&default-tab=result&embed-version=2)

现在你知道了。我们已经从 1.9mb 的透明 PNG 发展到大约 309kb 的 SVG，而没有牺牲质量和透明度。很可爱，是吧？

一如既往，编码快乐！🤓

需要注意的是，我还没有测试所有的浏览器，但是 SVG 支持现在已经非常普及，所以我不认为会有很多问题。也就是说，如果你遇到了任何问题，请在评论中告诉我。

你也可以用这支[棒的代码笔来帮助你的测试](https://codepen.io/shshaw/full/IDbqC/)。