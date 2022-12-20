# 所以你编写了回归程序

> 原文：<https://dev.to/jessekphillips/so-you-programmed-in-a-regression-5f1e>

你有过一个完美的功能，但现在它变成了一堆垃圾，隐藏在一个 bug 中？好吧，这是苛刻的，重点是这将是很好的知道什么变化引入了行为差异。

Git 二分执行提交历史的二分搜索法。首先找到一个已知的工作提交，然后使用二等分来管理代码签出，以找到后续提交。

通过跟踪一个交流的提交历史，每次提交都应该被编译，你会被告知当时请求了什么。

请注意，这些工具并不是第一件要用的东西，但是当问题没有直接指向时，它们是追踪事情的有效方法。D 社区提供了一个额外的工具，通过提供一个识别失败的命令来追踪编译回归。Git 二等分被执行来自动通过编译器构建(从提交)到达引入提交。

[![easyaspython image](img/c6cb3bb145c8475ff88f9b76abb5e0f0.png)](/easyaspython) [## 用 git-等分搜索 bug

### 戴恩·希拉德 1 月 13 日 193 分钟阅读

#git #bugs #softwaredevelopment](/easyaspython/hunt-bugs-with-git-bisect-l44)