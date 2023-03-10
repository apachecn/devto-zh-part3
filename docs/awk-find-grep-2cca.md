# AWK, Find & Grep

> 原文：<https://dev.to/vitalipom/awk-find-grep-2cca>

初学者在 Unix 环境下处理文本时经常遇到困难，所以这是一个简短但全面的主题教程，它将涵盖你需要了解的关于`grep`、`find`和`awk`的一切。这也是我了解他们的方式。

在 Unix 中，你需要解析大量的文件，可能通过一些 regex (find 命令)过滤，并打印出结果(grep)，同时你有时还需要从命令行运行函数打印出一些参数(这是 awk，非常酷)。

首先，`find`命令有一个特殊的“过滤器”语法，你的标志结合了一个实际的过滤器，而且你可以结合一个布尔表达式。例如，您可以执行`filter -type -d (-iregex abc -o -iregex dbe) -exec...`，它将匹配任何匹配 abc 或 dbe 的内容，并在最终结果中组合它们。`-o`代表或，括号为实际括号。对于这些目录，它将执行一些命令...在`-exec`中，您可以指定“{}”这是一个特殊字符，用作找到的文件或目录的名称。Unix parses 接受这个命令，无论`{}`出现在哪里，它都会用文件/文件夹的名称替换它们。为什么是`{}`？因为在命令行中很容易找到并匹配它。

查找也将总是匹配所有子目录中的所有子文件。

如果你想 grep *里面的*文件，你可以运行 grep。Unix grep 在文件上运行，它做的是`cat`和一些脚本，用于根据需要抓取和打印正则表达式、包含它的行和 tge 文件名。但是需要记住的是 *grep* 运行在文件名上，它运行在文件上。目录中的文件，单个文件，但它将在文件上运行。当你陈述论点时，不要忘记这一点。

在 Unix 中，有时可以使用-P 标志设置 Perl 正则表达式，但并不总是如此。根据您的命令检查它，并学习 unix 正则表达式。

```
BONUS:
Confused on the file permissions? Take calculator on windows for once and for all.
2,3 will never used. It's only write and only execute write. Why would you write but not read for god's sake?
456 is easy. Read, write, read write exec. It's a poem. You always get a read permission, you add write to it or you add exec to it. 7 has them all. 
```

Enter fullscreen mode Exit fullscreen mode

Awk 很酷，因为它允许您在命令行中打印 n 的元素。它还有 BEGIN 选项，允许你打印出 xyz 的所有行，并用 grep 过滤它们(STDOUT 是它将运行的文件名，默认为 bytheway ),如果它——输出应该有一些标题，如 title 或 table，awk 可能会添加它，如果你在 BEGIN 语句中使用它，说明在开始打印什么。

在 awk 中，您可以在位于“”内的{}括号中声明您的 awk 命令(请记住这些是 awk 命令)。在 perl 中，使用$符号可以将 n-s 元素输出。

Sed 也在 tge 列表中，但感觉偏离了这个列表的主题。