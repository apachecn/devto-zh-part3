# 谈 C++:西蒙·布兰德访谈录

> 原文：<https://dev.to/codecademy/talking-c-interview-with-simon-brand-3gma>

Codecademy 的 [Learn C++](https://www.codecademy.com/learn/learn-c-plus-plus) 课程的作者玛丽埃尔和我，最近坐下来和英国爱丁堡的微软 C++开发者代言人西蒙·布兰德视频聊天。Simon 是 [#include < C++ >](https://www.includecpp.org) 的组织者，C++标准提案的作者，也是一个经常参加会议的演讲者。

* * *

**我们向您提出的第一个问题是...是什么让你迷上了 C++？**🐣

我有点不小心掉进 C++了。在大学，我为 OpenCL 做了一些编译工作，OpenCL 是一种用于编程加速器的语言，比如 T2 的 GPU，T4 的 DSP，专用硬件——所有这些都有一个编程模型。与这些编译器一起工作，C++的决定对我来说是一种决定，我对它产生了兴趣，并在一个非常类似的领域找到了一份工作。

我开始阅读博客，观看会议演讲的视频，并决定我要写自己的博客，并参与标准流程，开始自己在会议上发言。所以它就是从那里长出来的。

太棒了。你认为 C++未来会走向何方？

C++标准化是有史以来最健康的，这取决于你如何定义它。在最近一次会议之前，圣地亚哥的[邮件](https://isocpp.org/std/meetings-and-participation/papers-and-mailings)中的论文比 C++历史上任何一次邮件都多 60%。因此，该标准有越来越多的人参与进来，并以前所未有的速度发展。所以它肯定还在上升。对于这样一门古老的语言来说，标准的发展势头是前所未有的。

我认为，就它在行业中的发展方向而言，它将继续在嵌入式领域发展，而嵌入式领域几乎是 C 拥有最长时间的领域。但我认为，随着该领域的开发人员尝试寻找其他地方，或者更多年轻人参与到社区中，并了解其他语言中正在发生的事情，人们将开始更多地关注 C++或 Rust 等嵌入式开发工具。

我认为它还会继续增长。从我看到的数据来看，它的采用率也在全面增长。因此，它将继续被用于金融行业、视频游戏以及所有目前处于市场领先地位的领域。

**有没有推荐给新手入门的测试框架？**

我认为 [Catch2](https://github.com/catchorg/Catch2) 是最好的开始。它非常容易访问，也非常容易集成到您的项目中。我得说，和他一起工作也很友好。文档也非常好。

你用的是什么 IDE(集成开发环境)？

[Windows 上的 Visual Studio](https://visualstudio.microsoft.com) 和其他地方的 [Visual Studio 代码](https://code.visualstudio.com)，当然！在我职业生涯的大部分时间里，我都使用 [Emacs](https://www.gnu.org/software/emacs) 。

> “我认为启动项目的主题并不重要，只要它是你感兴趣的东西。”西蒙·布兰德

在接下来的部分，告诉我们你脑海中闪现的第一件事！

**1。`main()`功能中的`return 0;`？**

我不喜欢。

**2。`using namespace std;`？**

从不在标题中。

**3。变量命名约定？**

我倾向于遵循标准库。几乎所有的东西。`PascalCase`为模板参数。`SHOUTY_CASE`为宏。

> ![Simon Brand profile image](img/945d23941943554f1d1e98fce44cd96d.png)西蒙·布兰德[@ tartanlma](https://dev.to/tartanllama)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)c++变量命名风格:
> 小型大脑:PascalCase
> 大型大脑:camelCase
> STL 大脑:snake _ case
> Bjarne Brain:Bjarne _ case
> 星系大脑:海绵宝宝18:14pm-2019 年 2 月 23 日

**4。STL(标准模板库)里最喜欢的东西？**

哦哦...嗯,`optional`目前是我的事情，因为我为此写了一篇论文。哦，实际上，我会选 [`std::vector`](https://www.codecademy.com/learn/learn-c-plus-plus/modules/learn-cpp-vectors) 。即使它也有一些问题，但它仍然是 C++的主力。

然后是最后一个问题。学习者做什么项目好？比如什么是真正坚实的基础项目？

是的，我认为基础项目的最佳主题就是你感兴趣的东西。它本质上可以是任何东西。比如，如果你对电影感兴趣，想出一些在那个领域行得通的东西。如果你喜欢运动，可以考虑一些模拟的东西。我认为启动项目的主题并不重要，只要它是你感兴趣的东西。

我认为重要的是确保它的作用域很好，这样当你有一个“当我到这一步时，我就有了一些东西，我就能够展示它了。”这可能是一系列的里程碑或类似的东西。但是你心里应该有某种范围。因为否则，有了这些东西，你就开始了，然后[特性蔓延](https://en.wikipedia.org/wiki/Feature_creep)开始了。然后你就失去了动力，转而做别的事情。

如果你想要看得见的进步，试着做一些有范围的事情:明确的里程碑或者你想学习和实践的事情。我认为这肯定比项目本身更重要。

* * *

非常感谢西蒙·布兰德为 C++社区做出的贡献。Simon 对我们的 [Learn C++](https://www.codecademy.com/learn/learn-c-plus-plus) 课程进行了全面的评估，提供了大量的反馈，以确保我们的 C++课程是现代的，在技术上是准确的。在推特上关注他们 [@TartanLlama](https://twitter.com/tartanllama) 。