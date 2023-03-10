# 前端指标:CSS 特异性

> 原文：<https://dev.to/wuz/frontend-metrics-css-specificity-288p>

我一直致力于发现和跟踪有用的前端指标，我想分享一些我学到的东西。随着我对它们了解的越来越多，这可能是一个关于所有前端指标的随机系列。

# CSS 特异性到底是什么？

[Emma]总结了什么是 CSS 特异性，并且做得比我好得多！

## 文章不再可用

简单回顾一下，CSS 特异性涉及级联样式表的“级联”部分。CSS 选择器的特殊性决定了应该给一个元素赋予哪种样式。良好的特异性会让你不需要太多的。

在编写 CSS 时了解这一点很重要，而且随着时间的推移跟踪这一点会更好！对于我的日常工作，我们一直在收集一些前端指标，以便我们可以看到我们的工作如何影响应用程序。我们正在跟踪的一件事是 CSS 特异性在整个文件中的样子，它被称为特异性图。

# 好的特异性图是什么样子的？

[![A CSS specificity chart](img/920fca21850bb899271d8fcbc4f67fbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---x3boBD4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cw4sfz5gg38sr2ptjelv.png)

一个好的特异性图有几个值得注意的属性。首先，沿着`y`轴绘制选择器的特异性，沿着`x`轴绘制选择器在 CSS 文件中的位置。理想情况下，你的特殊性应该是曲棍球棒-对于文件的大部分，它应该保持稳定，任何主要的特殊性峰值应该限制在最后。

# 你是如何把你的 CSS 特异性图形化的？

我已经在我的公司[元数据库](https://metabase.com)中设置了我正在研究的指标，但是我构建了一个小工具来查看你的特性图。这段代码并不难理解。尝试一下，然后检查一下`getCSSStats.js`文件。它使用 [cssstats](https://npmjs.com/package/cssstats) 来获取一大块 CSS 的统计数据。

[https://glitch.com/embed/#!/embed/mud-leather?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/mud-leather?previewSize=100&path=index.html)

通过在对 CSS 进行更改时监视图中的变化，可以确保您处于正确的轨道上。这不是一个一成不变的规则——很多时候你需要某种特定性——当使用`scss`或`less`导入时很容易被打破。然而，当用作快速健康检查时，一个好的特异性图会非常有用！

点击阅读更多关于特异性图[的信息](https://csswizardry.com/2014/10/the-specificity-graph/)

祝你一周愉快，德夫斯！你跟踪哪种度量标准？你对 CSS 特异性图有什么看法？