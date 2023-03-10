# 软件测试人员的编码表

> 原文：<https://dev.to/eviltester/coding-katas-for-software-testers-o2a>

对于想学习如何编码来支持他们的测试的人来说，什么是合适的代码表？

代码形是一个非常流行的编程概念:

*   [https://www.codewars.com/](https://www.codewars.com/)
*   [http://www.codingdojo.org/](http://www.codingdojo.org/)
*   [https://en . Wikipedia . org/wiki/Kata _(编程)](https://en.wikipedia.org/wiki/Kata_(programming))
*   [https://leanpub.com/codingdojohandbook](https://leanpub.com/codingdojohandbook)

我不做代码形。约翰·索莫尼兹在这里写了他为什么不做[代码形](https://simpleprogrammer.com/dont-code-katas/)，这里是另一个来自弥迦·马丁的[代码形](https://8thlight.com/blog/micah-martin/2013/05/28/performing-code-katas.html)

## 我为什么不做代码形

*   时间
    *   我真的没有时间去研究代码形
*   有用
    *   当我学习一门语言时，我真的希望代码有用，因为这是我学习更多的动力。
*   我不想一直做同样的事情

但我最近意识到，我可能会做代码形，但我没有这样叫它们。

## 我的代号卡塔斯

我在以下代码中编写了 CounterString 实现:

*   VBA (Excel) [代码](https://github.com/eviltester/test-utils-written-in-excel)
*   VB(代码丢失)
*   Java [主代码](https://github.com/eviltester/testtoolhub)， [java](https://github.com/eviltester/testtoolhub/tree/master/src/main/java/uk/co/compendiumdev/javafortesters/domain/counterstrings)
*   JavaScript [预测](https://github.com/eviltester/TestingApp/tree/master/java/testingapps/compendiumdevapps/src/main/resources/web/apps/counterstrings)，[反转](https://github.com/eviltester/counterstringjs)

每次创建一个新的实现时，我都以不同的方式编写 CounterString。

随着对编程语言的了解越来越多，我重新审视了我的实现。

## 一次锻炼

如果你想使用 CounterStrings 作为练习编程的一种方式，并且有可能把它当作一个代码形。

以下是我的建议。

1.  编写代码生成以`*`开头的字符串，然后是`3*`，依此类推。到你的反字符串的长度，例如，这将创建一个类似`*3*5*7*9*1`的十字符反字符串。我将此描述为[正向反串生成](https://www.eviltester.com/2018/05/counterstring-algorithms.html#forward-counterstrings)
2.  编写代码来生成一个 CounterString，它与 [JamesBach 描述的](http://www.satisfice.com/blog/archives/22)相匹配，其中字符串以*结尾，具有所需的长度，每个*前面的数字是*在字符串中的位置，因此 10 个字符的 CounterString 将读作`*3*5*7*10*`
3.  不要将您的 CounterString 写到控制台，而是将其写到一个文件中
4.  不要将您的 CounterString 写到控制台，而是将其写到剪贴板
5.  不要把你的反字符串写到剪贴板上，而是让它输入到一个字段中
6.  找一个不同的实现方法，例如，如果你用了递归，把它改成别的，如果你没有用递归，试试看，如果你在反转字符串，试着不要反转字符串
7.  创建执行您的实现的自动化脚本，并将输出与不同的实现进行比较，例如 [James Bach 的 perl clip](http://www.satisfice.com/tools.shtml) 这可能最初是一个预先生成的文件或响应，但是创建一个动态调用 perl clip 的文件或响应
8.  对于“终极”挑战，请尝试编写一个实现，以 James Bach 描述的方式生成 CounterStrings，但要动态进行，以便您可以流式生成。我将此描述为[预测正向反串生成](https://www.eviltester.com/2018/05/counterstring-algorithms.html#predictive-forward-counterstrings)

有了这些，你会创造出一些有用的东西，支持你的工作。你会学到一些东西。

## 其他建议

我似乎一直在写的其他代码:

*   创建一个动词/名词文本冒险游戏
*   编写一个链接检查器
*   编写一个计算器，它接受一个字符串，例如“2+4+7”，并返回结果
*   使用生成语法编写测试数据生成器

所有这些都有不同的复杂性，但都提供了有用的学习工具。你会在我的 [Github repos](https://github.com/eviltester) 中找到以上所有的例子(但你可能需要搜索一下)。

你是如何练习编程的？你用什么练习来学习新的语言？

## 免费视频内幕

[https://www.youtube.com/embed/3n1ENZ1KE60](https://www.youtube.com/embed/3n1ENZ1KE60)

## 幻灯片

[//www.slideshare.net/slideshow/embed_code/key/GwLWV5jMZya5NG](//www.slideshare.net/slideshow/embed_code/key/GwLWV5jMZya5NG)