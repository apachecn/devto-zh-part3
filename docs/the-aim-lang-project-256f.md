# AIM-lang 项目

> 原文：<https://dev.to/areknawo/the-aim-lang-project-256f>

把这篇文章当作一篇介绍性的短文。所以，最近我想到了一个创造一种**编程语言**的想法。看起来很容易，不是吗？😅可能吗？-是的。值得吗？-算是吧。你看，通过创造一种语言，你可以学到一些你用别的方法学不到的概念。这只会让你成为一个更好的程序员**。而且，很好玩。😉所以，事不宜迟，让我们**创造一种编程语言**！**

 *** * *

[![person writing bucket list on book](img/d71ed9c00d9c6bc9443e100024fd50aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8j1LtNmA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1484480974693-6ca0a78fb36b%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 照片由[格伦·卡斯滕斯-彼得斯](https://unsplash.com/@glenncarstenspeters?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

# 让我们做好准备！

嗯，我认为这将是一个有趣的(相当长的)系列。请记住，即使这将是一些新的经验，甚至对我来说。和很多人一样，我只是一个没有更深语言创作知识的普通人。我只是计划从无到有(或者从网络上的其他来源)创造一些东西。现在，一些基本信息。我要制作的语言将被命名为 **AIM** (因为我正瞄准我的目标创造一种语言，这是一个很好的捷径)。它应该是多范例、静态类型、编译语言。为此，我将使用 [**、LLVM**](http://llvm.org/) (稍后将详细介绍)和 [**Node.js**](https://nodejs.org/) 。你可能知道，编程语言和类似的低级语言是用 C/C++完成的，所以我用 node . js——这个不寻常的项目是用不寻常的工具完成的。此外，与 C/C++相比，我对 Node.js 更有经验。👍

所以，如果你准备好了，让我们首先创建一个待办事项列表:

1.  找个很酷的名字(搞定！)😀
2.  创造/想象语言的语法。
3.  创建 lexer。
4.  创建解析器。
5.  创建编译器。

这些是最基本的，也是绝对需要的步骤。接下来，在没有太多顺序的情况下继续前进:

*   标准库实现
*   运行时库实现
*   自托管/编译器引导
*   规格/文件
*   示例/其他库/第一个应用程序

现在，让我们回到有序列表，花一两秒钟讨论每一点。此外，我将在下面的帖子中讨论每一点，所以我会很简短。

* * *

[![laptop computer showing codes](img/1ff005066a7ac28bd0cbc18606044cd3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XX6NWmeW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1509966756634-9c23dd6e6815%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 照片由[费边·格罗斯](https://unsplash.com/@grohsfabian?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

# 创建语言语法

语法和它的一般概念是设计语言时最重要的因素之一。许多编程语言都有相似的语法。它们基于已经被证明有效的东西，这很好。这是你在创建自己的编程语言时应该考虑的。但是，由于我并不打算为通用目的创建这种特殊的语言(但是谁知道呢😂)我很可能不会为了创造新鲜感而遵循这个建议。当然不会像[这个](https://en.wikipedia.org/wiki/Brainfuck)那么激进，所以不要慌，只是有一点点不同。尽管如此，我不知道它到底会是什么样子，但我肯定它不会那么标准。🤔

# 创建 lexer

这里没什么特别的。lexer 只是一个软件，它获取您的代码并创建一系列带有附加元数据的标记。至少我现在知道的是这样。当然，对于语言发展的每一步都有一个发布计划，所以要有耐心。

* * *

# 创建解析器

解析器比词法分析器重要得多。解析器从词法分析器创建的标记列表中创建所谓的 AST(抽象语法树)。它基本上是以数据树的形式表示解析后的代码，允许以编程方式与之交互。因此，它的快速和良好的设计是很重要的，因为它将用于像棉绒，漂亮的打印机等等。一般来说是通过软件来直接与你的语言的语法进行交互。此外，AST 是在将语言编译成机器代码表示的阶段使用的。这就把我们带到了最后，也是最重要的一步...

* * *

# 创建编译器

这就是所有编程语言(当然是编译语言)的目的——能够执行。编译器只是获取你的代码并输出(通常是)机器码。我已经决定在 **LLVM** (低级虚拟机)的帮助下实现编译器，所谓的编译器基础设施库。这个软件已经被用来创建像(最显著的) [Rust](https://www.rust-lang.org/) 和 [Swift](https://swift.org/) 这样的语言，以及最流行的 C/C++编译器前端之一- [Clang](https://clang.llvm.org/) ，所以它对于这个项目来说一定足够好。😉显然，使用 LLVM 的 C API 生成机器码要比使用汇编容易得多。尽管如此，它仍然非常庞大和复杂，所以我将花相当多的时间来阅读它的文档，这是一篇写得非常好的文档。这里可能需要 Node.js C 绑定。

* * *

[![woman sitting on grey cliff](img/4dacfa97a2f802f9cf6017856db46d36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sDqd31s1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1523975864490-174dd4d9a41e%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 照片由[弗拉德·巴加西安](https://unsplash.com/@vladbagacian?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

# 旅程开始...

所以接下来的事情就交给你了。我不会讨论其他的问题，因为只有时间会告诉我们未来会发生什么。在这个小小的介绍之后，我希望你和我都会喜欢这个旅程。再次提醒，请记住我不是专家，我只是分享我自己的经验，所以如果你有任何提示给我，我将不胜感激。现在，下一个帖子是关于这个创造的语法，所以如果你想在 Twitter 上关注我的任何更新。再次感谢您阅读这篇介绍，让旅程开始吧...👍**