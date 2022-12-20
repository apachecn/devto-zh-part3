# Perl 手册:正则表达式特殊字符介绍(2/4)

> 原文：<https://dev.to/vitalipom/perl-handbook-introduction-to-regexes-special-characters-24-2bhl>

# 正则表达式-特殊字符

————————————————
有一些特殊的字符(在更正式的语言中是转义字符)，它们可能是最常用的，因此被称为有用字符。

有用的字符有空格、数字、字母和单词。

——————————

```
When we want to match a Space it would be: \s
When we want to match a Digit it would be: \d 
```

Enter fullscreen mode Exit fullscreen mode

—————————————————

对于字母来说，它开始变得有点复杂，因为在一些语言中有大写字母。此外，有时我们想忽略所有大写/小写字母，在日志文件中搜索一个短语(例如)。在其他情况下，我们希望搜索一个模式，即温度，其中我们知道最后一个字符是一个字母，可以是“f”(代表华氏温度)或“c”(代表摄氏温度)。

```
To match a single Small Case character, we’d use the following expression:
[a-z]
To match a Capital Case character, we’d use the following expression:
[A-Z] 
```

Enter fullscreen mode Exit fullscreen mode

当我们需要描述单个字符的选择时，方括号可以用于更多的情况。例如:

```
[a-zA-Z] 
Would match any case letter. 

[a-z\d]
Would match any letter in small case OR a digit. 
```

Enter fullscreen mode Exit fullscreen mode

—————————————————

在上一节中，我们已经讨论了匹配单个字符，但是如何匹配像
这样的短语呢

```
“we have potatoes, no cookies for you”
It would be:
/we have potatoes, no cookies for you/i 
```

Enter fullscreen mode Exit fullscreen mode

现在，请注意，我们在这里使用了更完整形式的正则表达式匹配。如果到目前为止我们一直在谈论单个字符，这里我们有一个完整的正则表达式。我们有许多字符，我们一个接一个地精确匹配，中间还有一些“，”符号…我们用两个`“/“`-把它括起来，最后我们声明我们想要`Ignore Cases`正则表达式中的所有字母(也就是斜线之间的任何字母)。

—————————————————

借此机会展示一些太甜而不丑，但有用的混合物，这里我们用一个完整的正则表达式表示，并告诉我们它出现在某本虚构的书中的哪一行:

