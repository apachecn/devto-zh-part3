# JavaScript 中的色彩调和

> 原文：<https://dev.to/benjaminadk/make-color-math-great-again--45of>

### [T1】简介](#intro)

我最近用**电子**设计了一个彩色实用程序。[电子](https://electronjs.org/)是一个用于构建跨平台桌面应用的 **JavaScript** 框架。我了解到在编码时，颜色和数学是紧密联系在一起的。这篇文章总结了我的一些心得。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [本杰明达克](https://github.com/benjaminadk) / [颜色-工具-混音](https://github.com/benjaminadk/color-tool-remix)

### 颜色工具混音🎨一个跨平台的颜色实用程序

<article class="markdown-body entry-content container-lg" itemprop="text">

# 颜色工具混音

一个 HSL 颜色选择器，滴管，颜色分析器和调色板生成器。全彩工具。

<g-emoji class="g-emoji" alias="monocle_face" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f9d0.png">🧐</g-emoji> 寻找一个贡献者来帮助测试`macOS`。

[![GitHub release](img/727f7dea13e252460fee79a742c74f90.png) ](https://github.com/benjaminadk/color-tool-remix/releases) [ ![Github All Releases](img/53f89b007b157d849d0e863e2b44a989.png)](https://github.com/benjaminadk/color-tool-remix/releases)

[![Platform](img/36d45a79eaf7da5b4c711ff00ec9afda.png)](https://camo.githubusercontent.com/3b8fcd8802bd150d94e854c570361cd711d0dfd9a71bb6e9fb3c935f82549e4f/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f706c6174666f726d2d77696e646f77732d6c69676874677265792e7376673f7374796c653d666c61742d737175617265)[![AppVeyor](img/7d403b0bdf2bba44261e38e8e3a1deec.png)](https://camo.githubusercontent.com/ed88a8bbf3717de269f4b68518f0babbebd39963a753b29f612b54388fbc5ea4/68747470733a2f2f696d672e736869656c64732e696f2f6170707665796f722f63692f62656e6a616d696e61646b2f636f6c6f722d746f6f6c2d72656d69782e7376673f7374796c653d666c61742d737175617265)[![Platform](img/76f6e9b9fe984a2e442641bc6b9f1657.png)](https://camo.githubusercontent.com/df58f44b317a05f94e6868a2c17ad9c2bf8cb0db7cc28c8a9539e71e67762dce/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f706c6174666f726d2d6f73782d6c69676874677265792e7376673f7374796c653d666c61742d737175617265)[![Travis](img/916c8f38a9362db26e43654f926be81d.png)T11】](https://camo.githubusercontent.com/4b047bca2f78d8c20bf88cfd87906a36de27305d45f967f847b4e519065720a2/68747470733a2f2f696d672e736869656c64732e696f2f7472617669732f62656e6a616d696e61646b2f636f6c6f722d746f6f6c2d72656d69782e7376673f7374796c653d666c61742d737175617265)

[![](img/964af556df173cd2168b28fec4d87360.png)](https://camo.githubusercontent.com/d787f5039e47a2d0d752ddc3d8ee2c2e6485aacbd25c2c3cd12c3c09c484d18c/68747470733a2f2f73332d75732d776573742d312e616d617a6f6e6177732e636f6d2f636f6c6f722d746f6f6c2d72656d69782f636f6c6f722d746f6f6c2d312e676966)

## 内容

*   [安装](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#installation)
*   [用途](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#usage)
    *   [拾取器](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#picker)
    *   [快捷处理](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#dropper)
    *   [调色板](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#palettes)
    *   [发电机](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#generator)
    *   [项目模式](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#project-mode)
    *   [用户界面](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#user-interface)
    *   [选项](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#options)
*   [技术堆栈](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#tech-stack)
*   [变更日志](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#changelog)
*   [贡献者](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#contributors)
*   [捐赠](https://raw.githubusercontent.com/benjaminadk/color-tool-remix/master/#donate)

### 装置

[**最新发布**](https://github.com/benjaminadk/color-tool-remix/releases/latest)

| 平台 | 自动更新 | 可执行的 |
| :-: | :-: | :-: |
| **窗户** | 是 | `electron-picker-remix-setup-[VERSION].exe` |
| **苹果电脑** | 不 | `electron-picker-remix-setup-[VERSION].dmg` |
| **Linux** | 不适用的 | 不适用的 |

*由于缺少代码签名，系统警告是正常的*

### 使用

#### 采摘者

[![](img/964af556df173cd2168b28fec4d87360.png)](https://camo.githubusercontent.com/d787f5039e47a2d0d752ddc3d8ee2c2e6485aacbd25c2c3cd12c3c09c484d18c/68747470733a2f2f73332d75732d776573742d312e616d617a6f6e6177732e636f6d2f636f6c6f722d746f6f6c2d72656d69782f636f6c6f722d746f6f6c2d312e676966)

*通过多种输入方法快速创建颜色*

*   **颜色条**
    *   调整色调、饱和度、亮度和 Alpha 颜色条
*   **微调**
    *   调整色调、饱和度、亮度和 Alpha 输入值
*   **颜色字符串解析器**
    *   解析`hsl`、`rgb`、`hex`和`css named`颜色
*   **随机颜色**
    *   生成随机颜色，不包括白色、黑色和灰色
*   **快捷处理**
    *   从屏幕上选择任意像素
*   **发电机**
    *   用数学方法从一种基色生成颜色

#### 使滴下的东西

[![](img/9876e967b1971b5d8a6d039563389afc.png)](https://camo.githubusercontent.com/102804f4abcc0fbab70c7c434d2f6dbf7f3381245e0230dbc57f5caa937f9a23/68747470733a2f2f73332d75732d776573742d312e616d617a6f6e6177732e636f6d2f636f6c6f722d746f6f6c2d72656d69782f636f6c6f722d746f6f6c2d332e676966)

*从*中选择一个像素…

</article>

[View on GitHub](https://github.com/benjaminadk/color-tool-remix)

### 格式

到处都不缺少彩色格式。仅在 **CSS** 中，我们就可以选择 4 种格式。请注意，其中 3 种格式还有可选的`alpha`或不透明度设置。要了解**丽贝卡紫色**[的感人故事，请查看这篇短文](https://medium.com/@valgaze/the-hidden-purple-memorial-in-your-web-browser-7d84813bb416)。

[![rebeccapurple](img/ca80a1620ae622a954fd308348cbd37f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Du0Vp4He--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/odwqbpo6dom05ebhld3f.png)

| 格式 | 缩写 | 例子 |
| --- | --- | --- |
| 命名颜色 | 不适用的 | `rebeccapurple` |
| 十六进制的 | 十六进制 | `#663498` |
| 红色、绿色、蓝色 | RGB | `rgb(102,52,152)` |
| 色调、饱和度、亮度 | high-speedlaunch 高速快艇 | `hsl(270,49%,40%)` |

在我的日常工作中，我倾向于选择十六进制或 T2 RGB 而不是其他。然而，当设计**颜色工具**时，我使用了 **HSL** 作为我的基本格式。

### HSL 的好处

我发现 HSL 是最容易被人类理解的颜色格式。为什么？仔细看看这种格式的每个组成部分。*色相*是 0 到 360 之间的数字，代表色轮上的度数。在其他格式中，没有一个值能给我们提供关于最终颜色的更多信息。红色在 0/360。**饱和度**是颜色相对于灰色的量，100%是全彩色无灰色，0%是全灰色无颜色。**明度**是 0 到 100 之间的另一个刻度。0 是全黑，100 是全白，所以 50 是中间值。

[![wheel](img/328c87cd65a942a048c8c296c56c25a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--no5iegHA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dxqwg4bnqg869nvpnroh.png)

### 解析 HSL 字符串

为了使用 **HSL** 值，我们需要能够获取一个初始的字符串值，比如`hsl(270, 49%, 40%)`，并将其分解成单独的 H、S、L 和/或 A 段。

```
function parseHSL(str) {
  var hsl, h, s, l
  hsl = str.replace(/[^\d,]/g, '').split(',')   // strip non digits ('%') 
  h = Number(hsl[0])                            // convert to number
  s = Number(hsl[1])
  l = Number(hsl[2])
  return [h, s, l]                              // return parts
} 
```

Enter fullscreen mode Exit fullscreen mode

### 色彩调和

**色彩调和**是数学上相互关联的颜色组合。一些比较常用的和声包括`Complementary`、`Split Complementary`、`Triadic`、`Tetradic`和`Analogous`。让我们把它带回`rebeccapurple`看看这些看起来像什么。

| 和谐 | 颜色；色彩；色调 | 车轮 |
| --- | --- | --- |
| 补充的 | ![](img/f5a701da1b8b8aa75a531266a222c868.png) | ![](img/833ed19b103756ed1fdf177a87e1dfd1.png) |
| 分裂互补 | ![](img/611640fdef0e4d20133cee69fb45a04c.png) | ![](img/3ae6257fa6f0288d394fcc4a4401eb91.png) |
| 三人组合的 | ![](img/daf3889611f6943d15b53570ac541da2.png) | ![](img/cdf72c5337ab4bda0b8f4ba260d24c54.png) |
| 四元的 | ![](img/9fca09d4e64021223cce853ae92c0b87.png) | ![](img/6a2189c76dc0adaa7cef009e198cdf9d.png) |
| 类似的 | ![](img/177d6e3ceb7f10c9d2abca81960b27f3.png) | ![](img/ab0027e4f374da9cf2b9f02a4b356763.png) |

找到所有这些值归结为数学。这里使用 **HSL** 很方便。在这些计算中，我们最关心的是**色调**。当找到这些情况下的和声时，我们将保持**饱和度**和**亮度**不变。要直观地了解色轮上的和谐位置，请查看表格中的小图标。这些是固定的模式，如果我们知道我们的基色的色调，并且一个圆有 360 度，我们就能找到我们的和谐。例如，补色直接在对面，或 180 度之外。

```
const complementaryHue = (baseHue + 180) % 360 
```

Enter fullscreen mode Exit fullscreen mode

分割补色从补色+- 30 度产生两种颜色。在这一点上，我开始考虑一个能在现实生活中发挥作用的函数。这个函数需要一个实际的颜色字符串，例如`hsl(34, 100%, 50%)`，并返回一个包含所有和谐颜色的数组。我们不会处理其他需要转换的颜色格式，因为那是另一篇文章。我们已经有了上面的`parseHSL`函数，所以我们有了一个好的开始。这个函数的关键是我们可以把分裂补码看作`baseHue + 150`和`baseHue + 210`。事实上，所有的多重色彩调和都从一个基础值(在这种情况下是 150)开始，并以一个设定的间隔(在这种情况下是 60)产生更多的`x`色彩(在这种情况下是 1)..让我们到达 210😱).

所以我们开始吧。

```
const rp = 'hsl(270, 49%, 40%)'

function harmonize(color, start, end, interval) {
    const colors = [color]
    const [h, s, l] = parseHSL(color)

    for(let i = start; i <= end; i += interval) {
        const h1 = (h + i) % 360
        const c1 = `hsl(${h1}, ${s}%, ${l}%)`
        colors.push(c1)
    }

    return colors
}

const complement = harmonize(rp, 180, 180, 1)
const split = harmonize(rp, 150, 210, 60)
const triad = harmonize(rp, 120, 240, 120)
const tetrad = harmonize(rp, 90, 270, 90)
const analogous = harmonize(rp, 30, 90, 30) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一些代码，我们可以把它作为一个额外的功能来修饰一个颜色选择器应用程序。

[![](img/ee441d099fb824e44e93bdbd743d38a4.png)](https://camo.githubusercontent.com/5d49cc9403085caf7a6114ee4f2eb3b906045558/68747470733a2f2f73332d75732d776573742d312e616d617a6f6e6177732e636f6d2f636f6c6f722d746f6f6c2d72656d69782f636f6c6f722d746f6f6c2d372e676966)

有各种有趣的结合数学和颜色的功能。很多事情并不像看起来那么复杂。我一直在玩 GIF 中的应用程序，我希望吸引贡献者来帮助测试和改进`macOS`版本，并全面改进产品。谢了。👌