# 解析脚注和代码块(MDL 日志#4)

> 原文：<https://dev.to/mortoray/parsing-footnotes-and-code-blocks-mdl-log-4-80i>

在这次更新中，我增加了脚注解析和基本渲染 <sup>[1](#note-1)</sup> 。这是由 Mogo 的用户故事激发的，他需要在文章中添加源信息。这是我写健康相关文章时需要的东西。脚注提供了一种连接单词的替代方法——在引用来源时，脚注在许多情况下更自然。

## Mogo 的用户画像

我正在做用户案例，因为它们帮助我检查我现在需要什么功能。我没有走完美库代码的路线，而是试图让所有主要用户特性首先工作。这对扩展特性很有帮助:我已经有了需要支持的所有东西的例子，减少了对设计错误的担心。

> 如果你想快速了解用户故事，请查看我在 Skillshare 上的[课程。](https://skl.sh/2HJdiyk)

[![Photo of Mogo](img/5fc5f421e6aea80a0ec340405995810d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--edPkt6kZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7x0501l2pia4zcrasaoz.jpg)

莫戈是一名自由医学记者。免费到各国旅游，主要是非洲，并在著名的在线杂志上发表健康文章。她进行当地采访，以获得对事件的个人感受，并发现对这些社区来说什么是重要的。她通过在科学出版物上搜索来源信息来补充她的采访

该杂志对发表文章有非常严格的要求。在写之前，他们需要提纲，并会提供反馈。他们有一个 Word 模板来提交提纲。像大多数作家一样，在完成大纲之前，Mogo 开始记录她的想法和采访笔记。然后，她手动将关键部分转移到标题模板中。当反馈被返回时，她然后手动更新她的源注释。

该杂志需要广泛的来源信息，包括采访参考和研究来源链接。这必须在 Word 中作为尾注完成，并在文本中包含数字链接。Mogo 花了很多时间清理这些信息，因为她忘记更新链接或笔记。偶尔，她也会删除带有文本的源。这是她工作中乏味的一部分。

这让我们衍生出两个相关的用户故事，第一个，简而言之就是“一个用户，将导出一个大纲，以满足出版前期的规划”。听起来还行。

但是下一个特性是关于使用 MDL 的各种脚注特性，并把它们格式化成目标格式。用标准的用户故事简写形式来记录有点困难。说“一个用户，会使用各种各样的内联、外部和结尾注释，因为他们想要有统一的脚注”有点罗嗦和混乱。这就是为什么我不赞成严格的用户故事形式。我们可以说，“Mogo 希望方便地将 MDL 笔记导出到她出版物所需的格式。”

和上次一样，我在这里将重点关注底层的 MDL 特性。该项目对用户界面层还不感兴趣，而这正是用户最希望的。虽然可能不是，但许多内容作者并不关心他们使用什么工具，满足他们其他需求的文本编辑器可能会被一些人接受。

## 脚注

脚注允许在句末用数字链接到文档末尾的相应条目。但是注意，在 MDL 中，它们只被称为“notes”。由渲染器决定如何渲染它们。HTML 呈现器提供一个弹出框是合理。不幸的是，这种弹出窗口在支持降价的系统中是不可能的。

这看起来像是一个奇特的特征，但是它的缺乏给我自己的写作带来了一些悲伤。在写研究材料时，脚注是很常见的。

MDL 为生成脚注提供了两种选择。要么是内联 <sup>[2](#note-2)</sup> ，要么是联动 <sup>[3](#note-3)</sup> 。我们来看看那个语法 <sup>[4](#note-4)</sup> :
的语法

```
Either they are inline^[MDL on Github](https://github.com/mortoray/mdl/), or they are linked^linked. 
```

Enter fullscreen mode Exit fullscreen mode

内联链接是引用源的一种快捷方式。链接注释是保持文本整洁以便编辑的一种方式，也是为注释提供文本块的一种方式。链接的音符由^后跟一系列字母来表示。在本文档的后面，这与注释的定义相匹配:

```
^linked The body of a note is regular MDL, allowing _formatting_ and [linking](https://edaqa.com/what_is_programming/). 
```

Enter fullscreen mode Exit fullscreen mode

如果这些代码样本在我发布时与本文的代码不同步，我深表歉意。作为一个例子，在文档内部复制粘贴文档的一部分有点奇怪。

* * *

*这是 [MDL 文档处理器项目](https://github.com/mortoray/mdl/)的开发日志条目。*

* * *

1.  看，这不是谎言。从介绍到这里确实有一个脚注。
2.  [Github 上的 MDL](https://github.com/mortoray/mdl/)
3.  笔记的主体是常规 MDL，允许*格式化*和[链接](https://edaqa.com/whatprogramming/)。
4.  为了展示脚注是如何工作的，添加代码块也是必要的。