# 命令魔术:如何从终端操作文件和字符串

> 原文：<https://dev.to/strikingloo/command-magicks-how-to-manipulate-files-and-strings-from-the-terminal-35b1>

在本 shell 教程中，我们将处理需要与文件或字符串交互的任务，这些任务可以更容易地从终端完成。

作为开发人员，我们每天都会做很多重复的事情，这些事情占用了我们宝贵的时间。寻找自动化和优化这些流程的方法通常非常有利可图。

很多时候，我们发现自己在程序的输出中寻找相关的位，并手动将它们移动到不同的文件中，将一个句子中的所有大写字母改为小写字母，或者从文件中删除所有非数字字符。那种枯燥、重复且容易出错的任务，如果我们用手去做，就会堆积起来，变成一件令人头疼的事情。

传统观点认为，我们应该通过编程而不是手动来完成这些事情。很多时候，问题都陷入了这个甜蜜点，为它编写一个完整的脚本，即使是用 Python，感觉也是多余的。手工操作会花费太长时间或者产生太多错误。

幸运的是，许多任务已经被比我们聪明得多的人编写了代码。只需按几下键就可以访问它们。它们都可以作为 shell 命令使用，今天我将向您展示其中的一些。如果你对终端完全陌生，不知道如何导航你的文件系统或做类似的任务，我建议你读一下我之前对终端的介绍。

所以事不宜迟，让我向你介绍任何编码向导都应该知道的最有用的咒语。

#### **echo:使一个字符串出现在控制台**

在我们深入占卜和转换的艺术之前，一个真正的编程向导必须掌握魔术的技艺。

echo 命令，后跟一个字符串，将简单地使控制台输出作为输入给出的内容。例如，运行下面一行:

```
echo “hello world!” 
```

Enter fullscreen mode Exit fullscreen mode

将产生以下输出:

```
hello world! 
```

Enter fullscreen mode Exit fullscreen mode

这可能现在看起来微不足道，但我保证它在将来会很有用。

#### **猫:显示输入的真实形式**

对一个文件调用 cat 命令会将其内容输出到终端。

例如，我们有一个包含文件‘file 1 . txt’和‘file 2 . txt’的目录。两个文件都包含文本“这是一个文件”。来电:

```
cat file1.txt 
```

Enter fullscreen mode Exit fullscreen mode

将输出文件的内容:

```
this is a file 
```

Enter fullscreen mode Exit fullscreen mode

注意，cat 命令的参数可以是任何 shell 样式的名称。我们可以使用通配符*来匹配任何字符串。这样，我们可以一个接一个地输出不同文件的内容，就像这样:

```
cat \*.txt 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，*匹配文件 1 和文件 2，并且它们都以结尾。txt，所以都是打印的。该命令的输出将是

```
this is a file
this is a file 
```

Enter fullscreen mode Exit fullscreen mode

记住这个命令——没有一个术士没有一只小猫是真正完整的。

#### **grep:大海捞针**

切换到占卜，grep 是在字符串中查找子串的咒语。

呼唤

```
grep \<some string\> filename 
```

Enter fullscreen mode Exit fullscreen mode

将输出指定文件中出现给定字符串的每一行。

如果我们希望它不仅以准确的形式出现，而且以不同的大小写出现，我们必须通过 **-i** 参数来忽略大小写。

如果我们在一个命令中对不同的文件调用它，我们将得到每个文件的列表，其中的行与模式匹配。例如在前一个目录中，调用

```
grep “this” \*.txt 
```

Enter fullscreen mode Exit fullscreen mode

会屈服

```
file1.txt: this is a file
tile2.txt: this is a file 
```

Enter fullscreen mode Exit fullscreen mode

#### **sed:将一个字符串转换成另一个**

sed 命令是一个转化法术。它获取一个文件的内容并把它们转换成不同的内容。有许多使用它的方法。其中一些我承认知道的很少。(如果你正在阅读这篇文章，并且想到了一些我没有提到的 sed 做的很酷的事情，请在评论中告诉我，因为我喜欢学习新的技巧)。最常见的一种方法是用不同的字符串替换字符串中与模式匹配的部分。

这是通过调用
来完成的

```
sed “s/regexp/replacement/optional\_flags” file\_name 
```

Enter fullscreen mode Exit fullscreen mode

这将做的是:

*   在 file_name 文件中查找与 regexp 匹配的每一行
*   用*替换*替换该行的第一个 *regexp* 实例
*   将结果字符串输出到控制台(不改变文件！).

如果我们在末尾提供 g 标志(比如 s/old/new/g ),它将匹配每一行的所有实例，而不仅仅是第一个。使用 **-i** 参数(就地)将实际写入输入文件。

举个例子，调用

```
sed “s/is/was/g” file1.txt 
```

Enter fullscreen mode Exit fullscreen mode

将输出

```
thwas was a file 
```

Enter fullscreen mode Exit fullscreen mode

如果我们只想匹配整个单词，我们必须将\b 字符放在正则表达式的两边，就像这样

```
sed “s/\bis\b/was/g” file1.txt 
```

Enter fullscreen mode Exit fullscreen mode

最终得到

```
this was a file 
```

Enter fullscreen mode Exit fullscreen mode

## 结合符咒:符咒

现在你精通了四种新的魔法学校，每一种都有其特有的法术。但是要成为一名真正的巫师，你必须学会把魔法线绑成令人敬畏的图案。为此，您将使用三个强大的工具。

#### **|(管道)操作员**

管道操作符获取前一个命令的输出，并将其写入下一个命令的输入，从而创建一个管道。

比如，打电话给

```
cat \*.txt | grep “is” 
```

Enter fullscreen mode Exit fullscreen mode

将首先获取当前工作目录中所有文本文件的内容。然后查找包含字符串“is”的每一行，最后打印出来。

#### **>(写)符**

写操作符将把它的输入写到它的输出——通常是一个文件。

例如，创建一个以“这是一个文件”为内容的文本文件的快速方法是调用

```
echo “this is a file” \> some\_file.txt 
```

Enter fullscreen mode Exit fullscreen mode

看到那个魔法咒语是怎么加起来的了吗？我告诉过你会有用的。

请注意，如果文件已经存在，这将覆盖其内容，甚至没有询问。如果这不是我们想要的，我们必须使用我们最后的工具:

#### **> >(追加)运算符**

>>操作符将把它的输入写到它的输出中，除了它不会覆盖已经存在的内容。

就这样，我们完成了这个教程，你现在是一个巫师的学徒。去练习你新的施法技巧吧，你可以以后再感谢我。如果您遇到困难或不记得一些标志做了什么，请务必查看所有这些命令的手册页——一个向导永远不会离开他的书。

如果你想更深入地学习更多的命令和用法，我强烈推荐 [O'Reilly 的 Bash Cookbook](https://www.bookdepository.com/book/9781491975336/?a_aid=strikingloo&chan=ws) 作为一个很好的起点。

*在 [Medium](http://www.medium.com/@strikingloo) 或 [Twitter](http://www.twitter.com/strikingloo) 上关注我，了解更多教程、技巧和诀窍。*

*如果你喜欢这篇文章，请与朋友分享！*

post [命令魔术:如何从终端](http://www.datastuff.tech/programming/files-strings-shell-tutorial/)操作文件和字符串首先出现在[数据素材](http://www.datastuff.tech)上。