# 关于我的第一个移动应用程序的日记

> 原文：<https://dev.to/voidjuneau/diary-about-my-first-mobile-app-2lc9>

*看到“您没有任何设备”的标志？不，我没有。我对此感到有点难过。😢
这篇文章是我用 Android Studio 制作第一个移动应用的个人记录。我想这也是我第一个严肃的项目。我想我可以用这种方式来解决这个问题，因为我花了 25 美元在 Google Play 上注册了一名开发者。*

1.  结局
    [这个](https://play.google.com/store/apps/details?id=im.juneau.discretemathformulas)就是结局。

    [![Screen shot of my app](img/4dd440b26eb048d57046e09a68ae01bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1rAfBR45--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mvgoauszu8tq3stgwvp6.png)

2.  动机
    首先，我想做一个类似于 [z-table 计算器](http://davidmlane.com/hyperstat/z_table.html)或[矩阵计算器](https://www.symbolab.com/solver/matrix-calculator)的东西，甚至尝试过做一点，但是我花了几个星期才发现这超出了我目前的能力。

    也许不是不可能。我知道我应该在我的舒适区之外探索，但这更像是生物危害区，而不仅仅是舒适区之外。是的，如果我准备好合适的设备，这可能不是不可能的，但可能需要几个月的时间，我只是想测试一下我现在能做什么，用一个可行的概念，而不是太多极端的额外工作。

    所以，我决定做一个公式页 app。起初，我认为我应该为可汗学院的预微积分做这个，我想，我甚至可以为我自己学院的离散数学和统计课做这个。

3.  图书馆

    *   [Katex](https://github.com/lingarajsankaravelu/Katex) :在 android 中显示 Latex。
    *   [YouTube Android Player API](https://developers.google.com/youtube/android/player/) :播放 YouTube 视频。
    *   [PhotoView](https://github.com/chrisbanes/PhotoView) :用于 z 台/ t 台图像的捏放放大。是的，我想要的不仅仅是图像，但是图像现在已经足够了。
4.  前一个项目的教训

    *   先计划:当我做最后一个项目时，在我几乎完成最初的功能后，我意识到有太多的重复，所以我不得不完全重构几乎完全重新设计整个结构。
    *   使用继承:从上一课中，我发现先制作一个框架并利用它是很方便的。它与 Android 应用程序配合得非常好，因为它都是关于不同的活动和片段以及传递内容的意图。
5.  吸取的教训

    *   使用开源自定义库:是的，说实话，我以前没有做过。
    *   用 document 学习不同的组件:由于我所学的教程没有涉及片段，所以我必须自己学习。有趣的是，我甚至让它工作，但没有使用它，因为它不像是一个适合我想要的功能的地方。但是官方文档真的很可爱。
    *   编写 Latex 语法的能力:奇怪的是，这似乎是改进最多的技能。我以前在 Khan Academy 和 math.stackexchange 中使用过 Latex，但从未如此频繁地使用它。我现在觉得很舒服。
    *   好好阅读文档:在 Latex 中，我没有阅读完整的支持表，所以我不知道我可以用一个定制的标记来对齐方程，所以我必须在开头插入一串空格。此外，我没有在它的页面上看到“尝试”部分，所以我与其他 Latex 编辑器进行了斗争，因为支持的表达式不同。
    *   做笔记很重要:如果我在学习的时候没有总结所有的公式，我甚至不能决定这样做，因为毕竟我没有数据可以输入。
    *   永远不要更改 Android 应用程序和公司的名称:重构应用程序名称和公司名称真的很痛苦，因为内置的重构功能有时并不支持每个文件。
6.  我想在未来开发的应用
    我会毫不犹豫地说 [Z-table 计算器](http://davidmlane.com/hyperstat/z_table.html)和[矩阵计算器](https://www.symbolab.com/solver/matrix-calculator)。我真的希望有一天我能做到这一点，不管是用 Android 的 Java，还是别的什么。