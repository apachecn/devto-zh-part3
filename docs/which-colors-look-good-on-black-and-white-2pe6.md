# 黑白配哪些颜色好看？

> 原文：<https://dev.to/finnhvman/which-colors-look-good-on-black-and-white-2pe6>

想象一下，你必须找到一种黑白都好看的颜色。我说的“看起来不错”是指至少符合 [WCAG AA 对比度(最小值)](https://www.w3.org/WAI/WCAG21/Understanding/contrast-minimum.html)，这是 **4.5:1** 的比率。

**TL；博士** : *滚动到最后得到颜色列表*

# 那么色彩对比度是如何计算的呢？

[WCAG 对比度定义](https://www.w3.org/TR/WCAG21/#dfn-contrast-ratio)说:

> **对比度**
> (L1 + 0.05) / (L2 + 0.05)，其中
> 
> *   L1 是较浅颜色的相对亮度，和
> *   L2 是较暗颜色的相对亮度。
> 
> 注意
> 对比度的范围可以从 1 到 21(通常写成 1:1 到 21:1)。

简单的数学，对吗？你取两个值，然后除以它们。

# 但是相对亮度是怎么计算的呢？？

[WCAG 相对亮度定义](https://www.w3.org/TR/WCAG21/#dfn-relative-luminance)说:

> **相对亮度**
> 色彩空间中任一点的相对亮度，标准化为 0 表示最暗的黑色，1 表示最亮的白色
> 
> 注
> 对于 sRGB 色彩空间，颜色的相对亮度定义为 L = 0.2126 ***R**+0.7152 ***G**+0.0722 ***B**其中 **R** 、 **G** 和 **B** 定义为:
> 
> *   如果 R <sub>sRGB</sub> < = 0.03928 那么**r**= r<sub>srgb</sub>/12.92 else**r**=((r<sub>srgb</sub>+0.055)/1.055)^ 2.4
> *   如果 G <sub>sRGB</sub> < = 0.03928 那么**g**= g<sub>srgb</sub>/12.92 else**g**=((g<sub>srgb</sub>+0.055)/1.055)^ 2.4
> *   如果 B <sub>sRGB</sub> < = 0.03928 那么**b**= b<sub>srgb</sub>/12.92 else**b**=((b<sub>srgb</sub>+0.055)/1.055)^ 2.4
> 
> 并且 R <sub>sRGB</sub> ，G <sub>sRGB</sub> ，B <sub>sRGB</sub> 定义为:
> 
> *   R<sub>sRGB</sub>= R<sub>8 bit</sub>/255
> *   G<sub>sRGB</sub>= G<sub>8 bit</sub>/255
> *   B<sub>sRGB</sub>= B<sub>8 bit</sub>/255

不要被方程式迷惑，这仍然是简单的数学！最后三行是通过用 **255** 划分颜色通道来标准化颜色通道，下面是一些例子:

| 颜色 | r<sub>8 位</sub> | g<sub>8 位</sub> | b<sub>8 位</sub> | -> | <sub>sRGB</sub> | g<sub>srgb</sub>的缩写 | <sub>sRGB</sub> |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 黑色 | Zero | Zero | Zero |  | Zero | Zero | Zero |
| 怀特（姓氏） | Two hundred and fifty-five | Two hundred and fifty-five | Two hundred and fifty-five |  | one | one | one |
| 红色 | Two hundred and fifty-five | Zero | Zero |  | one | Zero | Zero |
| 紫色 | One hundred and twenty-eight | Zero | One hundred and twenty-eight |  | Zero point five zero two | Zero | Zero point five zero two |
| 柑橘 | Two hundred and fifty-five | One hundred and sixty-five | Zero |  | one | Zero point six four seven | Zero |

然后，如果归一化值小于或等于 0.03928，则通过简单的除法(除以 12.92)得到 **R** 、 **G** 和 **B** 的值，或者如果归一化值大于 0.03928，则应用上面的指数公式。最后一步是用不同的权重对这三个值求和。

# 我们来算算！

不难看出，黑色的相对亮度是 0，白色是 1。计算它们的对比度得出(1 + 0.05) / (0 + 0.05) = 21。我们可以通过下式计算任何其他颜色相对于黑白的对比度(其中“c”是颜色的相对亮度):

*   反黑:(c + 0.05) / (0 + 0.05)
*   反白:(1 + 0.05) / (c + 0.05)

我们要求对比度至少为 4.5，因此:

*   反黑:(c + 0.05) / (0 + 0.05) >= 4.5
*   反白:(1 + 0.05) / (c + 0.05) >= 4.5

解决这两个不等式给出:

*   相对于黑色:c >= 0.175
*   相对于白色:0.1833 >= c

最后我们得到相对亮度要求:

**0.175 < = c < = 0.1833**

# 颜色列表

最后，我创建了一支钢笔，它遍历 RGB 颜色空间，从黑色到白色，每个通道的增量为 0x11 = 17。遍历所有的颜色会花费一些时间和 CPU，我确信列出大约 300k 的颜色是不实际的。因此，这支笔遍历可以用 3 位六进制表示法描述的颜色，并列出符合我们要求的 76 种颜色。[明智地使用它们；)](https://codepen.io/finnhvman/full/bZQLgR)
[https://codepen.io/finnhvman/embed/bZQLgR?height=600&default-tab=result&embed-version=2](https://codepen.io/finnhvman/embed/bZQLgR?height=600&default-tab=result&embed-version=2)