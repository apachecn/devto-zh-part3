# 雪球效果设计

> 原文：<https://dev.to/thefern/snow-ball-effect-design-12na>

我想谈谈我称之为雪球效果设计的东西。是的，我只是瞎编的。归结起来就是当我有了一个软件项目的想法时，我是如何试水的。对于我的例子，我将使用“可视化绘图”工具，因为这是我最新的项目。

## 为什么我需要可视化绘图工具？

在我的工作中，有大量的数据分析。数据可以来自不同的点、csv、微控制器等。看 csv 数据很糟糕，我的大脑无法处理电子表格中的数字，也许有些人可以。我需要看到一个视觉情节，让我的大脑真正处理正在发生的事情。

## 核心需求

*   **实时显示数据**在 1-16 之间的任何地方显示数据，超过 1-16 的数据很难分析，尽管并非不可能。尽管理想情况下，我们仍然可以提取 16 个以上的数据，比如说 32 个。
*   **能够连接不同类型的控制器**处理不同的驱动，即 arduino、raspberry pi、allen bradley、西门子、欧姆龙、csv 等。
*   **写入/读取绘图数据**

## 其他要求

一开始不需要的东西的清单。换句话说，很好的特性，但不是运行程序所必需的。

*   用数字读数显示绘图数据
*   基于表达式显示警报列表
*   自动缩放地块线
*   将触发器设置为开始/停止绘图

## 试水

此时，您将为项目决定一个堆栈。我在这里不严格地使用 stack，但是我的意思是你选择一种编程语言，库/框架等等。任何你需要编译和运行功能原型的东西。如果是一个网站，那么它可能是 apache、php、mysql 或 node.js、npm 模块、javascript 之类的东西。你明白了。

在绘图工具的例子中，我选择 python 作为我的理想语言来测试 waters，因为它可以快速地建立任何东西的原型。我遇到的最大障碍是找到一个好的 python 库来绘制情节线。Matplotlib 对我来说太大了，需要其他模块。最终给项目带来了太多的依赖。一旦我找到一个好的绘图库，我需要为 python 做绑定，为 Python 设置 Qt\SIP。不用说，这是很难得到一个项目，甚至设置。我花了很多天，很多天，直到我扔掉失败的白旗。Qt 看起来很有前途，但是有太多的移动部件来设置一个最小的项目。我在 Windows 和 Linux 上进行了尝试，只是为了进行比较。在 Linux 上稍微容易一些，但是我在 SIP 上仍然有很多问题。

*   QT
*   编译程序
*   环境路径变量
*   啜饮
*   为 C++库生成绑定

我重新开始，这太麻烦了。我甚至还没有开始浏览 SO 和 QT 手册。我转向了我所知道的另一种主要语言，Java。原来 Java 有 GUI 项目的 JavaFX 库，也支持绘图，它可以很好地与 Java 集成，而不必安装一千个东西。更不用说 JavaFX 已经开源了一段时间，而且有一个很大的社区。我不到 10 分钟就完成了我的项目。

## 雪球 1kg - GUI 窗口

首先要测试的是用 JavaFX 生成一个窗口 GUI。我转向在线示例，很快就有了一个带按钮的工作 GUI。

## 雪球 2kg -绘制线条

有一个 JavaFX 图表 API 绘图的工作示例。事实证明，JavaFX 有一个很好的 Ensemble.jar，其中有多个 GUI 示例和源代码。我修改了源代码，添加了第三条情节线。

## 雪球 3kg -连接控制器

确保有到控制器的连接，并且设备工作模式读取数据。

[![enter image description here](img/83ce1b74d480a49b72b6bb02f6a3b4fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YKDUVoRM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jv7s6r570hotdjsazemo.jpg) 
[图片来源:Praxisociety](http://praxisociety.com/2016/06/knowledge-base/loa-rethinking-snowball-effect/#.XNa4FY5KiM9)

## 结论

我不会用很多雪球来烦你，没有双关语的意思。从中吸取的主要教训是，当你开始一个大项目时，它会变得势不可挡。旅程的一部分就是很好地分解它。在研究部分，你可能不知道怎么做。不，让我换个说法，会有一些你不知道的东西，很可能有人已经做过了，并且有希望在网上记录下来。试着先单独得到一个工作实例，直到你掌握了它，因为如果你试图一次结合所有的需求，它会变得复杂，最终会减慢你的速度。

在我的绘图例子中，我从未做过绘图工具或从 Java 连接到 arduino，但是一次解决一个需求给了我快速推进项目的动力，因为有时不容易看到隧道尽头的亮光。

雪球设计的核心特征，是在项目的每一步中让雪球越滚越大。顺便说一下，雪球的设计是松散地模仿瀑布。雪球设计更多的是关于项目的开始和测试想法，它不一定是项目中的主要里程碑，如实现、测试、部署等。

*   你如何处理最初的项目研究？
*   你是如何进行初始设计的？
*   在你的工作中，最常用的软件开发生命周期是什么？