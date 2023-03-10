# 务实的程序员——第 6 章注释和问题

> 原文：<https://dev.to/cseeman/the-pragmatic-programmer-chapter-6-notes-and-questions-4c02>

所以我的工程读书会继续学习《实用程序员》的第 6 章。一路上，我们有过一些停停走走。对于第 4 章和第 5 章，我们结束了对周的梳理，因为我们没有人真的想讨论第 4 章(实用主义偏执狂)。我们确实不得不从上周开始移动第 6 章，因为那是在 sprint 接近尾声的时候，我们的许多成员想要努力完成一些 sprint 承诺，并在他们的故事上获得更多的工作时间。

这可能是做工作读书会最难的事情之一，很容易推后章节。我们可能已经这样做了 3 次左右，这确实增加了完成一本书的时间，而且似乎降低了动力。

和往常一样，我使用了来自棒极了的 [Dev 同理心读书俱乐部](https://devempathybook.club/)的问题。如果你感兴趣，他们现在正一头扎进*火箭手术制造的简易*。

[![](img/7af1c85478b96387a398f0c95ff1b2ba.png)](https://i.giphy.com/media/3oKIPnAiaMCws8nOsE/giphy.gif)

编写代码时，请注意第 6 章

*   巧合编程，不要仅仅依赖于可以工作但你不明白为什么会工作的代码，要有意识地编程
*   通过意识到你在做什么，思考，只依赖可靠的东西和记录你的假设来有意识地编程。
    *   测试您的代码和假设
    *   不要成为历史的奴隶，现有的代码应该支配未来的代码。
*   算法速度，大 O 符号
    *   只是另一种估算，用算法来估算
    *   《出埃及记》用字符串(n)排序，解密一个消息，这些输入的大小会影响算法。
    *   输入越大，运行时间或内存越长
    *   线性–O(n)时间与 n 值成正比增加，顺序搜索，简单循环
    *   constant–O(1)，访问数组中的元素，简单语句
    *   O(lg(n))对数，二分搜索法，二进制斩波
    *   O(n lg(n))比线性差，但差不了多少，快速排序，堆排序，分治
    *   平方律，选择和插入排序
    *   O(n)立方，2n×n 矩阵的乘法
    *   O(cᴺ)指数，旅行推销员，集合划分
    *   测试您的估计，最好的并不总是最好的，选择适合您的数据的算法
*   重构，当你看到重复、非正交设计、过时的知识和性能问题时，重构代码。
*   使代码易于测试，单元测试是一种测试模块和测试合同的测试
*   不要使用你不理解的向导代码

第 6 章的问题

*   在关于优化(算法速度)的一节中，作者警告不要过早优化，因为通常一个低性能的实现“将花费你更少的时间来编写和调试”，除非必要，否则你不想投入时间。我们可能会说，我们不是针对 CPU 周期进行优化，而是针对程序员周期进行优化。你认为在软件开发中最需要优化的是什么？
*   测试更多的是文化上的，而不是技术上的；不管使用什么语言，我们都可以将这种测试文化灌输到项目中。如何在一个团队中灌输一种强大的测试文化，或者任何编码实践，而这个团队没有把它作为日常工作的一部分，甚至可能不相信它？你是经理、技术主管还是开发人员重要吗？
*   作者并不反对框架，而是反对使用你没有编写和不理解的自动生成的代码。有不懂的代码会有什么危险？有合适的数量吗？多少才算多？