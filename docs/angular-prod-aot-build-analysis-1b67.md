# 角度产品/AOT 构建分析

> 原文：<https://dev.to/subodhkumares/angular-prod-aot-build-analysis-1b67>

大家好，

我们正在制定一个解决方案，其中涉及到大中型项目。
在这个过程中，我们发现了很多关于棱角分明的 AOT/Prod Build 的东西。我们确实花时间浏览了 Webpack 配置&捆绑包分析器。

我们很想知道，当涉及到大中型项目的 Angular 7 Prod 构建时，世界是如何处理以下几点的。

**1。减少构建时间的方法(任何特定的 Webpack 配置或正在使用的其他构建策略)**

**2。分析 Treeshaking 输出(内部代码和第三方库代码)的方法，因为我们看到许多库代码包含在 bundle 中，尽管没有在应用程序中使用(我们确实使用了 bundle analysers)。**

任何信息或反馈都是最受欢迎的！谢谢