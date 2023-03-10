# ConstraintLayout 初学者指南第一部分:为 Android 应用程序设计个性化用户界面

> 原文：<https://dev.to/miguelrodoma95/constraint-layout-beginners-guide-part-i-designing-a-repsonsive-ui-for-android-apps-57ic>

由于我一直在学习如何为 Android 开发移动应用程序，我想写一些关于我遇到的一些有用的事情的帖子。它有助于我组织我的想法，并且对刚开始 Android 开发的开发人员很有用。

在这篇文章中，我将谈论 ConstraintLayout 的优点，这款工具彻底改变了我为 Android 应用程序设计视图的方式。我将介绍的一些功能包括:

*   约束布局概述
*   中心视图
*   链式视图

在第二部分，我将确保涵盖*指南*和其他特性。

# 什么是 ConstrainLayout，我为什么需要它？

### 有许多采用不同屏幕尺寸的 Android 操作系统的设备，这对开发者来说是一个问题...

众所周知，Android 操作系统可以在各种各样的智能手机上运行。根据 [Statista 关于操作系统全球分布的文章](https://www.statista.com/statistics/266136/global-market-share-held-by-smartphone-operating-systems/)世界上大约 85%的智能手机运行 Android，并且有数千种(可能数百万种)不同型号运行该操作系统。

这给 Android 开发者带来了一个众所周知的问题:构建一个能在尽可能多的设备上流畅运行并且看起来不错的应用。

有很多方法可以解决这个问题。Goolge 在 2016 I/O 中发布了 Constraint Layout，这种方法已经被开发人员广泛推荐用于设计响应式 UI。

### 什么是约束布局？

正如[官方 Android 开发人员文档](https://developer.android.com/training/constraint-layout/)所述，ConstraintLayout 允许开发人员使用平面视图层次结构创建复杂的布局，这意味着不需要使用嵌套视图组，这有助于提高构建效率，减少内存使用，并且设备创建视图所需的时间更少。

如果使用正确，ConstrainLayout 还可以简化设计视图，使其在屏幕处于*横向*模式时看起来不错。这都是关于知道如何将视图与它们的父母和兄弟姐妹联系起来。

在本教程中，我将介绍 ConstraintLayout 使用 Android 的 [LayoutEditor](https://developer.android.com/studio/write/layout-editor) 提供的一些主要功能。

要访问布局编辑器，请单击“设计”选项卡。
[![Editor tab](img/546ac1c1e8074449989d73746e051759.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--vYlfvtL_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pu0223s5qmfrvsld7m31.PNG)

### LayoutEditor 概述

[![Overview](img/7bb23c4724db2c38e6090b4c128e7664.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ujtoiKX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ncdkqpjnl9ft7afzdnr7.jpg)

现在您已经在 LayoutEditor 中，您可以看到我已经标记了一些最重要的部分。从左到右你可以看到:

- **面板**:面板包含了布局中可能需要的大部分视图。要添加一个视图(这样一个按钮)，只需在*面板*中找到它，并将其拖到您的*组件树*中。对于其他一些视图(来自您添加的库的示例视图)，您可能需要转到 *Text* 选项卡(xml)并手动添加。

- **组件树**:组件树显示了你的嵌套布局的层次结构(如果需要的话)，每个布局内部的视图，视图的 id 和一些其他的特性，比如*指南*或者*障碍*(我们将在后面讨论)。

- **你目前使用的设备**:我认为这很重要，因为有时候你想看看更大或更小的智能手机的布局是如何变化的。LayoutEditor 为此提供了一些选项。在左边，你可以选择将屏幕方向改为*风景*。

- **属性**:在这个部分你定义属性(如背景、颜色、风格等。)中，就像在 xml 中一样。要访问更多属性，请单击右侧的双箭头图标。*属性*的另一个重要部分是带点/线的正方形。您将使用它来定义所选视图的*约束*和边距。

# 约束布局特征

现在我们已经找到了我们的工具，是时候进入我们可以用 LayoutEditor 和 ConstraintLayout 做什么了。

首先要注意的是出现在每个视图顶部、底部、左侧和右侧的点。通过点击它们并拖动鼠标，您可以连接到另一个视图来构建您的布局。

[![Boundaries](img/a4e50a8f9eb51cfb2fd77842ef297aac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5nVHdTV0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/re3vebecryv82d5wt4tb.PNG)

### MatchConstraint，WrapContent 或 Fixed

使用 ConstraintLayout 时，您可以决定视图边界(宽度和高度)的定义方式。我们可以通过单击代表我们的视图的正方形内的图标(可能是箭头、直线或之字形线)或简单地通过编辑高度和宽度属性来编辑它。从左至右:

[![Boundaries](img/c5a8dafc34fc06ab8e13c3b65f69ab67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d2rYWzSW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sae2loda62vekc21psx0.jpg)

**匹配约束**:匹配约束将你的视图拉伸到它的连接。如下图所示，我已经将 textView 连接到 textView2，并选择了 *match_constraint* 作为 textView 的宽度。在第二张图片中，我在文本视图的右边添加了“24”的边距。

[![MatchConstraint](img/42316060a04c53df2341525403a45936.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RfATsxaG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ht7svcuyljelhsmbj8l9.jpg)

请记住，布局中的约束越多，响应就越快。

**Wrap Content** :大多数视图组的公共属性。这仅仅意味着视图组的大小与其内容相同。如果我们在谈论一个文本视图，宽度将和文本一样长。

**固定**:通过选择*固定*，你可以定义视图的宽度和长度。建议使用 *dp* over *px* 或中的*作为单位，因为它考虑了智能手机屏幕的密度，并使视图适应它。通过简单地编辑高度和宽度属性来设置它。*

**注意**:注意你可以为高度和宽度选择不同的选项，例如，你的宽度可以由 *MatchConstraint* 定义，而你的高度使用 *WrapContent* ，就像在任何其他视图组中一样。

### 居中一个视图

我们可以通过右键单击并选择居中选项来水平或/和垂直居中。
[![Center View](img/70de234d965fce687742a192e2354841.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--5v1dYdIM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1yv9iuox61t0pwyhxqzt.PNG)

你也可以将一个视图居中，考虑到连接的视图，例如:
[![Center View](img/d6418b883c9dc9cc031d6b0746c7ffa3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vaM-TOx3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hxeqnaa7euvoxfce3krk.PNG) 
在这个图像中，左侧的纹理视图水平居中，考虑到左侧约束(父)和右侧约束与纹理视图 2(兄弟)。我还在两边加了 16 的余量。

### 连锁视图

当你有几个连续的视图时，使用*链*会很有帮助，例如，当你的布局末端有三个按钮时，或者当你的应用程序中有一个用户注册的片段或活动时。通过在组件树中选择想要链接的视图，右击并选择*链*，可以使用垂直链或水平链。

您可以用 4 种不同的方式来设置视图链的样式:

*   传播
*   在里面传播
*   衡量过的
*   包装

**展开**:这是选择链选项时的默认样式。首先，我将三个按钮垂直居中，然后选择水平链选项。
[![Overview](img/76e64034e1df7059dad29c707e50ecae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cmgjQraN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sez4pk0rig8zy8lyx70f.png)
[![Overview](img/bbd6d578d1591604186f225d8b2c4989.png)T9】](https://res.cloudinary.com/practicaldev/image/fetch/s--4g-i9N07--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tok90zls3pstpoz0k1o7.png)

**在**内展开:要使用此选项，您需要直接从 xml 编辑“layout _ constraint horizontal _ chain style”的属性。这是对我们的按钮应用 spread_inside 水平链的结果:
[![Overview](img/3c05b82ad43ce393f39604ee651bcca7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2zJs74Ia--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9kyl8e41m4qhxm334q2m.PNG)

**加权**:当您选择 *spread* 或 *spread_inside* 作为样式时，您可以通过编辑每个视图的‘layout _ constraint horizontal _ weight’属性来设置权重。它就像 LinearLayout 中的“layout_weight”一样工作。为了实现这一点，您必须将*宽度*属性设置为**匹配约束**。
在这张图片中，我已经将所有按钮的*宽度*设置为**匹配约束**，并且我已经为第一个按钮添加了. 5 的*布局约束水平权重*，为其他按钮添加了. 25，注意分布是如何变化的。
[![Overview](img/827ce6838f55fd49fb959a9daae18bfc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T6br76An--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x0cxmvqb8go6huas2mw6.PNG)

**打包**:要打包你的视图，你需要编辑“layout _ constraint horizontal _ chain style”的属性(就像 spread_inside 一样)，并将其设置为打包。这就是打包的链接视图的样子:
[![Overview](img/2cd713f83b820ae72ce6f4f6b809bc47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VR-VRof---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iyqqj96e41jtjt27opwz.PNG)

这个帖子到此为止！任何来自更有经验的开发者的反馈都是受欢迎的。我会确保上传更多功能的第二部分，以及其他 Android 开发主题的简短教程。

快乐编码。