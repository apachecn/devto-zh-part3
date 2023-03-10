# 如何开始使用终端来提高工作效率

> 原文：<https://dev.to/strikingloo/how-to-start-using-the-terminal-to-be-more-productive-a9l>

作为开发商，终端可以是我们的第二个家。

然而，在我们学会如何使用它之前，我们不能使用它，并且需要练习使用它来学习——这有点像第 22 条军规！

我希望这篇介绍能为您解决这个难题。阅读完本教程后，您将能够立即开始使用终端。

### 入门

我将首先介绍基础知识，因此如果您知道这个小型 bash 教程中的所有内容，请继续关注下一篇教程，在下一篇教程中，我将讨论更高级的主题。

考虑到这一点，我将从头开始。如果你在 Ubuntu 上，你所要做的就是打开你的终端，按下 *ctrl+alt+。_ 在 Mac 上，你应该按下 _ cmd+空格键，*开始键入*终端*，当选项出现时按下回车键。

在这两种情况下，您都应该看到一个黑色背景，您的用户名后面跟着您的电脑名称(在 Linux 中)或相反的顺序(在 Mac 中)。

我强烈建议您打开自己的终端，在一个空的目录中尝试这些命令，亲自查看并掌握它们的窍门。

您将看到一个提示，邀请您键入命令。要输入命令，只需输入并按回车键。一些导航命令包括:

#### **光盘:移动你的工作目录。**

```
cd \<relative path\> 
```

Enter fullscreen mode Exit fullscreen mode

这将使您的终端指向一个不同的目录，从那里您可以运行新的命令。例如，如果你在一个名为 *animals* 的文件夹中，其中有三个文件夹*猫*、_ 狗 _ 和 _ 乌龟 _，你应该运行

```
_cd turtles_ 
```

Enter fullscreen mode Exit fullscreen mode

