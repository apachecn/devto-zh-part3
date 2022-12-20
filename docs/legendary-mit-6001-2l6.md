# 传奇的麻省理工学院 6.001

> 原文：<https://dev.to/stereobooster/legendary-mit-6001-2l6>

[https://www.youtube.com/embed/2Op3QLzMgSY](https://www.youtube.com/embed/2Op3QLzMgSY)

> 对这个行业来说，计算机科学是一个可怕的名字。首先，它不是一门科学。它可能是工程，也可能是艺术，但我们实际上会看到，计算机所谓的科学实际上与魔法有很多共同点……所以它不是一门科学。它也不是真的非常关于计算机。它与计算机无关，就像物理学与粒子加速器无关，生物学与显微镜和培养皿无关。它与计算机无关，就像几何学与使用测量仪器无关一样。
> 
> —哈尔·阿伯尔森

## 简介

1980 年，哈尔·阿伯尔森和杰拉德·让伊·萨斯曼在麻省理工学院开设了一门课程。后来，在 1985 年，基于这个课程，他们写了一本书——计算机程序的结构和解释或 SICP 或向导书。这门课在麻省理工教了 N 代程序员。

以下是人们对 SICP 的评价:

> 这是计算机科学的伟大经典之一。15 年前我买了第一本书，我仍然觉得我没有学到这本书要教的所有东西。
> 
> — [保罗·格拉厄姆](https://www.amazon.com/gp/customer-reviews/R3G05B1TQ5XGZP/ref=cm_cr_srp_d_rvw_ttl?ie=UTF8&ASIN=0262011530)

彼得·诺威格，谷歌研究员。

> 那些讨厌《SICP》的人认为，就阅读时间而言，它没有提供足够的提示和技巧。但是如果你像我一样，你不会再寻找一个技巧，而是寻找一种方法来综合你已经知道的东西，并建立一个丰富的框架，在这个框架上你可以在职业生涯中加入新的知识。这就是 SICP 为我做的。1982 年左右，我读研究生时读过这本书的初稿，它改变了我对自己职业的看法。如果你是一名有思想的计算机科学家(或者想成为一名)，它也会改变你的生活。
> 
> [彼得诺维](https://www.amazon.com/gp/customer-reviews/R403HR4VL71K8/ref=cm_cr_srp_d_rvw_ttl?ie=UTF8&ASIN=0262011530)

## 试试看

目前，这门课程的所有材料都可以在网上免费获得。所以可以按照自己的节奏去尝试。

根据[官网](https://download.racket-lang.org/)的说明安装球拍。我尝试用软件包管理器安装，但是我只能安装 CLI 版本，而不能安装桌面版本。CLI REPL 使用 Emacs 编辑器，这可能是一个新手的障碍。除非你已经知道如何使用 Emacs，否则我建议你使用 GUI 版本的球拍。

按照手册中的[说明安装方案中的 SICP 方言。](https://docs.racket-lang.org/sicp-manual/)

你可以在这里观看所有的讲座。

可以在线或者[下载 pdf](http://web.mit.edu/alexmv/6.S184/sicp.pdf) 看书[。](http://sarabander.github.io/sicp/)

全部资料都在麻省理工学院官方网站上。

## 他们为什么停下来教它？

为了画面的完整，我需要说的是[他们在 2008 年停下来教 SICP(？)](https://mitadmissions.org/blogs/entry/the_end_of_an_era_1/)。

苏斯曼是这样描述他的课程的:

> 苏斯曼指出，工程师现在例行公事地为复杂的硬件编写代码，他们并不完全理解(而且由于商业机密，往往无法理解。)在软件层面上也是如此，因为编程环境由具有巨大功能的巨大库组成。据苏斯曼说，他的学生花了大部分时间阅读这些图书馆的手册，以找出如何将它们缝合在一起以完成工作。他说今天的编程“更像科学。你拿起这个图书馆，戳它。你写程序戳它，看看它做什么。你说，‘我能调整它来做我想做的事情吗？’".SICP 的“综合分析”观点——用更小、更简单的部分构建更大的系统——变得无关紧要了。如今，我们通过戳来编程。
> 
> — [为什么麻省理工学院不再教 SICP](https://web.archive.org/web/20160504164044/http://www.posteriorscience.net/?p=206)

来自 Hickey 的类似见解:

> 当然每个人的体验会不一样。这是我的 2 美分:
> 
> 我不认为《SICP》是一本关于编程语言的书。这是一本关于编程的书。它使用 Scheme，因为 Scheme 在许多方面是一种原子编程语言。Lambda 演算+TCO for loops+Continuations for control abstraction+syntax abstraction(macros)+需要时的可变状态。它非常小。这就足够了。
> 
> 这本书真正探讨了编程中的问题。模块化、抽象、状态、数据结构、并发性等。它提供了通用调度、对象、并发性、懒惰列表、(可变)数据结构、“标签”等的描述和玩具实现，旨在阐明这些问题。
> 
> Clojure 不是一种原子编程语言。我太累/老/懒了，不能用原子编程。Clojure 提供了通用调度、关联映射、元数据、并发基础设施、持久数据结构、惰性序列、多态库等的生产实现。Clojure 已经提供了一些更好的实现，这些实现是你跟随 SICP 一起构建的。
> 
> 所以 SICP 的价值在于帮助你理解编程概念。在我看来，如果你已经理解了这些概念，Clojure 可以让你更快地开始编写有趣且健壮的程序。我不认为 Clojure 的内核比 Scheme 的大多少。阴谋家是怎么想的？
> ...
> 去 Clojure 的路上学习 Scheme 或者 Common Lisp 都可以。将会有无法翻译的细节(来自方案-无 TCO，false/nil/()差异，无延续；来自 CL - Lisp-1，symbol/var 二分法)。但我个人认为 SICP 不会在 Clojure 上帮你太多。YMMV。
> 
> — [里奇·希基](https://groups.google.com/forum/#!topic/clojure/jyOuJFukpmE)

Lambda man 的评论:

> 艾贝尔森和苏斯曼写了一本优秀的教科书，它可能会引发编程教学方式的一场革命。他们不是强调某一种特定的编程语言，而是强调应用于编程的标准工程技术。尽管如此，他们的教科书还是与 Lisp 的 Scheme 方言紧密联系在一起。我相信，如果将他们文本中使用的相同方法应用于诸如 KRC 或米兰达这样的语言，将会更好地介绍作为工程学科的编程。
> 
> 菲利普·瓦德勒。为什么算计比心机好，1987

## 第二人生

他们停止了最初的 6.001，然而一些爱好者开始了更高级的 SICP 版本- [6.037 -计算机程序的结构和解释](http://web.mit.edu/alexmv/6.S184/)。

要是能有在线 SICP 版带内联 [REPL](https://repl.it) 不用离开浏览器就能写练习题，带[帕里弗](https://shaunlebron.github.io/parinfer/)也许在浏览器里还有用 WASM 写的[方案就好了。](https://github.com/google/schism)

## 其他书籍

以下是 SICP 的一些替代品:

*   [如何设计程序](https://www.htdp.org/)
*   [编程语言:应用与解释](https://cs.brown.edu/~sk/Publications/Books/ProgLangs/2007-04-26/)
*   [更多关于方案的书籍](https://racket-lang.org/books.html)
*   小阴谋家，老练的阴谋家，理性的阴谋家