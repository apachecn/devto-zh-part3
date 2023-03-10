# 您应该在 Vim 中使用标记

> 原文：<https://dev.to/djmoch/you-should-be-using-tags-in-vim-2c41>

*注:这是我在今年的[vimways.org](http://vimways.org)降临节日历上写的一篇文章的交叉帖子。如果您对 Vim 感兴趣，我建议您也去那里看看其他文章。*

> 我爱你；你使我完整。
> 
> *   邪恶博士

我第一次来 Vim 是通过推荐。我一直在寻找一个好的 Python IDE(当时我是这门语言的新手),一个建议是使用 Vim 并在上面添加各种插件。Vim 可以做很多我认为只有 IDE 才能做的事情，这让我有点吃惊。我实习了一个夏天，在 Unix 终端上使用 Emacs，但当时我没有足够的好奇心去使用它和`notepad.exe`有什么不同。那个夏天，我一直希望我有自动补全、缩进和所有让我欣赏我在大学使用的 IDE 的功能。我是多么天真啊！

那么，如何指导我在 Vim 中实现强大的编程完成呢？通过使用一个叫做 YouCompleteMe 的插件。我对它的体验还不错，至少一开始是这样。它花了一段时间来安装和工作，但当时这并没有困扰我，因为我只是在家里玩它，如果它突然坏了，风险很低。我确实注意到它让维姆慢了下来。很喜欢。但那主要是在它刚开始的时候，我知道的还不够多，觉得很沮丧。大概这个插件第一个真正困扰我的是嵌入式 Jedi 语言服务器比 Vim 本身使用了更多的内存。其他推荐的插件同样落后，我最终去寻找更好的。

我发现的是 Vim 本身。

您知道 Vim 有内置的完井设备吗？它开箱即用也能很好地工作，但稍加设置就能变得很棒。让我们来看看 Vim 在完成方面提供了什么，以及如何充分利用它。

## 在 Vim 中完成

Vim 中的完成功能很强大，但不一定很简单。读读 [:h ins-completion](http://vimdoc.sourceforge.net/htmldoc/insert.html#ins-completion) ，你就会明白我的意思:

```
Completion can be done for:
1\. Whole lines                                |i\_CTRL-X\_CTRL-L|
2\. keywords in the current file               |i\_CTRL-X\_CTRL-N|
3\. keywords in 'dictionary'                   |i\_CTRL-X\_CTRL-K|
4\. keywords in 'thesaurus', thesaurus-style   |i\_CTRL-X\_CTRL-T|
5\. keywords in the current and included files |i\_CTRL-X\_CTRL-I|
6\. tags                                       |i\_CTRL-X\_CTRL-]|
7\. file names                                 |i\_CTRL-X\_CTRL-F|
8\. definitions or macros                      |i\_CTRL-X\_CTRL-D|
9\. Vim command-line                           |i\_CTRL-X\_CTRL-V|
10\. User defined completion                   |i\_CTRL-X\_CTRL-U|
11\. omni completion                           |i\_CTRL-X\_CTRL-O|
12\. Spelling suggestions                      |i\_CTRL-X\_s|
13\. keywords in 'complete'                    |i\_CTRL-N| |i\_CTRL-P| 
```

Vim 足够聪明，可以从各种来源提取完成数据，但反过来也希望用户知道哪个来源会提供最佳答案，并调用正确的键映射来绘制所需的完成数据。就学习曲线而言，这不是一个很大的障碍，但也不像按 tab 那么简单。

当试图学习 Vim 的补全系统时，首先要做的是禁用任何补全插件并学习这些键映射。熟悉它们还有助于您了解和记住 Vim 可以从哪里获取完成信息。您还应该阅读[:h‘complete func’](http://vimdoc.sourceforge.net/htmldoc/options.html#'completefunc')和[:h‘complete’](http://vimdoc.sourceforge.net/htmldoc/options.html#'complete')以获得更多关于用户定义的完成和`complete`选项的信息。

现在我们对 Vim 中的完成有了一个粗略的了解，让我们更深入地看看标签以及它们是如何参与完成的。

## Vim 中的`tags`介绍

Vim 中完成的一个来源是标记完成，它来自一个特殊的文件，叫做——一个标记文件。标记文件是标识符(例如，函数名)的集合，它们连同对它们在源树中的位置的引用一起被编译成单个文件。Vim 能够将(格式正确的)标记文件用于各种用例，其中包括像 Visual Studio 和 completion 一样导航您的源代码。

默认情况下，Vim 除了读取标记文件之外，不会对它做任何事情。参见[:h‘tags’](http://vimdoc.sourceforge.net/htmldoc/options.html#'tags')了解如何配置 Vim 查找标记文件的位置。Vimdoc 还包含了一个非常好的对标签的[介绍](http://vimdoc.sourceforge.net/htmldoc/usr_29.html#29.1)，所以我不会在这里花更多的时间来介绍它们。让我们继续，看看我们如何生成标记文件。

## 简介`ctags`

标记文件解决了在给定项目中导航和完成代码的问题，但是它们也产生了一个问题:我们如何创建标记文件，以及我们如何使它保持最新？即使对于一个小项目，手动维护标签文件也是一件痛苦的事情；对于像 Linux 内核这样的大型项目来说，这几乎是不可能的。幸运的是，没有人需要维护标签文件。有很多实用程序可以帮你做到这一点，通常被命名为 ctags，或者其他一些变体。一个非常受欢迎的选择叫做 [Exuberant Ctags](http://ctags.sourceforge.net/) ，它的优点是可以通过放置在`.ctags`文件中的正则表达式进行扩展，但缺点是自 2009 年以来就没有更新过。另一个越来越受欢迎的选择是[通用 ctag](https://ctags.io/)，它作为旺盛 ctag 的替代功能，并得到积极维护。我在这两方面都很幸运。

标签文件和生成它们的工具与高级文本编辑器一起有着悠久的历史。我这个计算机历史的书呆子喜欢知道我使用的工具和程序员在 BSD Unix 早期使用的工具一样。这也证明了他们提供的解决方案有多强大，40 年后人们仍然在使用他们。

## 生成`tags`文件

### 手动

当我们谈到手动生成标记文件时，我们指的是使用前面提到的任何一个标记实用程序来生成标记文件。如果您是那种喜欢至少理解如何从命令行做事情的人，那么您应该查阅所选标记实用程序的手册页。如果您想一次性为整个项目生成一个标记文件，请特别注意递归遍历所有子目录所必需的标志。

### 自动

您总是可以使用 ctags 实用程序从命令行生成您的标记文件，但是这需要在您的文本编辑器和您的 shell 之间来回切换，我怀疑任何尝试这样做的人是否会长期享受这种体验。所以让我们来看看自动生成它们的方法。

如果你只在 Vim 中使用标签文件，那么也许插件会让你感兴趣。我用了 [Gutentags](https://bolt80.com/gutentags/) 很久，发现它像宣传的那样“刚刚好”。它有一些默认设置，但是有很多机会定制它的行为，如果你访问上面的链接你就会看到。

尽管如此，在管理我的标签文件时，我最终转向了一个不同的方向。有几个原因，但最主要的一个原因是我喜欢把标记文件看作是与 Vim 分开的，文本编辑器无需管理就可以使用它。这是一种固执的观点，但是我越来越不喜欢配置我的文本编辑器来管理我的标签文件。所以我去寻找另一种方法，我找到了蒂姆·波普的方法，这是我一直在使用的方法。这种方法不是使用 Vim 本身来管理标签文件，而是在执行一些常见的 Git 操作时，使用本地的 [Git 挂钩](https://git-scm.com/docs/githooks)来重新构建标签。结果是一个系统也能正常工作，但只需要六行 shell 脚本，而不是几百行 vim script(T4)。必须保持 Vim 配置紧密。

另外，如果你已经使用了 Tim Pope 的[逃犯 Git 插件](https://github.com/tpope/vim-fugitive)(你应该这样做)，这个方法可以很方便地把你的标签文件放在插件告诉 Vim 去找它的地方——在`.git`文件夹中。当然，shell-script 方法是无限可配置的，所以您最终可以将标记文件放在您想要的任何地方。你也可以为其他分布式配置管理工具(比如 Mercurial)定制这个。

一般来说，还有其他选择。您可以设置一个文件系统监视器来监视您的项目树，并在文件发生变化时运行 ctags。像 <cite>Grunt</cite> 这样的任务运行器也可能是一个可行的选择，尤其是对 web 开发人员来说。目标是自动执行(重新)生成标签文件的任务，因此可能不会缺少选项。

## 把一切联系在一起

这让我们回到了开始的地方，回到了 Vim 中的代码完成问题。是的，Vim 确实提供了本机代码补全(从标签补全是在插入模式下用`C-x, C-]`完成的)。不，它可能没有你用像 Jedi plugin à la YouCompleteMe 这样的东西得到的强大，但我发现它经常满足我的需求，用`:grep`(或我自己的 [:GrepJob](https://git.danielmoch.com/vim-makejob) 以更自然的方式很好地填补了空白。

在这里你还可以做更多的事情。例如，如果你发现自己本能地用 tab 键来完成一个单词，有 [VimCompletesMe](https://github.com/ajh17/VimCompletesMe) ，它通过巧妙使用 [omni 补全函数](http://vimdoc.sourceforge.net/htmldoc/options.html#'omnifunc')来利用 Vim 的所有内置补全功能。这是可行的，但是用户确实放弃了对选择 Vim 使用什么数据源来完成特定任务的控制权。在我放弃 YouCompleteMe 后，我使用了这个插件一段时间，但最终还是删除了它，因为它有效地使 tab 键在插入模式下变得模糊不清。有时我想插入一个真正的制表符，但却得到了一个结束符。

有了所有这些，很自然会问 Vim 是否还需要语言服务器。我不打算在这里给出这个问题的答案，但是我要说的是，到目前为止，Vim 中语言服务器集成的许多解决方案看起来都是不值得的。也就是说，随着 Vim 8 及其异步功能的出现，这些解决方案还有改进的空间，我预计在不久的将来，其中的佼佼者会变得更加引人注目。

我不建议带着在终端上创建 IDE 的想法来使用 Vim。也就是说，Vim 是一个非常强大的工具，如果你花时间去学习它是如何工作的，它会带你走得很远。换句话说，在寻找一个插件来帮助你之前，尽可能地使用 Vim。任何人(像我一样)直接安装一堆插件——无论是疯狂地抓取任何看起来有趣的东西还是只是复制别人的配置——都可能以一个不可维护的混乱工具而告终，这个工具不能一致地工作，可能根本不能工作，或者工作速度与你想要摆脱的 IDE 一样慢。