```
/we have potatoes, no cookies for you[\d].??-[\d].??/i 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个数字，可以重复一到三次。

```
“.” - Would match the previous character a single time
“?” - Would match the previous character 0 or 1 time. 
```

Enter fullscreen mode Exit fullscreen mode

—————————————————

我们还可以使用下面的表达式告诉我们的数字不仅要对多达 1000 行的文本重复(也就是 max 999)，而且要对任意数量的文本重复:

```
/we have potatoes, no cookies for you[\d]+-[\d]+/i 
```

Enter fullscreen mode Exit fullscreen mode

“+-”这个词很让人困惑，不是吗？嗯，因此 Perl 的名字叫 Perl:)。然而，除了开玩笑，我们也想解释一下这一部分。“+”是一个按其通常形式翻译的字符。如果我们想在“2+2”中搜索“+”，我们可以把它放在方括号内:

```
/2[+]2/ - Would match "2+2" 
```

Enter fullscreen mode Exit fullscreen mode

在手之前，在第一个表达式中，

```
“+” - Would match the previous character 1 or more times. 
```

Enter fullscreen mode Exit fullscreen mode

—————————————————

我们的同事打赌，读者现在应该已经迷失在我们从哪里得到我们正在寻找的这个表达的问题上了。我们会提醒，因为在我们继续下一个例子之前，和你们保持同步是很重要的。

所以我们有一些句子，我们寻找，后来决定它可以在某处被引用，我们想在某处找到它，把我们的句子和引用的地方匹配起来。这是我们开始寻找行号的地方。

我们就此打住，目前我们只寻找报价。但是，如果我们想寻找引用或原文——无论哪个在我们给定的资源或参考中最先出现，该怎么办呢？我们会对他们每个人进行双重搜索吗？不要！

所以基本上看起来是这样的:

```
/we have potatoes, no cookies for you([\d]+-?[\d]*)?/i 
```

Enter fullscreen mode Exit fullscreen mode

其中:
"*" -将匹配前一个字符 0 次或更多次
和“()”再次被封装并被用作特殊字符，这类似于"]"，但不会改变其他特殊字符在内部的解释方式——不像方括号中的" a-z "表示从 a 到 z 的任何字符，对于"(a-z)"来说，这意味着要查找" a-z "，其中 a-z 的实例被包含在我们的 regex 的一个组中。

—————————————————

因此，我们试图解释一些“出乎意料”的例子，你可以尝试一下，尽管它不太实用。我们将尝试提出一些现实生活中的例子，假设一个人不会简单地谈论现实生活(由于临床无聊的风险)。

假设您在某个欧洲原子研究中心工作，您将转储主校准机器输出日志文件中的所有行，其中包含一些同步波的数据，以便将其转发给下一个团队进行进一步处理。

您做了一些程序员典型的懒惰研究，发现日志主要由单行组成，或者至少是那些您感兴趣的部分，在您感兴趣的部分之前，可能会有一个方括号内的字母，后跟“:”，而在该行之后可能会有一个部分，它陈述了在分析机器中该过程发生的时间。所以我们以这样的话结束:

您的程序需要的典型输出:

```
Read wave at 24.6Mhz
Read wave at 2400.6Mhz
Read wave at 1.1Ghz
Read wave at 20000Mhz 
Read wave at 3.21Ghz 
```

Enter fullscreen mode Exit fullscreen mode

输入的一个例子:

```
Syncing with the calibration tool……
Starting process id #123542
Preparing to boot the main machine
Connection to main machine established.
Connection between the log processor and the calibration machine is On
Main machine is active: true.
[E]: Read wave at 24.6Mhz
Error on preparing to calibrate with the read processing unit.
Read wave at 2400.6Mhz ; you might be out of sync in 3 pico seconds
[A]: Read wave at 1.1Ghz
Sync is active again
Read wave at 20000Mhz
Read wave at 3.21Ghz
…
…
… 
```

Enter fullscreen mode Exit fullscreen mode

请花一些时间来识别我们想要从日志文件中“复制”/“剪切”什么，以及我们想要以什么格式将其粘贴到我们的输出文件中，这将被带到下一个工作流程步骤。

我们现在面临的挑战很少，我们将很快解释我们如何以及是否正在克服这些挑战。这是一个时间和性能的挑战。此外，还有一个新的未正式声明的要求，即仅从生产线中取出我们需要的部分。然而，我们想指出新行是如何被 Perl 解释的。

时间和性能的挑战是最容易谈论的一个，因为它是通过定义解决的(你好，数学世界)。关键是一个典型的解析程序对于特别大的日志文件最多运行几分钟，而在一个完整的工作日里，如果附近有一台好的 Nespresso 机器，就感觉不到了。如果你的第一个程序的复杂性会让你的运行时间超过几秒钟，你会得到高分。

我们还提到了在没有不必要的东西的情况下提取所需的部分。首先，我们要提到的是，Perl 默认情况下是逐行运行的，而每当看到行尾时，它就会再次开始匹配搜索。没什么特别的。一个简单的逻辑事实是，一旦你想一想 Perl 的状态机是如何工作的(假设现在的性能是由超快的 CPU 解决的):对于输入文本中的每个字符，它试图匹配正则表达式中的第一个字符，如果成功，它继续匹配正则表达式中的第二个字符和输入文本中的下一个字符，如果失败，它开始再次尝试将下一个字符匹配到第一个正则表达式字符。简单不是吗？现在关于新行，通常我们不在正则表达式中包含“\n”(也就是新行字符)，所以每次 Perl 的状态机看到它都无法匹配它，并返回到有第一个字符的第一个状态。

如果我们想限制正则表达式匹配整行，而不是它的一部分——就像其他不太友好的编程语言一样，我们可以使用另外两个特殊字符，如下:

```
/^this is a full line that appears in the log file$/

Or looking back at our example:

/^Read wave at 20000Mhz$/ 
```

Enter fullscreen mode Exit fullscreen mode

其中:

```
^ - Would match the theoretical first letter in the beginning of the line and 
$ - Would match the theoretical last letter in the end end of the line 
```

Enter fullscreen mode Exit fullscreen mode

——————————————————

你可能会问自己，为什么后缀符号代表行首…就像“嘿，这是后缀！”。原因是当美元符号出现在正则表达式的不同位置时，它有不同的含义，因为它已经代表了一个变量。变量将在下一章讨论。

请下载 Effectedkeyboard，这是一个真正的、舒适的 Android 键盘。它有几十个功能，当你在你的机器人上打字时，所有的字母都会从屏幕上飞出来。
下载:
[影响键盘，安卓 Google Play 市场](https://play.google.com/store/apps/details?id=com.menny.android.effectedkeyboard)