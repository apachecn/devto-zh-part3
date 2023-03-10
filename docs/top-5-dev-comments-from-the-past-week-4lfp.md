# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-4lfp>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

回复 **[如果你能改变学习编码的一件事，会是什么？](https://dev.to/aspittel/if-you-could-change-one-thing-about-learning-to-code-what-would-it-be-10p0)** ， [@cjbrooks12](https://dev.to/cjbrooks12) 谈到了两个极端的关注点:绝对初学者和完全主题专家的内容，以及中间的空隙:

[![cjbrooks12 profile image](img/97fbb987cec86db9d610eae97c0dfa7b.png) ](/cjbrooks12) [ Casey Brooks ](/cjbrooks12) • [<time datetime="2019-05-10T16:58:23Z" class="date-short-year"> May 10 '19 </time>](https://dev.to/cjbrooks12/comment/algl) 

我觉得对于绝对的初学者来说有太多的内容，有很多非常具体/高级的主题，但是对于中级开发人员来说却没有那么多。在不深入研究 90%的应用程序都不需要的理论和设计模式的情况下，越过各种框架的“hello world”进入现实世界的开发主题，感觉是一个真正的挑战。

对于大多数项目，你不需要使用最前沿的技术和设计模式，这是一个初级开发人员会做的。它们对于从事专业开发的有经验的开发人员来说非常有用，但是对于初学者/业余爱好者来说，它甚至会成为真正的障碍。

在极端的情况下，它训练开发人员只是复制和粘贴解决方案来让一些东西工作，而没有教他们*为什么*应该这样做，因此，开发人员更难真正后退一步，思考他们试图解决的问题。

[@mortoray](https://dev.to/mortoray) 在 **[ELI5 中提供了一些实质性的见解:一个人如何编写一种新的计算机语言？](https://dev.to/desi/eli5-how-does-someone-write-a-new-computer-language-1a3n)线程 T5。当我看到这个主题的时候，我希望 Edaqa 会加入进来:** 

[![mortoray profile image](img/3f601f0d3e93536df22a4b2d92f1094d.png) ](/mortoray) [ edA‑qa mort‑ora‑y ](/mortoray) • [<time datetime="2019-05-08T23:22:40Z" class="date-short-year"> May 8 '19 </time>](https://dev.to/mortoray/comment/akd3) 

没有办法在不模糊细节的情况下真正做到这一点，所以我会选择简化，但尽量保持正确。

### 词汇

一门语言始于对这门语言能做什么的人类水平的描述。你决定一个词汇表，以及它在文本文件中的外观。也就是说，在不考虑硬件/软件的情况下，你就你的语言中的单词将完成什么达成了一致。

一旦你完成了，你就可以为这种语言编写一个编译器了。这是能让计算机理解你的词汇的东西。

编译器由几部分组成。

### 解析

那个文本文件对你来说可能是可读的，但计算机却无法理解。解析将文本翻译成计算机能够理解的形式。这是抽象语法树(AST)。

此时，编译器已经读取了您的代码，但还不理解它。

### 语义/类型

一旦编译器有了 AST，它就需要理解这一点。在这个阶段，它检查并决定代码做什么。它指出函数名是什么，以及如何调用它们。它决定了什么是变量，以及如何在变量中存储数据。它指出了 if 语句和循环的条件。

此时，编译器理解您的代码。

### 平移/降低

编译器现在将把代码翻译成一种新的形式，一种计算机能够理解的形式。这通常被称为“降低”，因为它使你的高级语言更接近计算机理解的机器语言。

编译器在不同的阶段完成这项工作，通常会降低到一种或多种中间语言(IRs)，最后是机器的机器代码。

简单的编译器可能会选择“降低”到另一种高级语言，如 C++。这提供了一种快速实现一些新语言的方法。

### 链接

你的代码不是孤立存在的，它需要与目标机器交互。除了翻译代码，编译器还将设置名称和资源表。这些是您的代码将如何附加到系统上的其他组件。

## 宿主语言

但是你可能想知道用什么语言编写编译器？这可以是任何语言。编译器的工作是把你的高级代码翻译成一种已知的低级语言。编译器本身是用什么语言写的并不重要。

为简单的语言编写编译器相对容易。复杂的语言需要更多的努力。

我鼓励每个人写一次语言。我已经写了一些，从基本的领域特定语言，到像 C++这样的现代语言。

本描述集中于编译语言，最好用命令式或函数式语言来理解。随着我们进入其他范例，如声明性语言或解释型语言，细节和术语往往会发生变化。尽管如此，大多数阶段仍然存在。

我需要计算机科学学位才能在科技行业找到工作吗？[@ _ 亚当 _ 巴克](https://dev.to/_adam_barker)讲述了为什么学位不是要求，并强调了雇主寻找的一些品质:

[![_adam_barker profile image](img/b45b0b782e48d5bb2bf68139d4c15bb7.png) ](/_adam_barker) [ Adam Barker ](/_adam_barker) • [<time datetime="2019-05-09T04:09:56Z" class="date-short-year"> May 9 '19 </time>](https://dev.to/_adam_barker/comment/akfa) 

很棒的文章！我坚信，当一个人成为开发人员时，他所拥有的最大的品质就是学习的欲望。

对我来说，学习的欲望和能力胜过一切。依靠 CS 学位作为替代品是行不通的。没有什么比与一位热情的候选人交谈更能给我信心了，他有一个 Github 账户，里面装满了他们在业余时间用不同语言编写的项目或实验。

我在 Instagram 上找到了我们的新员工，很快就知道她对学习充满热情，这将使我们长期受益。她在零售业做全职工作，但还是抽出时间致力于(并完成)项目来展示她的学识。

作为一个线程 ELI5(像我五岁一样解释) **[GitHub 公布 GitHub 包注册表](https://dev.to/peter/github-announces-github-package-registry-3a27)** ，@0xrumple 谈包注册表有什么用:

[![yaser profile image](img/31df1fc8df7b44c6d537d1e78e852766.png) ](/yaser) [ Yaser Al-Najjar ](/yaser) • [<time datetime="2019-05-10T22:28:26Z" class="date-short-year"> May 10 '19 </time> • Edited on <time datetime="2019-05-10T22:34:01Z" class="hidden m:inline-block date-no-year">May 10</time>](https://dev.to/yaser/comment/alna) 

假设你在一家餐馆工作...

作为一名厨师，你不希望每次想做一道美味的菜肴时，都从其他厨师那里拿配方，然后自己做(那需要很多配方)。

如果你有一个厨房，供应一些即食商品食品，并将其放入你的特殊盘子中，你会喜欢的。

现在，那个厨房的可爱之处在于它**保留了带有标签**的旧商品食品，因为一些厨师更喜欢那种商品食品的旧味道，而厨房**只允许那家餐馆**的厨师进入(因为你不想与其他餐馆分享你的商业秘密)。

现在改变上面的故事措辞:

Restaruant =您的组织

主厨=您组织中的其他开发人员/团队

配方=包装代码(原始)

厨房=登记处

商品食品=包装(可供消费)

盘子=你的应用

标签=这些包的版本(以确保新版本不会影响使用该包以前版本的应用程序)

最后，在 **[中，你应该在网络开发面试中问的 10 个问题](https://dev.to/flippedcoding/10-questions-you-should-ask-in-a-web-dev-interview-1c2d)** [@perigk](https://dev.to/perigk) 提出了几个有助于揭示公司文化的逆向面试问题:

[![perigk profile image](img/7d6bdd6c3bbc3537454e4465443f6d60.png) ](/perigk) [ Periklis Gkolias ](/perigk) • [<time datetime="2019-05-09T15:46:28Z" class="date-short-year"> May 9 '19 </time>](https://dev.to/perigk/comment/aknm) 

不错的名单。我还要补充一点

公司的培训政策是什么？一家没有培训政策或福利的公司，至少在软件行业，是完全没有价值的(除非我们是在为早期创业公司说话，我可以原谅他们)

你如何处理荒谬的顾客要求？你不会想和一家不知道如何反击的公司合作

下周见，更多精彩评论，✌