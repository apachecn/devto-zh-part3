# 一个数字的 UILabel，它可以使值发生变化

> 原文：<https://dev.to/slavenko/a-numeric-uilabel-that-animates-value-change-4hd6>

[![Slavenko Miljic - SMCounterLabel](img/a8e93d9701374cb014336260483dd053.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QBRsEyUG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/slavenko/SMCounterLabel/master/animation.gif)

[https://github.com/slavenko/SMCounterLabel](https://github.com/slavenko/SMCounterLabel)

这是我的一个有趣的项目，我开始做这个项目是因为我想在我当时做的一个项目中使用类似的东西。你可以在许多金融应用的模型中看到这种 UI 交互，但我找不到任何类似的已经编码的东西(至少对于 Swift 来说)，所以我必须自己制作。

它的基本工作原理是为字符串中的每个字符创建一个单独的标签，根据选定的字体计算其位置，然后分别为每个标签制作数字变化的动画，直到它达到所需的值。

把这个放在这里，也许有人会觉得有用。

我们非常欢迎所有的建议和批评。快乐编码。