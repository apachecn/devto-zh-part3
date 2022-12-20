# RGB 与 HSL:又一轮

> 原文：<https://dev.to/metamn/rgb-vs-hsl-another-round-3db5>

人们应该能够使用代码来处理颜色，并以思考其他编程工件(如数据和算法)的同样方式来思考颜色。

## 颜色模型和空间

为了使颜色可以量化和计算，使用了颜色模型和颜色空间，如 RGB、HSL 或 CMYK。

CMYK 专用于打印。RGB，HSL 专用于计算机显示器。

每个模型都以特定的方式表现颜色。有时这使得颜色是可计算的，在其他情况下不是。

## RGB

RGB 是一种颜色模型，其中每种颜色都有三个主要属性:每种颜色中出现的`red`、`green`和`blue`的数量。RGB 中的颜色是通过添加/混合这三种基本颜色/属性来创建的。

还有第四个可选属性:alpha 透明度或不透明度，定义颜色的透明度。

符号:

`rgba(red, green, blue, alpha transparency)`

其中:

*   红色:一种颜色中红色所占的百分比，用`%`或【0】表示..255]其中 255=100%
*   绿色:一种颜色中绿色所占的百分比
*   蓝色:一种颜色中蓝色所占的百分比
*   Alpha 透明度:颜色的不透明程度，以百分比表示。0 表示完全不透明

用 CSS 表示法:

```
--red: rgba(255, 0, 0, 1);
--red-opaque: rgba(255, 0, 0, .5);
--green: rgba(0, 255, 0, 1);
--blue: rgba(0, 0, 255, 1);
--black: rgba(0, 0, 0, 1);
--white: rgba(255, 255, 255, 1); 
```

## HSL

HSL 是基于 RGB 的颜色模型。它是对 RGB 的增强，提供了一个更直观的框架来思考颜色，以模仿画家创造和使用颜色的古老最佳实践。

在 HSL 中，颜色又有三个主要属性，即色调、饱和度和亮度。HSL 中的颜色是通过选择一种基本颜色(色调)并添加灰色(饱和度)和白色或黑色(亮度)来创建的。

还有第四个可选属性:alpha 透明度或不透明度，定义颜色的透明度。

符号:

`hsla(hue, saturation, lightness, alpha transparency)`

其中:

*   色相:颜色本身，如`red`、`green`或`blue`，用角度表示。0 度=红色= 360 度，绿色= 120 度，蓝色= 240 度。
*   饱和度:颜色中`grey`的数量，以百分比表示:0% =颜色完全是灰色，100% =颜色中完全没有灰色
*   明度:一种颜色中`black`和`white`的量，以百分比表示:0% =颜色全黑，50% =颜色全是颜色本身，100% =颜色全白。
*   Alpha 透明度:颜色的不透明程度，以百分比表示。0 表示完全不透明。

```
--red: hsla(0, 100%, 50%, 1);
--red-opaque: hsla(0, 100%, 50%, 0.5);
--green: hsla(120, 100%, 50%, 1);
--blue: hsla(240, 100%, 50%, 1);
--black: hsla(anything, anything, 0%, 1);
--white: hsla(anything, anything, 100%, 1); 
```

## RGB 与 HSL

对于 RGB 符号，颜色不提供关于它们的性质以及与调色板中其他颜色的关系的信息。

例如`rgb(125, 125, 125)`表示红色、绿色和蓝色各占一半的颜色。`rgb(63, 63, 63)`表示颜色为前一种颜色的红、绿、蓝的一半。

在 HSL 中`hsl(123, 50%, 75%)`表示 50%灰色和 75%白色的颜色。`hsl(256, 50%, 75%)`表示另一种颜色，灰色和白色等量。

使用 HSL 符号很容易形成调色板。选择饱和度和明度相同的两种颜色作为原色，然后迭代饱和度和明度，得到一组单色色对。

这是一种简单、直观和可编程的方法。

对于 RGB 符号，我们没有这样一种直观的方法来迭代颜色，并仍然保持它们在同一个系列中。

## 资源

*   [https://en.wikipedia.org/wiki/Color_space](https://en.wikipedia.org/wiki/Color_space)
*   [https://developer . Mozilla . org/en-US/docs/Web/CSS/color _ value](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value)
*   [https://www.w3schools.com/colors/colors_hsl.asp](https://www.w3schools.com/colors/colors_hsl.asp)