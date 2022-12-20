# 搞什么~ | & | & & $！`()?

> 原文：<https://dev.to/walkingriver/what-the-177l>

这些符号是什么意思？我最近在 Twitter 上看到有人发帖说，如果在两个 shell 命令之间放置一个&符号，它们就会并行运行。虽然这在某种程度上是真实的，但这条推文并没有抓住真正发生的事情的本质。所以我决定回归基础，提醒我自己(和其他人)这些和其他奇怪的符号在 Linux、MacOS 甚至 Windows (Bash)终端中的作用。

这是我提到的推文...

> 您是否知道双&将顺序运行多个脚本，而单&将并行运行它们？[# JavaScript](https://twitter.com/hashtag/javascript?src=hash&ref_src=twsrc%5Etfw)[#节点](https://twitter.com/hashtag/node?src=hash&ref_src=twsrc%5Etfw)[# NPM](https://twitter.com/hashtag/npm?src=hash&ref_src=twsrc%5Etfw)[# web developer](https://twitter.com/hashtag/webdeveloper?src=hash&ref_src=twsrc%5Etfw)🤯[pic.twitter.com/58P92Bo3AI](https://t.co/58P92Bo3AI)
> 
> — Dan Vega ([@therealdanvega](https://dev.to/therealdanvega) ) [April 11, 2019](https://twitter.com/therealdanvega/status/1116403685452668928?ref_src=twsrc%5Etfw)

*注意:下面包含的例子都是在运行 bash shell 的 MacOS 终端上测试和运行的。它们应该可以在 Linux 的任何其他常见 shell 上工作，甚至可以在 Windows 的 GitBash 上工作。一如既往，您的里程可能会有所不同。*

# 和& &

大多数人都熟悉这个例子。我经常在构建脚本中看到它，如下面的命令所示。

```
npm test && npm run build 
```

Enter fullscreen mode Exit fullscreen mode

传统观点认为这个命令运行`npm test`，然后运行`npm run build`。但这并不准确。更准确的说是运行`npm test`，只有`npm run`成功(退出码为 0)才运行`npm run build`。如果第一个命令失败(退出代码！= 0)，那么第二个命令根本不会运行。在大多数情况下，这正是你想要发生的。在我的例子中，如果我的测试失败了，就没有理由运行构建。如果您只是简单地认为这两个命令将按顺序运行，这种行为可能会让您大吃一惊。

为了证明这一点，请在您最喜欢的 shell 中尝试以下方法。

```
ls this-folder-does-not-exist-anywhere && echo "I will not execute" 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到一条错误消息，并且回显的字符串不会出现。

你可以随意把这些串起来。请注意，第一个失败的命令会中断其余的命令。

# 或||

如果想让命令串继续呢？您可以尝试使用 Or ( `||`)运算符，但这可能也不会像您想的那样起作用。前一个操作符在第一次失败后终止，而这个操作符在第一次成功后终止。所以第二个命令只有在第一个命令失败时才会运行！

要查看这一过程，请尝试以下命令:

```
echo "I worked" || echo "Which means I will not execute" 
```

Enter fullscreen mode Exit fullscreen mode

我经常看到这种结构在创建文件或文件夹之前被用作简单的测试机制。考虑这个例子。

```
[ -d ~/i-do-not-exist ] || mkdir ~/i-do-not-exist 
```

Enter fullscreen mode Exit fullscreen mode

如果您没有看到第一个命令，它是一个测试目录是否存在的函数。因此，该命令会说，“创建这个目录，但前提是它不存在。”这种方法的优点是不会向终端写入错误，如果使用`ls ~/i-do-not-exist`就会发生这种情况。

# 我的个人文件夹~

如果您不熟悉上一个示例中的波浪号`~`符号，这是一个简单的快捷方式，它代表您的主目录。要查看它的扩展，您可以使用`echo`命令。

```
echo ~
/Users/michael 
```

Enter fullscreen mode Exit fullscreen mode

# 背景&

回到开始这一切的 tweet，暗示使用单个`&`允许命令并行运行。正如我所说，这是一种影响，但这只是故事的一部分。通常，当您希望一个长时间运行的 shell 脚本在后台执行时，您会使用`&`操作符。当然，您也可以打开另一个终端会话，但是这将比运行一个命令使用更多的资源。

要了解这在实践中是如何工作的，考虑下面这个长时间运行的命令，它在我的整个主目录中搜索所有 PDF 文件，在我的主目录中一个名为`my-pdf-files.txt`的文件中输出每个文件的路径。

```
find ~ -name *.pdf > ~/my-pdf-files.txt 
```

Enter fullscreen mode Exit fullscreen mode

在我的 i7 MacBook Pro 上执行这个命令需要 2.5 分钟以上。只需在该命令的末尾添加`&`操作符，它就会作为后台作业运行。

```
find ~ -name *.pdf > ~/my-pdf-files.txt & 
```

Enter fullscreen mode Exit fullscreen mode

执行该命令会立即得到如下所示的命令响应:

```
[1] 35715 
```

Enter fullscreen mode Exit fullscreen mode

这告诉我，我的后台作业是 Job #1，它的进程 ID 是 35715。我可以用`jobs`命令检查它的状态。

```
jobs
[1]+  Running                 find ~ -name *.pdf > ~/my-pdf-files.txt & 
```

Enter fullscreen mode Exit fullscreen mode

我可以用`fg 1`命令将它带到前台，这将导致我的终端阻塞。一旦在前台，我可以通过输入`Ctrl+Z`来暂停作业，然后输入`bg`将它放回后台。

如果我真的想这么做，我可以用`kill 35715`(初始命令输出的进程 ID)完全终止进程。

那么这两个命令是如何工作的呢？如果这两个命令相互之间有任何关联，那么可能不会。但是，如果我想运行两个类似的查找命令，一个查找 pdf，一个查找 MP3 文件，该怎么办呢？在这种情况下，哪个先完成并不重要，并行运行它们有明显的优势。

```
find ~ -name *.pdf > ~/my-pdf-files.txt & find ~ -name *.mp3 > ~/my-mp3-files.txt &
[1] 36782
[2] 36783 
```

Enter fullscreen mode Exit fullscreen mode

执行该命令将创建两个后台作业，由提供的两个作业号和进程 id 表示。

当它们完成时，您将在终端中看到类似这样的内容。

```
[1]-  Exit 1                  find ~ -name *.pdf > ~/my-pdf-files.txt
[2]+  Exit 1                  find ~ -name *.mp3 > ~/my-mp3-files.txt 
```

Enter fullscreen mode Exit fullscreen mode

# 重定向输出>

如果您一直遵循这些示例，您可能会看到一些错误消息。尽管命令在后台运行，但两者的输出都显示在终端上。通过使用“大于号”或“右尖括号”符号(`>`)重定向其输出，这很容易解决。事实上，我在前两个例子中使用过。

使用单个`>`符号告诉 shell 将标准控制台输出(stdout)重定向到指定的文件。所以上面的`find`命令将其标准输出发送到文件中。如果该文件不存在，将会创建它。如果它确实存在，它将被替换。

如果您希望命令附加到文件中，而不是从头开始创建，您可以使用两个`>>`符号。

所有这些都不能防止错误显示在控制台上，因为那是一个不同的输出流(称为 stderr)。你有几个选择。您可以通过指定另一个重定向将错误发送到不同的文件中，如下所示。

```
find ~ -name *.pdf > ~/my-pdf-files.txt 2>~/errors.txt 
```

Enter fullscreen mode Exit fullscreen mode

`2>`明确表示您正在重定向`stderr`。正如你可能猜到的，`1>`表示`stdout`，但是默认重定向是`stdout`，所以`1`可以省略。

更复杂的是，您可以通过使用`&1`作为目标，将`stderr`重定向到与`stdout`相同的目标，就像这样...

```
find ~ -name *.pdf > ~/my-pdf-files.txt 2>&1` 
```

Enter fullscreen mode Exit fullscreen mode

`&1`，`&`的另一个例子，完全表示别的意思，是“T2 被送到哪里”的快捷方式

我不常遇到`stdout`和`stderr`出现在同一个文件中的情况，特别是在上面的例子中，我将某个类型的文件收集到这些文件的一个文件中。在这种情况下，我宁愿简单地扔掉这些错误。

在 MacOS 或 Linux shells 中，我可以选择将`stderr`重定向到`/dev/null`，一个简单忽略一切的特殊文件。在 Windows 上，我知道有一个叫做`nul`的特殊文件可以完成同样的事情。

所以我上面的完整命令会是这样的。

```
find ~ -name *.pdf > ~/my-pdf-files.txt 2>/dev/null 
```

Enter fullscreen mode Exit fullscreen mode

# 重定向输入<

我发现这种用法很少使用，因为大多数命令都接受输入文件名作为参数。过去，许多(如果不是大多数的话)命令在标准输入(`stdin`)上运行，默认情况下是键盘，并将其输出发送到`stdout`。

想象一下，我想知道上面那些 PDF 文件找到了多少。我可以在我最喜欢的文本编辑器中打开文件并检查，但是有一种更简单的方法。我可以在终端输入这个命令。

```
wc -l < ~/my-pdf-files.txt`
    3308 
```

Enter fullscreen mode Exit fullscreen mode

是的，我不知道我有这么多。`wc`是“字数统计”命令，`-l`开关告诉它我只关心行数。默认情况下，`wc`从`stdin`获取输入，你可以自己输入命令，输入任何你想要的文本，通过输入`Ctrl+D`结束输入。

```
wc -l
Mike
was 
here
Ctrl+D
       3 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`Ctrl+D`没有被算作一行。它还需要单独在一行中指定。如果你在行尾输入它，它将成为那一行的一部分。

该策略适用于接受来自`stdin`的输入的任何命令，这是终端中可用的大多数命令。

我最常看到的一个命令是`more`，用来对一个文件的输出进行分页。

```
more < ~/my-pdf-files.txt 
```

Enter fullscreen mode Exit fullscreen mode

同样，我看不到这种用法了；大多数这些命令接受要操作的文件作为参数。这个版本的命令更常见。

```
more ~/my-pdf-files.txt 
```

Enter fullscreen mode Exit fullscreen mode

# I/O 管|

单个`|`操作符呢？这是一个输入输出管道。它使用第一个命令的输出作为第二个命令的输入。你可以把这看作是`>`和`<`的组合。

如果我想要这些 PDF 文件的排序列表呢？我可以在事后使用`sort`命令。然而，我也可以使用`|`管道作为`find`命令的一部分，忽略错误，对输出进行排序，最后将信息存入一个新文件，作为后台作业执行整个过程。

```
find ~ -name *.pdf  2>/dev/null | sort > ~/my-pdf-files.txt & 
```

Enter fullscreen mode Exit fullscreen mode

# 展开美元

每个系统都有环境变量。这些是特定于运行系统的设置。在我的苹果电脑上，我有 100 多个。如果您需要在 shell 脚本或另一个命令中使用它们，了解它们会很方便，即使它们有一个等效的命令。

比如我上面提到的`~`。您也可以使用`$HOME`环境变量来引用它。在我的系统上，我也有像`$HOSTNAME`、`$USER`这样的东西。

这些命令的输出经常在其他命令中使用。

```
echo My home directory is ~
echo My home directory is $HOME 
```

Enter fullscreen mode Exit fullscreen mode

这种方法最常见的用途是查看在系统的哪些文件夹中搜索可执行文件。这允许你输入`java -version`而不是它的绝对路径，绝对路径会更长也更不方便。

```
echo $PATH 
```

Enter fullscreen mode Exit fullscreen mode

## “内扩”

上面的`echo`命令比较特殊。它可以处理多个参数。大多数命令更喜欢将这样的字符串用引号括起来，将其视为单个参数。在这种情况下，知道单引号(`'`)和双引号(`"`)之间的区别是很重要的

当您使用双引号时，您的环境变量将被扩展。

```
echo "Hi, $USER! Have you cleaned up your $HOME folder today?"
Hi, michael! Have you cleaned up your /Users/michael folder today? 
```

Enter fullscreen mode Exit fullscreen mode

在该命令中，`$USER`和`$HOME`变量都被展开。整个字符串作为单个参数传递给`echo`命令。

## ‘内无膨胀’

如果不希望扩展环境变量，可以使用单引号(`'`)。

```
echo 'Hi, $USER! Have you cleaned up your $HOME folder today?'
Hi, $USER! Have you cleaned up your $HOME folder today? 
```

Enter fullscreen mode Exit fullscreen mode

# 使用命令输出``命令

如果您想执行一个命令并将其输出作为另一个命令的一部分，该怎么办？为此，您可以用反勾号(``)将命令括起来。这不同于输出管道，因为它不一定将一个命令的输出重定向为另一个命令的输入。考虑这个过于简单的例子。

```
echo "You have `wc -l < ~/my-pdf-files.txt` PDF files."
You have     3308 PDF files. 
```

Enter fullscreen mode Exit fullscreen mode

执行`wc`命令，其输出被放入字符串中，然后整个字符串被传递给`echo`命令。

我经常使用这种模式的另一个地方是试图找到可执行文件的实际位置。在我的 Mac 上，大多数可执行文件都象征性地链接到了`/usr/bin`文件夹。所以这个命令没有提供我需要的信息。

```
which java
java is /usr/bin/java 
```

Enter fullscreen mode Exit fullscreen mode

为了知道它的真实位置，我将对`which java`命令的输出使用`ls -l`命令，就像这样。

```
ls -l `which -p java` 
```

Enter fullscreen mode Exit fullscreen mode

`-p`开关将输出缩短为路径，没有消息“java is”。在我的系统上，这扩展到我真正想要运行的命令。

```
ls -l /usr/bin/java
lrwxr-xr-x  1 root  wheel    74B Sep 11  2018 /usr/bin/java -> /System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/java 
```

Enter fullscreen mode Exit fullscreen mode

# 历史！

最后一个我要提到的符号是感叹号，或者叫 bang ( `!`)。此符号允许您执行命令历史中的任何命令。要查看这些命令的列表，请输入以下命令。

```
history 657  which -p java
  658  ls -l `which -p java`
  659  history 
```

Enter fullscreen mode Exit fullscreen mode

您将看到一个可能很长的命令列表，最新的命令在列表的最后。要执行它们中的任何一个，只需在命令旁边输入命令号。

```
!657
which -p java
/usr/bin/java 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`grep`实用程序来搜索您的历史记录。例如，这里是我在创建这篇文章时运行的所有 find 命令，通过`uniq`实用程序只获得唯一的命令。

```
history | grep find | uniq 585  find ~ -name *.pdf > ~/my-pdf-files.txt
  586  find ~ -name *.pdf > ~/my-pdf-files.txt &
  589  find ~ -name *.pdf > ~/my-pdf-files.txt & find ~ -name *.mp3 > ~/my-mp3-files.txt &
  605  find ~ -name *.pdf  2>/dev/null | sort > ~/my-pdf-files.txt 
  606  find ~/Downloads/ -name *.pdf  2>/dev/null | sort > ~/my-pdf-files.txt 
  608  find ~/Downloads/ -name *.pdf  2>/dev/null | sort > ~/my-pdf-files.txt &
  663  history | grep find | uniq 
```

Enter fullscreen mode Exit fullscreen mode

# Ctrl+R 搜索历史

另外，在一些 shells 中，您还可以使用键盘快捷键`Ctrl+R`来交互式搜索您的历史记录。要查看这一过程，请键入`Ctrl+R`，后跟命令文本，例如`find`。将显示最近的匹配。继续键入`Ctrl+R`在历史中后退。在任何时候，你都可以通过按`Space`，或者左右箭头键来停止。然后，您可以自由编辑该命令，按 Enter 键执行它。按`Ctrl+C`不做任何操作，退出历史记录。

# 总结

尽管它们是专门针对基于*nix 的操作系统的，但其中许多都可以在 GitBash 或类似的 Windows 上运行。

我只触及了许多终端外壳中可用的特殊符号的表面。

我错过了你的最爱吗？你有什么特别的建议吗？我在这篇文章中犯了什么错误吗？在推特上告诉我。我是 [@walkingriver](https://twitter.com/walkingriver) 。

*交叉贴自[走江湖博客](https://walkingriver.com/what-the-symbols)T3】*