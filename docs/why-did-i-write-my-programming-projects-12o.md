# 我为什么要写我的编程项目？

> 原文：<https://dev.to/rain1/why-did-i-write-my-programming-projects-12o>

# 我为什么要写我的编程项目？

我们日常使用的所有软件都是建立在基础之上的。我相信这个基金会对我们来说应该是平易近人，可以理解的。否则，我们只是漂浮在未知的海洋上，无法把握或充分利用它。我认为我们很多人都在为我们的软件工具包的奇怪的边缘情况和令人惊讶的行为而斗争，因为它非常复杂。我发现这种复杂性大部分是不必要的。

我试图通过创建我自己的现有工具的精简版本来驯服一些复杂性。我对软件和设计方面有自己的关注，我很乐意听到关于这些话题的其他观点，所以如果你有任何关于这些事情的想法，请随时添加一些评论。

## 年代

*   [https://github.com/rain-1/s](rain-1/s)

我制作了轻量级 shell，因为我不喜欢 bash shell 复杂的语法。我发现像向命令行工具传递变量或文件名这样的基本任务充满了危险。当您编写 bash 脚本时，它可能看起来工作正常，但实际上可能有各种与文件名相关的边缘情况，文件名中包含空格，以破折号开头的内容会被误解为命令行标志。问题太多了。shell 的早期设计被各种关于转义和引用的错误所困扰，今天我们很好地理解了这些事情，可以设计出更简洁的语言。现代脚本语言中有一个明显的趋势，那就是我们想要摆脱字符串移植。David wheeler 有一些很棒的内容，bash wiki 也有:

*   [https://dwheeler.com/essays/filenames-in-shell.html](https://dwheeler.com/essays/filenames-in-shell.html)
*   [https://dwheeler.com/essays/shellshock.html](https://dwheeler.com/essays/shellshock.html)
*   [https://mywiki.wooledge.org/BashPitfalls](https://mywiki.wooledge.org/BashPitfalls)
*   [https://twitter.com/roblourens/status/1128165769395597312](https://twitter.com/roblourens/status/1128165769395597312)

[![](img/a30386034ba9c8b4ceb5857deb900c2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--00MFgsgm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/748by9gapgt229v2q6zp.jpg)

所以我想构建自己的 shell，它具有常规的令牌语法。并且该阶段将与可变膨胀阶段分离。少了很多惊喜！我从制作这个中学到了一些东西。我还用 C 实现了一个很好的内存分配“区域”系统，欢迎你使用。制作 shell 的一个好处是可以练习 dup/pipe，以便实现类似于`ps | grep | wc`的管道。不过，我从中学到的最重要的一点是，shell 脚本的情况不会因为一个 shell 而得到显著的改善..

我得到的理解是，我们使用 shell 组合在一起的基本 UNIX 工具应该更有规律，对机器更友好。我认为 UNIX 命令行工具应该有一个完全不同的命令行处理系统，而不是使用纯文本作为通用接口，它们可以使用像制表符分隔值或 ascii 分隔值这样的东西。能够用一种明确指定的结构化数据格式在程序之间进行交换是非常有价值和有帮助的。相反，我们每天都用 awk 和 sed 构建特定的解析器。

*   [https://gist . github . com/rain-1/e 6293 EC 0113 c 193 ECC 23d 5542961d 322](https://gist.github.com/rain-1/e6293ec0113c193ecc23d5529461d322)
*   [https://Ronald Duncan . WordPress . com/2009/10/31/text-file-formats-ascii-delimited-text-not-CSV-or-tab-delimited-text/](https://ronaldduncan.wordpress.com/2009/10/31/text-file-formats-ascii-delimited-text-not-csv-or-tab-delimited-text/)

人们谈论文本如何成为 UNIX 系统中的通用接口，但是你甚至不能解析`ls`的输出。这有点可笑，这样一个简单的工具——你通常学到的第一个工具——违反了系统的普遍规律。各种项目已经重新实现了 GNU coreutils——这不是一个大工程。如果我们花时间为命令行工具设计一个新的结构化数据交换系统，而不是“文本行”的模糊概念，我认为脚本的日常管道工作会更加顺畅和容易。

我已经看到这种情况发生了一点点。`ip`实用程序现在可以输出 json 格式的数据了！虽然这可能不是我推荐的确切方向，但这仍然是一个进步。配合使用`jq`工具，您可以在终端中非常有效地处理这些数据。

*   [https://lwn.net/Articles/738897/](https://lwn.net/Articles/738897/)
*   [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)

最后，制作`s`是一个有价值的实验，但是它向我指出了真正的问题在别处(用户空间工具)。我不推荐使用 s，但是它的代码值得学习。

## 异人

*   [https://github.com/rain-1/makes](rain-1/makes)
*   [https://github . com/rain-1/makes/tree/master/previous-version](rain-1/makes/tree/master/previous-version)

我一直在批评整个 autoreconf，automake，m4，./配置，做工具包很久了。它解决了一个实际问题，软件在构建之前需要配置。为了构建软件(用 C、C++和许多其他语言编写),我们需要做依赖解析、增量构建和捆绑并行的东西。Make 做到了这一点，但我讨厌使用 make——它可能会带来压力。

Make 过于复杂。它不符合解决一个任务并与其他程序交互的 UNIX 哲学。相反，它试图做一切:例如，它包括一个不兼容的 shell 的重新实现，它甚至有 guile scheme 脚本引擎内置其中。我觉得我可以做一个正交的构建引擎。现在我意识到有许多现存的构建系统，当然我回顾了它们。我会说这个图很有趣。我认为我们也看到了一种趋势，即新语言内置了自己的构建工具(我认为这很清楚他们为什么这样做，而不是仅仅让人们使用 make)。

出于这些原因，我决定我已经受够了，并创造了自己的替代品。我对现有的构建系统如 cmake 或 meson 不感兴趣，所以我想探索绝对最小可行的构建系统。我的第一个尝试是“前一版本”，这是一个简单的命令行工具，它告诉你文件 A 是否比文件 B、C、d 中的任何一个都新。原语足以进行增量构建，您只需将构建规则嵌入到 shell 脚本中，如下所示:

```
makes data.o \
        data.c data.h &&
        $CC $CFLAGS -o data.o data.c 
```

通过制作这个工具，我了解到这个框架对于增加并行性来说有点太简单了。我希望通过等待文件出现来增加并行性，但是存在一个竞争条件，因为像 gcc 这样的编译器会在填充内容之前打开输出文件——这不是一个原子操作。让另一个进程等待文件“完成”不是你真正能做的事情。我研究了一些像 inotify，fnctl 这样的东西来尝试，但是它们都不可靠。

那时，我设计了一个新版本的构建系统，它基于一个用制表符分隔的值文件，其中充满了构建规则。该工具将加载所有规则并执行依赖关系解析，然后为您执行增量和并行构建。我决定用 golang 而不是 C 来实现这个工具，因为它增加了复杂性，解析一个输入文件并执行一个图形 topsort，以及使用 go 的并发特性，这是一个很好的帮助。

这个工具确实建立在 makes v1 的概念之上，因为它的思想不是直接编写 TSV 规则文件(尽管如果您愿意，您也可以这样做)，而是使用您选择的任何语言的脚本来生成它。我使用了 shell 脚本，但是 python 或者你喜欢的语言也可以。我确保通过构建一些现有的软件项目来测试我的 makes 概念:bash、jq、sbase。我还发现该工具可以用于其他事情，比如维护一个 mp3 版本的目录树，其中包含 flacs。你可以重新运行它，它将只编码新的广告，还没有被翻译成 mp3。

您可以在这里查看这些脚本(以及其中一些脚本的 TSV 构建规则输出):

*   [https://gist . github . com/rain-1/f 3434 F4 b 12147 D5 ef 62369 e 511 a 184 de](https://gist.github.com/rain-1/f3434f4b12147d5ef62369e511a184de)

在我看来`makes`的这个 v2 很优秀。对我来说，它完全填补了发誓不再使用‘make’的空白。我推荐使用这个工具。

虽然我还不知道如何处理自动工具的情况。有什么想法吗？我要指出的一件事是，今天的软件配置需求没有那时复杂。

## 测试

*   [https://github.com/rain-1/tests](rain-1/tests)

`tests`是一个极其简单的脚本——80 行代码——它使您能够使用一个充满输入和预期输出(或预期错误)的文件夹来自动测试命令行工具。

简而言之，我做这个是因为我找不到任何可以做这个的东西？这听起来可能很疯狂，但我搜索了一下，陷入了阅读“测试任何东西协议”的困境。我发现的每一个测试工具都非常臃肿——以 dejagnu 为例:`wc *`说它在顶层目录中有 28012 行代码..这还不算源代码，这是构建它的脚本和其他东西。行数。'*.' exp '表示大约有 17195 行“exp”代码。所有这些只是为了运行一个具有不同输入的脚本并检查结果？

*   [https://www.gnu.org/software/dejagnu/](https://www.gnu.org/software/dejagnu/)

我发现，完成相同任务的两个程序的 80 行代码与 17195 行代码的比率是完全正常的。我们有一个软件复杂性的危机，大多数程序员甚至不愿意接受这是真实的。我相信所有这些额外的工作只会使完成基本任务变得更加困难，并导致持续的额外维护成本。

[![](img/d6470097b10ac27e8721e00908861621.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JHOrZTvX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/19il8txpsddfkz3uy449.jpg)

抛弃过于复杂的软件，寻找或制造一些简洁明了的软件来完成你的任务是值得的。

*   [https://softwarecrisis.miraheze.org/wiki/Linecount](https://softwarecrisis.miraheze.org/wiki/Linecount)

我推荐用这个！

## 线路噪声暴民

*   [https://github.com/rain-1/linenoise-mob](rain-1/linenoise-mob)

我在 shell 中使用了 linenoise。这是一个很好的在终端中编辑行的小工具。它让你不能使用 GNU readline。不幸的是，作者忙于许多其他的事情，所以大量的 pull 请求和 github 问题加在一起。我合并了所有“好的”(由我判断的)拉请求(大约 200 个！)变成分叉回购。工作量很大..我还包括 UTF-8 补丁。我希望这是一个有价值的贡献，其他人需要加入到维护它的行列中来——有些人已经做到了！

我从 tinycc 那里窃取了-mob 分支的想法，这非常成功。

[![](img/e6ee8b188da01b1a41d52c8faa15ab5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hLoZ7tdF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/478jzx33g7i6e03xrfhf.png)

*   [https://repo.or.cz/w/tinycc.git](https://repo.or.cz/w/tinycc.git)

## 塔罗牌和单 _ 奶油

*   [https://github.com/rain-1/tarot-compiler](rain-1/tarot-compiler)
*   [https://github.com/rain-1/tarot-vm](rain-1/tarot-vm)
*   [https://github.com/rain-1/single_cream](rain-1/single_cream)
*   [https://rain-1.github.io/scheme](https://rain-1.github.io/scheme)
*   [https://gist . github . com/rain-1/36c 4851 b 7 c 29 cf 8 e 42 f 23 ba 6 EEC 37 be 6](https://gist.github.com/rain-1/36c4851b7c29cf8e42f23ba6eec37be6)

我是 scheme 语言的粉丝，我想从头开始构建自己的 scheme，我真的想做一个可以自我编译的自托管版本。这比我想象的要难多了。在我达到我想要的结果之前，我在它上面工作了几年。尽管最后的结果很好。源代码非常简短。它可以很快地自我构建(14 秒)。大多数编译器太复杂，难以理解，并且需要很长时间来构建自己。

编译器中最好的算法之一是闭包转换传递。我已经把一个要点链接放到了一篇关于它自己的文章中——这样就可以独立于编译器的其他部分来学习和理解。我还写了每一遍编译器的文章。我认为这些文档对学习实现函数式语言的初学者很有价值。它还有一个用 C 编写的自定义 scheme 虚拟机，所有的东西都在它上面运行。

通过这样做，我认识到的一个主要问题是，自托管编程语言实现并不是一件真正有益的事情。这非常“酷”，但这并不意味着这是一个好的软件工程实践。您从自宿主创建的“循环”中，源代码需要一个已经编译好的二进制文件，这引入了许多您需要解决的陷阱和问题，以及一个简单的事实，即您不能用更小的东西来构建编译器。这个特性被称为“可引导的”,我认为我们应该努力使我们所有的编译器工具都可以引导到一个可以直接审计的微小的种子二进制文件。我们已经陷入了 GCC 工具链的混乱，需要 GCC 和 binutils 二进制文件来构建它。有一个项目可以修复这个问题。

[![](img/8a7b7ea70ef31054e9052d8f3a12a6a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--opVdx4yS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/22zffj9mis87own5clkf.jpg)

我从 chez scheme 引导我的编译器，然后让 tinyscheme 能够构建它。最终 tinyscheme 崩溃了，所以我用 C 从头开始写了自己的 scheme 解释器。我认为它是最好的单文件方案实现之一，因为它有一个工作的 GC 和适当的尾部调用——大多数单文件。c 语言的方案没有经过足够的实战考验，无法进行更大规模的计算。

关于实现编程语言，我学到的另一件重要的事情是，实际的核心特性:获取一个输入源文件并执行或编译它，只是创建编程语言所需做的一小部分。您还需要调试和分析功能以及所有的文档。可能更多..

*   [https://tratt . net/Laurie/blog/entries/the _ bootstrapped _ compiler _ and _ the _ damage _ done . html](https://tratt.net/laurie/blog/entries/the_bootstrapped_compiler_and_the_damage_done.html)
*   [https://bootstrappable.org/](https://bootstrappable.org/)

我推荐使用 single_cream，但我并不真的推荐使用 tarot，如果你想学习编译器和 PL dev，学习它可能是有价值的。

## 地鼠

*   [https://github.com/rain-1/gopher](rain-1/gopher)

[![](img/2baed8846c7c9fac0102205744324b66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7bqsD0VX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/73p7jiiwo9l1ypyehs56.png)

这是我的地鼠客户。我想构建一个类似于 web 浏览器的 GUI gopher 客户端——但不是构建在 web 技术之上。为了做到这一点，我首先分叉 cgo，并添加了一个 gtk gui。后来我在瓦拉写了一个全新的。我认为 vala 版本工作得很好，但是我在并发特性方面遇到了一些麻烦(在页面加载时支持 spinner)。我很感激#vala IRC 频道的人们帮助我完善了它！

由于广告、跟踪和不良的前端实践(不必要的 javascript 框架)，网络已经变得难以置信地敌视用户。Gopher 提供了喘息的机会。我希望人们不要试图使它现代化而毁了它。我对向 gopher 添加某种降价支持有些兴趣(提前计划这是为什么我用 GTKTextView 而不是基于的终端制作 GUI 客户端的部分原因),但我还没有具体的计划，我在 gopher 上看到了很多降价文档，所以我相信很多人都会从中受益。

我觉得这个地鼠客户很棒。使用 gopherspace 是一件令人愉快的事情，也很有趣。能够在网络浏览器之外做这件事真的很棒。我认为它需要一个“书签”功能，我还没有想出如何添加它。我希望其他人会使用这个客户端，甚至帮助改进它。

*   [https://idlewords.com/talks/website_obesity.htm](https://idlewords.com/talks/website_obesity.htm)

# 总结

写软件本身是有回报的，但我把这些东西放在 github 上并与世界分享，希望它能造福他人。我真的不知道和那些在软件方面有着相同审美和关注点的人联系的最佳方式。据我所知，任何地方都没有这样的组织。

*   (s)使用结构化格式进行数据交换
*   (s)可追溯到 70 年代的历史错误已经成为我们的基本工具。
*   (s)我们现在知道如何把事情做得更好，但在自由软件的“自由市场”中很难向前飞跃，我们只能一步一步地改变。
*   (使得)甩了让你不开心的软件。把你的精力放在你相信的技术上。
*   (使得)制表符分隔值格式比它得到的好评要好得多，使用不足。
*   你可以用你在几周内编写的一个程序取代(供你自己使用)几十年的软件。
*   (测试)转储过度复杂的软件
*   (测试)做同样事情的两个程序可能有 1000 倍的大小差异。
*   (塔罗)自我托管不应该是语言实现者的目标。
*   (塔罗牌)如果你在完成一个大目标时遇到困难:设定许多小目标来增强力量，以便长期坚持下去。
*   (地鼠)如果你喜欢某样东西，就随它去吧。(不要修理没坏的东西)。