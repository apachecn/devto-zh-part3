# 如何更好地实现:第 1 部分

> 原文：<https://dev.to/abhishekalbert/how-to-make-implementation-better--part-1-31i2>

你好。

[![](img/8e2da54e2a323837227159195772ca25.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JfPsXUMb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5ax24ypj7s1uwyedlxr2.jpg)

我是 Abhishek，这是我的第一篇文章，希望你会喜欢。或者你可以说这是关于我的编码之旅，我如何开始，如何让代码更有效。还有一件事，如果你认为这篇文章中的任何一点是错误的，那么不要犹豫，只要在评论区评论哪里是错误的。所以让我们开始为什么我们在等待:
从现在起大约一年后，我选择开始编写代码，或者你可以说我在大学入学时听说了编程。在此之前，我不知道什么是编码或编程/编程语言，甚至我不知道编码的 C。相信我，这是真的。
现在，我在二年级。是的，我是大学生。所以还是回到我们的话题吧。所以大学录取的时候，拿了一堆书。这次我很兴奋，开始一本书一本书的看，最后我得到了一本关于 C 语言编程的书。是的，我的第一个编程语言是 c。在看过一些章节和一堆代码的简短描述后。现在我很害怕，首先我想到了放弃，但是电脑的工作和我们周围的许多应用迫使我走向学习。这就是为什么我在第一学期上课前 15 天参加了一个辅导班学习 C 语言。是的，在短短的 15 天里，我学会了 C 语言。而这 15 天，就是我爱上编程语言的那一天。每天 1 小时，我阅读一些理论部分，然后尝试编码。但是正如你所知道的，这并不像我想的那么简单。但是我没有放弃，更加努力。现在我能够更高效地实现代码。所以我只是观察我所面临的问题，然后列出一张清单，稍后我会逐一解释。

[![](img/14f4906e2b648f06b4464a76b12be017.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cJM0V438--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w9s9ozimxpzuam54dfct.jpg)

首先，在进入主要观点之前，这是非常基本的，一般每个人都知道。但是在我们的编码过程中，我们错过了它们，因为我们不能很好地实现它们，这就是为什么把它们弄清楚是很重要的。那么在开始写代码之前，你有没有想过一些技术词汇，比如程序、程序员、指令、代码、语法等等。

我只想问你一个简单的问题“你知道它们实际上是什么吗？”
“这些单词的基本意思是什么？”

为了简化，想象一个场景。你的上级给你一个任务，教一个家伙编程的基础，但问题是他从来没有听说过，你必须教一个非极客的人编程，那么你将如何管理。休息一下，想一想，然后开始阅读剩下的内容。

[![](img/df2482893052a72b8b88bc72cd5ec17a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iRGWQ0Uj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1xal16nd7towannhq367.jpg)

现在让我从我的角度解释一下这些是什么。

程序:如果你在谷歌上搜索，我们会发现*“程序是计算机执行的一组特定的有序操作”。*好的，但真正的作用是什么，或者更好地理解你将如何与我们的日常生活相关联。所以让我们从日常生活中举个例子，大多数时候我们的父母/老师让我们做某项任务，然后我们开始按照他们说的去做。类似地，计算机也做同样的事情。你在里面写的东西(用代码写的)把它当成一个指令，在最后一路跟着它，给你一些结果。

程序员:现在出现的第二点是什么是程序员或者他们是谁？所以答案很简单*“一个能通过编程语言写几行代码或给出一些指令以得到他们想要的输出的人被称为程序员。”*这也是一个标准定义，那么简单的定义是什么呢？如果你通过定义你会发现，而不是问谁是程序员？**我们要问的是，程序员到底是做什么的？**仔细想想再去翻我的话。实际上，当你问这类问题时，几乎 98%的人都会遵循标准定义。那么他们实际在做什么呢？
答案是简单的，就像其他人一样(例如，机械工程师设计、建造机器或建筑工人制造家、舒适的办公室、高效的工作等等。)他们还试图在他的知识基础上做出贡献，以简化计算机世界或技术世界中的事情。*T9】*

现在来看我们的下一点**指令:**指令是关于什么的？简单地说，它就像一个以特定方式做事的命令。听起来怪不怪？？我们再举一个例子。你要去办公室、学校或做其他工作。在旅途中，一个陌生人向你求助:“你能告诉我地铁站在哪个方向吗？因为我迷路了。”现在有两种可能，第一种是你知道的，第二种是不好意思。现在，如果你知道路，那么你可以指导他“如何到达车站”，但是如果你不知道，那么你会告诉他你也不知道到达车站的正确方法。然后他会去请求任何其他人的帮助。所以在这两种情况下，如果你看到他会按照你的指示去做。所以你对他说的话他会当作命令来执行。希望现在这一点已经清楚了。

所以现在我们的下一个点是**代码**。现在的问题是什么是代码？
Hmmm 代码是程序员做的事情。更好，还是不好。哈哈，开玩笑的。

事实上，如果你仔细想想，你会发现这是一个棘手的问题。考虑一下，因为当你开始写程序时，它起着非常重要的作用。当你深入标准定义时

> 计算机代码或程序代码是形成由计算机执行的计算机程序的指令集。...这个源代码由编译器或解释器翻译成机器代码，以便计算机可以执行它来执行它的任务。”

you will found the there are 2 meaning of this definition .
1st definition will be according to you or a programmer what is he wants as a output .
2nd will be according what you write inside the code computer will give output.

现在再休息一下，想想哪个是对的第一个还是第二个？？

[![](img/7d3ab9deba9c5fb8c8aab6f0b436b1f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yzp7WNsk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xo72frcqifbf844603vx.jpg)

所以如果你反复阅读，你会发现第一和第二都是部分定义。那什么才是正确的定义呢？？？让我们看看。

首先，如果我们写一个代码，让我们举一个例子，一个 2 数相加的程序，然后我们简单地给出 2 数作为输入，然后想要一个输出，比如说 C，但是计算机给出了另一个输出，比如说 D，意味着你没有得到想要的结果。现在作为一个程序员，你会开始认为你写的代码看起来是对的，但是为什么计算机没有像你想的那样理解，这意味着他理解不同的意思或观点，所以你必须以这样的方式写代码，计算机会得到你认为你需要修改的代码。所以当我们总结第一点和第二点时，代码的定义就完成了。

现在第一部分我要休息一下，否则会很长，因为这会变得很无聊。

[![](img/f33365c33f9644023f228e2148a85b16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f4fzdxzp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/efjr2762ryrb68pi16jb.jpg)

我想和你讨论的另一点是，一般来说，我们中的许多人开始放弃并停止编码，因为他们中的一些人认为这太难了，他们不适合。让我们尝试其他的东西，或者他们中的一些人认为他们没有关于它的先验知识，或者他们属于不同的领域。我只想说一件事，你们都错了，试着放弃，因为你不想离开你的舒适区，就是这样。那么你将永远得不到任何东西，因为要得到某样东西，你必须离开你的舒适区，并且需要一种献身精神去学习。

[![](img/e591d9228b0967649df4c3950f5f336f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r_MHZhPY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jho62uprn8psehvkvto4.jpg)

要学习代码，你只需要下面列出三样东西

1.  渴望学习。2.你能做到的信心。
2.  努力工作/聪明工作。

相信我，成为程序员只需要这三样东西，其他什么都不需要。如果你有，那么恭喜你。你只是向我们的编程世界前进了一步。很快你就会看到它有多美。所以我只想说

> 欢迎来到我们美丽而富有创造力的世界

[![](img/862f4df2f7b656d9d05148d5229de826.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a0ERRPwV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d6aipnl2tu02kkv3zsyk.jpg)

希望你们喜欢它，如果你发现任何问题，请评论。

在我们的下一篇文章中再见！

编码快乐！！！！