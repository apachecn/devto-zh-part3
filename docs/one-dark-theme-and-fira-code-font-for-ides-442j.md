# 一个黑暗的主题和 Fira 代码字体

> 原文：<https://dev.to/onmyway133/one-dark-theme-and-fira-code-font-for-ides-442j>

[![](img/52a61b9664ecceccd87c0c254de4deab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rnEfzwxd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A8vHNdW2w-vvr-wHPen38Ww.jpeg)

一个好的主题和字体可以让你的开发幸福感增加很多。自从使用了 [Atom](https://atom.io/) 之后，我喜欢上了它的一个黑暗主题。背景和文字颜色刚刚好，赏心悦目。

### 一片黑暗

最初是为 Atom 设计的， [one-dark-ui](https://atom.io/themes/one-dark-ui) 声称可以适应大多数语法主题，与 mozilla 的 [Fira](https://github.com/mozilla/Fira) Mono 字体一起使用。

[![](img/ade17cadef051e3043e11bf9457565a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g7Qxbc8n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3552/0%2A03uqfI0DFKADizkR)

还有很受欢迎的《德古拉》,但是对我来说对比度太高了。

### [庆祝活动](#fira)

最喜欢 [FiraCode](https://github.com/tonsky/FiraCode) 字体，就是好看，支持连字。

[![](img/46785e0ae9d4afd2dbf24415dd841e22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hFCZh0HP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3408/1%2A4iJo0-L4cS1OYl_jd3SSXw.png)

或者，你也可以浏览 [ProgrammingFonts](https://github.com/ProgrammingFonts/ProgrammingFonts) 或者其他连字字体比如 [Hasklig](https://github.com/i-tu/Hasklig) 看看哪个字体适合你。

主题和字体完全是个人口味，但如果你喜欢我，请给 One Dark 和 Fira 一个尝试，下面是如何在 Xcode，Android Studio 和 Visual Studio 代码中做到这一点，这些都是我日常使用的编辑器。

## Xcode

首先，你需要安装最新编译的 [FireCode](https://github.com/tonsky/FiraCode/releases) 字体

我曾经有自己的一个黑暗副本，叫做 [DarkSide](https://github.com/onmyway133/DarkSide) ，这就是我如何学习如何制作 Xcode 主题。目前，我觉得 [xcode-one-dark](https://github.com/bojan/xcode-one-dark) 已经足够好了。Xcode 主题只是一个带有。xccolortheme 扩展，放在~/Library/Developer/Xcode/user data/FontAndColorThemes 中

安装主题后，您应该能够从首选项->字体和颜色中选择它

[![](img/9f378ea5dce8b41fb6be2abb4765fe36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yFg-5TdX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3648/1%2A72XHQXThGhqTVpoOcYmF-g.png)

它看起来像下面。

[![](img/7356be276b27ff6fa731ba07144db8d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q4hbeya7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/7168/1%2AYk1GRdW7ALnU6UcaPyWvRA.png)

## 安卓工作室

安卓工作室默认只有默认和德古拉主题。让我们暂时选择德古拉。希望有一个黑暗的支持。

[![](img/ef88939980f4f52d038cbaa06be0d2b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SZHVT1BT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4548/1%2ASVS1CaXxXH3MvxTLLr4i0w.png)

此外，Android Studio 可以优先选择 Fira 代码，我们应该已经安装了。请记住选择“启用字体连字保持凉爽”

[![](img/56fd32b09f9ab585c13d9bde30af24a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cDOihu6g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4548/1%2AiwKiy_uzCRT5iG9V5luWKQ.png)

它看起来像这样

[![](img/3095d74181103190af287b734d0a5f73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R8stcfLE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/7168/1%2AMXOaGXuT30B4TNvX4xgGEg.png)

## Visual Studio 代码

使用扩展在 VSCode 中安装主题很容易。有这个 [One Dark Pro](https://marketplace.visualstudio.com/items?itemName=zhuangtongfa.Material-theme) 我们可以在 VS 代码中直接从扩展面板安装。或者，你也可以选择 [Atom One 黑暗主题](https://marketplace.visualstudio.com/items?itemName=akamud.vscode-theme-onedark)

[![](img/a45cc2a465d0bad19c551912919aa196.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vzCDF6ml--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/7168/1%2AQ5L_3MNaJlp1Yz6IpmotEQ.png)

然后进入首选项->设置来指定 Fira 代码

[![](img/9932da2cdea8099128b8e9a8a365f399.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S3ayM8SA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/6720/1%2AuVRSZOTCkm1i2VK6CCgXEA.png)

结果应该是这样的

[![](img/ce7422798c288abbbadc3f4793b75c24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Cu7zlUf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/7168/1%2AzqgMt0v_FEGydm67VuUT5w.png)

我希望这对你有所帮助。希望您的代码能够继续编译。

原帖[https://medium . com/fantageek/one-dark-theme-and-FIRA-code-font-for-ides-d 67 bb 2 AC 8 CB](https://medium.com/fantageek/one-dark-theme-and-fira-code-font-for-ides-d67bbe2ac8cb)