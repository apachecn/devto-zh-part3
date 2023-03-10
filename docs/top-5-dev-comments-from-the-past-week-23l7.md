# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-23l7>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

本周最受欢迎的讨论之一是 **[你最糟糕的面试经历是什么？](https://dev.to/sergio/what-was-your-worst-interview-experience-4d4n)** 线程。 [@avalander](https://dev.to/avalander) 加入了一个真正残酷的采访回顾:

[![avalander profile image](img/476a5291214fba12b6cca621e4079e74.png) ](/avalander) [ Avalander ](/avalander) • [<time datetime="2018-11-27T21:22:45Z" class="date-short-year"> Nov 27 '18 </time>](https://dev.to/avalander/comment/770p) 

我已经工作了大约一年半，我正在寻找一份新工作。我在约定的时间出现在公司。当我站在大楼门口时，我通过对讲机呼叫，说我在那里。他们告诉我要等待。他们不让我进去，显然我应该在外面等着。年长的我可能会当场离开，但这是年轻的我，天真无知，不知道一个工作的好地方是什么样的。

我已经在门口等了十五分钟了。我知道是因为我刚刚看了看表，我决定如果他们五分钟内不让我进去，我就要回家了。两三分钟后，当我正准备离开时，门终于打开了。

我礼貌地走进去，为面试做准备，但我已经决定我不想在这里工作。

面试本身很标准，有一个人力资源部门的人。他们问了我一些关于我的经历，我想从事什么样的技术，等等。然后他们问我在压力下工作是否出色。现在，年轻的我仍然很幼稚，但年轻的我一直在一个管理不善的项目中工作，加班是常态，知道“在压力下工作良好”是怎么回事。我回答说，当生产中断时，我会加班，但我珍惜我的空闲时间，我不觉得特别有必要用无薪加班来补偿缺乏适当的计划。采访很快结束。我再也没有听到他们的消息，从此以后我过着幸福的生活。

在一篇被大量收藏的名为 **[的文章中，你不知道你需要](https://dev.to/lynnewritescode/5-css-tips-you-didnt-know-you-needed-nb3)** , [@quantumsheep](https://dev.to/quantumsheep) 添加了另一个有用的提示:

[![quantumsheep profile image](img/b3a3d57dfeb10b65cb84704662b53188.png) ](/quantumsheep) [ Nathanael Demacon ](/quantumsheep) • [<time datetime="2018-11-29T08:13:03Z" class="date-short-year"> Nov 29 '18 </time> • Edited on <time datetime="2018-11-29T10:54:48Z" class="hidden m:inline-block date-no-year">Nov 29</time>](https://dev.to/quantumsheep/comment/77oo) 

我在每个项目中使用的最有用的技巧是:

```
* {
  box-sizing: border-box;
} 
```

Enter fullscreen mode Exit fullscreen modeEnter fullscreen mode Exit fullscreen mode
Enter fullscreen mode Exit fullscreen mode

它防止用边距、填充或边框打破框尺寸。

```
</div> 
```

Enter fullscreen mode Exit fullscreen mode

**中的家长评论[软件开发的未来薪酬范围是什么？](https://dev.to/ben/what-is-the-future-of-software-development-pay-range-13ff)** 描述了一个潜在的未来，编码职业最终会变得如此受欢迎，以至于工资大幅下降([完整评论](https://dev.to/leightondarkins/comment/7755))。 [@davidk01](https://dev.to/davidk01) 提出反驳:

未找到注释

[@canderson93](https://dev.to/canderson93) 跳转到一个 [#help](https://dev.to/t/help) 线程— **[为什么功能减速超越](https://dev.to/yashwanth2804/why-overriding-function-declaration---36n5)** —提供一个解释和一些有用的上下文:

[![canderson93 profile image](img/8821a4a2579bc3a413b4d02d8d01e593.png) ](/canderson93) [ Carl Anderson ](/canderson93) • [<time datetime="2018-11-30T10:33:33Z" class="date-short-year"> Nov 30 '18 </time> • Edited on <time datetime="2018-11-30T10:34:08Z" class="hidden m:inline-block date-no-year">Nov 30</time>](https://dev.to/canderson93/comment/78f7) 

这里发生的是，你已经找到了函数表达式和函数声明之间的区别。

```
function myName () {
console.log ("Rich");
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个函数声明，它受制于一个叫做*提升*的 javascript 特性，这个特性将它移动到声明它的作用域的顶部。这意味着当您设置`var myName = "Richard"`时，它实际上按照执行顺序排在后面并覆盖该函数。

相比之下，`myName = function() { ... }`是一个函数表达式，它在适当的位置进行计算，并按照您所期望的那样运行。

事实上，我刚刚写了一篇关于这件事的帖子-[carlanderson.xyz/function-declarat...](https://carlanderson.xyz/function-declarations-vs-expressions-explained/)

最后， [@nepeckman](https://dev.to/nepeckman) 在 **[分享了一些关于](https://dev.to/sergio/what-language-should-i-use-in-2018-if-i-want-to-easily-compile-a-single-binary-for-the-end-user-1npe) [Nim](https://nim-lang.org/) 的信息，如果我想~轻松~为最终用户编译一个单一的二进制，2018 年应该用什么语言？** 讨论线程。这让我渴望更多“倡导这种语言”的评论/文章:

[![nepeckman profile image](img/25b3cd1c7a898b5935662a2c8c4d3094.png) ](/nepeckman) [ nepeckman ](/nepeckman) • [<time datetime="2018-11-30T17:57:52Z" class="date-short-year"> Nov 30 '18 </time>](https://dev.to/nepeckman/comment/78k8) 

我要把尼姆扔进拳击场。它不像 Rust 或 Go 那样流行(也缺乏公司的支持)，但它确实是一种通用语言。比 Go(宏、泛型和 sum 类型)更强大，比 Rust(垃圾收集器，没有借用检查器的更宽容的编译器)更容易。它可以编译成适用于所有操作系统的静态二进制文件，具有 easy C interop，具有带 easy JavaScript interop 的 JavaScript 目标。它有一个很好的语法，感觉像一个脚本语言，但静态类型。真的很快。它可以做本地图形用户界面，CLI 工具，网络应用。我不能推荐它用于企业(还不行！)因为它还没有达到 1.0 版本，但它已经成为我的业余爱好语言有一段时间了，我真的很喜欢它。我是从一个 JS 重背景过来的，有问题尽管说！

下周见，更多精彩评论，✌