移动到该目录中。要从当前目录上移一级(例如，从*海龟*移回*动物*，点击

```
cd .. 
```

Enter fullscreen mode Exit fullscreen mode

#### **mkdir 和 touch:创建文件夹或文件。**

如果你需要创建一个新的空目录，你只需要运行

```
mkdir \<directory name\> 
```

Enter fullscreen mode Exit fullscreen mode

反之运行

```
touch \<file\_name\> 
```

Enter fullscreen mode Exit fullscreen mode

将在当前工作目录中创建一个空文件，以第一个参数作为其名称。

如果具有给定名称的另一个文件已经存在，这将仅更新文件的上次更新日期。它不会对其内容进行任何更改。

但是我能知道这个文件是否存在吗？!'你问。很高兴你这么问。

#### **ls:查看一个目录的内容。**

命令按字母顺序列出了当前工作目录中每个文件和目录的名称。你可以用破折号给它传递几个参数，像这样:

```
ls -a -l 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下， *-a* 参数使 *ls* 显示不可见的文件。 *-l* 命令使输出看起来像一个列表。它为每个项目显示一行，还有一些额外的数据，如每个文件的大小或创建日期。

我最喜欢的关于 *ls* 的一个论点是 *-R，_ 它递归地调用每个列出的子目录上的*ls _ 来快速查看存储库或文件树。

请注意，对于所有命令，参数实际上可以在单个破折号之后组合在一起:

```
ls -alR 
```

Enter fullscreen mode Exit fullscreen mode

现在我听到你在问‘我到底要怎么记住所有这些论点和选项？所有的命令都有这么多疯狂的功能吗？但是不要担心，我们会保护你。

#### **男人:永远不要停止学习！**

如果你在 Stack Overflow 或 Reddit 上呆过，你可能会遇到“阅读手册页”这个短语，它要么是教育意义上的，要么是一种侮辱。

我这里是第一次使用。

尝试运行

```
man \<command name\> 
```

Enter fullscreen mode Exit fullscreen mode

它将显示该命令的手册页——官方文档，以及所有可能的参数和用法。当我们确信某个程序做了某件事时，我们大多数人都会使用它，但是我们不太记得是哪个标志让它这么做的。第一次使用命令时(例如，如果它出现在 google 搜索结果中)，调用 man 命令也是非常好的，这样可以更好地了解它，并找到调用它的更好方法。要关闭手册页，只需按下 *Q* 即可。

#### **头和尾，猫和少:读取一个文件的内容。**

在一个文件上调用 *head* 或 _ tail _ 将分别显示它的第一行或最后 10 行。

你可以用一些很酷的论点来称呼它:

*   * *-n <数量> : ** 显示 _ 数量 _ 行数而不是默认的 10
*   * *-f ** (用于*尾巴*):实时显示行，不要停止(当您 *ssh* 进入服务器时，非常适合于标记日志文件)

调用 *cat* 将简单地显示文件的内容。确保你在实际的文本文件中使用它，否则你会看到一些奇怪的东西。

如果你在一个大的(或者更大的——老实说，有点像 T2 的)文件中调用 *cat* ，你可能会发现不停地上下滚动来寻找相关的行是非常笨拙的。实际上有一种更方便的方法:less 命令。

less _ 将通过缓冲的方式加载文件的内容来显示文件的 less _ 内容。你可以用箭头键滚动文件，而不是用鼠标滚轮/触摸板，这样舒服多了。你也可以按/，输入一些东西，然后按 Enter 键来搜索文件(就像使用 *ctrl+f* )。

要退出*少*模式，只需按 *Q.*

#### **cp 和 mv:复制、剪切、粘贴。**

*cp* (copy)和 *mv _(move)分别是* copy *和* cut，_ 的 bash 等价物。你可以这样使用它们:

```
cp \<source\> \<destination\> 
```

Enter fullscreen mode Exit fullscreen mode

将*源*中的文件复制到*目的地。*

源文件可以是一个文件，也可以是一组文件。要选择多个文件，可以利用 bash 的通配符: ***** 。该字符将匹配任何字符串，甚至是空字符串。

例如，该命令会将 *some_folder* 文件夹中的所有文件复制到 *some_other_folder* 文件夹中，该文件夹位于文件系统中的上一级。

```
cp some\_folder/\* ../some\_other\_folder 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们只想移动。txt 文件放到一个名为 *texts、*的目录下，我们就用:

```
cp \*.txt texts/ 
```

Enter fullscreen mode Exit fullscreen mode

因为*匹配任何字符串。Ee 在 *.txt. _(例如，* *中强制结束。txt *匹配* filename.txt，*因为* * *匹配* filename，*但不匹配* filename.xtt，*因为即使*匹配整个名字，也没有匹配 _ 的。txt)。*

如果源是单个文件，目标可以是文件的路径(覆盖该路径中的当前文件，如果它存在，或者创建一个新文件),如果您希望复制/移动多个文件，目标可以是目录名。

#### **rm:删除文件和目录。**

与 _touch 相反，rm _ deletes 文件或目录。

以默认形式使用它

```
rm file\_name 
```

Enter fullscreen mode Exit fullscreen mode

删除文件时可以工作，但删除目录时会抛出错误。这可以防止我们删除一个目录中的重要文件，或者认为整个目录只是一个文件。

要绕过这一点，如果你觉得有勇气，只需添加 *-r，_ 递归删除目录中的每个文件，直到它变空，然后像某种串行删除程序一样删除它。如果您只想删除空目录，可以使用 _-d* 来代替。

请注意，您始终可以使用通配符(*)在一个命令中删除多个文件或目录。例如，调用

```
rm \*.txt 
```

Enter fullscreen mode Exit fullscreen mode

从当前工作目录中删除所有文本文件。

#### **结束…现在。**

咻，那是一些介绍。现在，您已经熟悉了日常编程中最常用的命令。

有很多事情我还没有涉及到。我计划跟进更多的用例、更多的命令和更多要解决的实际问题。

在我准备下一篇文章时，我想鼓励您自己尝试这些命令。看看哪些能节省你的时间，并习惯这整个终端的事情。也许把这篇文章收藏起来，作为参考。我不会告诉任何人。

我向你保证，过一段时间你会开始明白为什么它是值得的。(我知道这花了我一段时间)。最终，每当你开始做某事时，你会本能地打开终端。

我希望这些介绍对你有用，如果有用，请告诉我！我非常重视读者的反馈。这是我写这篇文章的主要原因，所以请告诉我是否有些部分很难理解，有些命令看起来没用，或者我的教程太无聊了。也让我知道如果一些部分是有趣的！

如果你想更深入地学习更多的命令和用法，我强烈推荐 [O'Reilly 的 Bash Cookbook](https://www.bookdepository.com/book/9781491975336/?a_aid=strikingloo&chan=ws) 作为一个很好的起点。

*[第二部分已经有了](http://www.datastuff.tech/programming/files-strings-shell-tutorial/)* 。

*在 [Twitter](http://twitter.com/strikingloo) 或 [Medium](http://medium.com/@strikingloo) 上关注我，了解更多编程教程、技巧和诀窍。*

帖子[如何开始使用终端变得更有效率](http://www.datastuff.tech/programming/terminal-tutorial-more-productive/)首先出现在[数据素材](http://www.datastuff.tech)上。