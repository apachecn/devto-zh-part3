# (少于)12 天的 Prolog-Prolog 编程语言指南+简要回顾

> 原文：<https://dev.to/donaldkellett/less-than-12-days-of-prolog---a-guide--brief-review-of-the-prolog-programming-language-1dgl>

所以，这一切都是从大约一两周前，有人在我所在的脸书计算机科学迷因群里发布了以下迷因开始的:

[![Trying to study Prolog](img/7c47ae37e69648399490543676af77bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xHrEYaWv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y6o0ncn9jzwas53bw2jn.jpg)

正如我从那篇文章的评论部分了解到的，许多大学的计算机科学项目要求他们的学生学习 [Prolog](https://en.wikipedia.org/wiki/Prolog) 编程语言，通常是在一个离散的数学课程中，学生们通常会觉得这是一个相当不愉快的经历。这篇文章还提醒我，教授我 9 月份参加的离散数学课程的教授在介绍著名的逻辑谜题[斑马之谜/爱因斯坦之谜](https://en.wikipedia.org/wiki/Zebra_Puzzle)时也简要提到了 Prolog(稍后会有更多介绍)，但随后明确表示这门课程不需要 Prolog(我猜我是幸运的:p ),所以我后来忘记了它。然而，迷因促使我试用 Prolog 来看看到底是怎么回事。

### 什么是 Prolog？

Prolog 编程语言首次出现于 1972 年，这使得它和声名狼藉的 C 编程语言一样古老。与高度命令性和过程性的 C 语言相比，Prolog 是一种基于 [Horn 子句](https://en.wikipedia.org/wiki/Horn_clause)(一阶谓词逻辑的图灵完全子集)的主要声明性编程语言(带有一些过程性元素)。它最初是为自然语言处理而设计的，但目前也在人工智能等其他领域得到了应用。尽管 Prolog 的影响力远不及 C，但它已经影响了许多著名的编程语言，包括 Clojure 和 Erlang。

### 你学 Prolog 花了多长时间？

我花了大约一周半的时间学习 Prolog 编程的基础知识，这比 12 天略短，因此有了这个标题；)

### 在哪里可以学习 Prolog？

现在就有免费的网络版[学习 Prolog！](https://lpn.swi-prolog.org)对于想要开始学习 [SWI-Prolog](http://swi-prolog.org) 的初学者来说，这是一本很棒的书，为数不多的(两本？)广泛接受的主要 Prolog 实现， [SICStus Prolog](https://sicstus.sics.se) 是另一个。请注意，尽管不同的 Prolog 实现共享相同的语法和语义，但它们在内置和提供的库方面可能有很大的不同，因此 Prolog 程序通常不能在实现之间完全移植。

### 如何在我的电脑上启动并运行(SWI-)Prolog？

如果你像我一样使用 Mac，那么我强烈建议你安装[家酿](https://brew.sh)(如果你还没有的话)。Homebrew 是 Mac 的命令行软件包管理器，它使得在命令行上安装各种东西变得更加简单直接*。然后简单地执行:*

```
$ brew install swi-prolog 
```

安装`swi-prolog`公式(就像他们在家酿中被称为的那样)。或者，您也可以访问 [SWI-Prolog](http://swi-prolog.org) 官方网站，该网站应包含下载官方 SWI-Prolog 解释器的链接，该解释器可用于多种操作系统。

### 我不能/不想在我的计算机上安装 SWI-Prolog。我还能学 SWI-Prolog 吗？

没问题——SWI-Prolog 官方网站提供了 [SWISH](https://swish.swi-prolog.org) ，这是一项在线服务，用于试验和分享 SWI-Prolog 程序。

### 你会推荐 Prolog 作为第一编程语言吗？

绝对不是——事实上，如果没有对形式(一阶)逻辑中的术语和概念的基本理解，比如命题、谓词、逻辑合取和析取、蕴涵和*假言假语*，一个人可能很难理解哪怕是最简单的 Prolog 程序。顺便说一句，(经典)命题逻辑(及其相关符号)通常在大多数离散数学课程开始时或接近开始时教授——难怪许多这样的课程将 Prolog 编程引入其课程。此外，精通递归或数学归纳法将有助于学习 Prolog——Prolog 中的递归极其常见，因为没有像`for`或`while`循环这样的(命令式)循环结构。

### 但是 Prolog 真的是*那个*硬吗？

在我看来不是。一旦您习惯了 Prolog 编程的方式，您可能会意识到 Prolog 是一种相当简单的语言——毕竟，只有少数几种数据类型(原子、整数、变量、复杂术语),并且 Prolog 程序的含义通常是透明的，只要它的过程特性(如各种形式的 I/O 或涉及副作用的操作，如 cut)没有被滥用。事实上，由于 Prolog 是声明性的，所以大多数时候你可以像阅读简单的逻辑语句一样阅读 Prolog 语句。例如:

```
lazy(X) :- hates(X, prolog). 
```

是一个有效的 Prolog 语句(实际上也是一个有效的 Prolog 程序)，它简单地表示“如果 X 讨厌 Prolog，那么 X 就是懒惰的”(参见我在那里做了什么？) ;)

学习 Prolog 的难度很大程度上是因为程序员无法抛开根深蒂固的命令式编程思维定势，这种思维定势是通过无数小时的传统编程语言(其中绝大多数是高度命令式的和面向对象的)的“灌输”而形成的，这些语言经常给人一种错误的印象，即编程就是告诉计算机“做这个”和“做那个”。

### 我很感兴趣——给我一个使用 Prolog 的例子。

很高兴听到这个消息。还记得我上面提到的斑马拼图吗？这也是众所周知的爱因斯坦之谜，据传是*(未经证实！)是由伟大的科学家阿尔伯特·爱因斯坦首先提出的。此外，*有传言说*只有大约 2%的世界人口能够正确地解答这个谜语。准备好了吗？谜语是这样的:*

 *> 1.  有五栋房子。
> 2.  那个英国人住在红色的房子里。
> 3.  西班牙人拥有这条狗。
> 4.  咖啡是在温室里喝的。
> 5.  乌克兰人喝茶。
> 6.  绿色的房子紧挨着象牙房子的右边。
> 7.  老金烟鬼拥有蜗牛。
> 8.  库尔斯在黄色的房子里被熏制。
> 9.  牛奶是在中屋喝的。
> 10.  挪威人住在第一栋房子里。
> 11.  抽切斯特菲尔德烟的人住在养狐狸的人隔壁的房子里。
> 12.  库尔斯是在养马的房子旁边的房子里熏的。
> 13.  幸运罢工吸烟者喝橙汁。
> 14.  日本人抽议会烟。
> 15.  挪威人住在蓝房子旁边。
> 
> 现在，谁喝水？斑马是谁的？
> 
> 为了清楚起见，必须补充的是，五所房子都被漆成不同的颜色，它们的居民来自不同的国家，拥有不同的宠物，喝不同的饮料，抽不同品牌的美国香烟。另一件事:在陈述 6 中，右意味着你的权利。

*(摘自维基百科)*

好吧，我们可以试着在纸上解决它。但是如果我们能写一个程序为我们解决这个问题不是更有趣吗？这些类型的问题是 Prolog 在约束编程中表现出色的少数几个领域之一。在更传统的编程语言(如 Python)中，您可能需要设计一种算法来寻找谜底，这可不是一件容易的事情。然而，在 Prolog 中，我们所要做的就是陈述约束(规则)，Prolog 将通过回溯为我们解决它！为了让您了解这种情况，Prolog 中的谓词(函数)的前几行(也是最后几行)说明了谜语的约束条件:

```
% riddle.pl

solve(WhoDrinksWater, WhoOwnsTheZebra) :-
  length(Houses, 5),                                      % 1\. There are 5 houses.
  member(house(red, english, _, _, _), Houses),           % 2\. The Englishman lives in the red house.
  member(house(_, spanish, dog, _, _), Houses),           % 3\. The Spaniard owns the dog.
  ... ,
  member(house(_, WhoDrinksWater, _, water, _), Houses),  % Who drinks water?
  member(house(_, WhoOwnsTheZebra, zebra, _, _), Houses). % Who owns the zebra? 
```

然后我们可以将文件`riddle.pl`加载到解释器中:

```
?- [riddle].
  true. 
```

提出我们的疑问:

```
?- solve(WhoDrinksWater, WhoOwnsTheZebra).
WhoDrinksWater = norwegian,
WhoOwnsTheZebra = japanese ;
  false. 
```

哒哒！Prolog 正确地告诉我们，挪威人喝水，日本人拥有斑马。它还正确地告诉我们这是这个谜题唯一可能的答案——在我们使用`;`提示它输入下一个答案集后，它回答了`false.`。

这个求解器的完整代码可以在[这里](https://github.com/DonaldKellett/Einsteins-Riddle-Prolog)找到。

### 斑马拼图很有趣——你有在现实生活中使用 Prolog 的更具体的例子吗？

回想一下，Prolog 最初是考虑到自然语言处理而设计的。事实上，它甚至在语言本身中内置了表达定冠词语法(DCG)的语法糖。DCG 允许我们明确地定义特定语法中的所有有效句子，该语法可以是自然语言(的子集)，或者通过扩展，是编程语言(因为编程语言通常比自然语言定义得更严格)。这里有一个简单的例子，直接取自[立即学习 Prolog！- 7.2 定分句语法](https://lpn.swi-prolog.org/lpnpage.php?pagetype=html&pageid=lpn-htmlse29):

```
s --> np,vp.

np --> det,n.

vp --> v,np.
vp --> v.

det --> [the].
det --> [a].

n --> [woman].
n --> [man].

v --> [shoots]. 
```

在这种语法描述的有效英语句子的子集里，像“一个男人射击一个女人”或“一个女人射击”这样的句子被认为是合乎语法的，而“女人射击男人”和“男人射击女人 A”则不是。注意表达这种语法的 Prolog 代码是多么简单和直观。相比之下，分析这样的语法(甚至像这样琐碎的语法！)在更传统的语言中，比如 C++/Java，甚至 Haskell，将会是一个令人头疼的问题。

### 结束语

我希望这篇文章能让你深入了解 Prolog 是什么，它在何时、何地如何有用，以及如果这篇文章引起了你的兴趣，你可以从哪里开始学习 Prolog。欢迎在下面的评论中留下你的想法:)

### 外部来源

本文的一些内容来自或直接取自下面列出的来源。也有一些链接可能是感兴趣的。

1.  [序言-维基百科](https://en.wikipedia.org/wiki/Prolog)
2.  [斑马拼图-维基百科](https://en.wikipedia.org/wiki/Zebra_Puzzle)
3.  立即学习 Prolog！免费网络版
4.  [SWI-Prolog 官网](http://swi-prolog.org)
5.  Macintosh 的命令行软件包管理器
6.  [SWISH - SWI-Prolog 共享](https://swish.swi-prolog.org)
7.  [序言中的爱因斯坦之谜](https://github.com/DonaldKellett/Einsteins-Riddle-Prolog)**