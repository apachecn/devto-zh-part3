# 满足您的需求

> 原文：<https://dev.to/timdeschryver/bending-tslint-to-your-needs-36lj>

在阅读了另一个询问项目中使用的 RxJS 操作符数量的 twitter 帖子后，我对自己说，创建一个小工具来做这件事可能是一个有趣的小项目。

经过一段时间思考完成这项任务的最佳方法后，我把任务分成了几个小部分，让自己更容易完成。我不得不:

*   迭代每个文件
*   搜索 RxJS 运算符
*   记数
*   打印出结果

记住这些较小的任务，我注意到 TSLint 可以用于前两个任务，为我完成大部分繁重的工作。这派上了用场，因为我还想尝试一下 TSLint 和 AST，或者抽象语法树，真是一举两得！

这篇文章详述了我创建这个新工具的过程，我称之为 rxjs-operator-counter。

[阅读更多...](https://blog.angularindepth.com/bending-tslint-to-your-needs-6ae0a51e633)