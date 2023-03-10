# 了解 Unix 文件权限

> 原文：<https://dev.to/ackers93/learning-about-unix-file-permissions-54h>

我最近被指派测试一个我的雇主写的小程序。简而言之，这是一段允许我在终端`t 'sample text'`中键入的代码，它会将它保存到一个导入文件中，我可以在我的文本编辑器中找到并操作该文件。这基本上是一种不用离开终端就能记笔记的方式，让我专注于手头的任何任务。

这是一个很好的开始，直到我删除了垃圾箱。我的 dropbox 中的文件，它目前存储着所有的信息。恢复文件后，只要我再次尝试使用该程序，终端就会显示以下内容:

`-bash: /Users/ackerman/Dropbox/Thoughts/bin/t: Permission denied`

以前从未遇到过这种情况，我开始在所有错误的地方寻找..我认为我的目录出了问题，然后可能是写它的 Ruby 代码出了问题，或者我不知何故弄坏了我的 dropbox..我所有的理论很快被证明是错误的，但不幸的是，我真的不知道如何找到解决方案。所以在尝试了一段时间后，我决定放下自尊去问我的雇主。谢天谢地，他非常愿意帮助我，所以我希望我可以把他的知识传递给其他可能遇到这种情况的人！

Unix 中的每个文件都有三个不同的权限选项:

**1。所有者权限:**这决定了所有者可以对文件执行的操作。

**2。组权限:**这决定了拥有该文件的组成员可以执行的操作。

**3。其他(世界)权限:**显示任何其他用户可以对文件执行的操作。

因此，了解我们需要更改什么的最好方法是查看我们正在处理的文件的权限设置。

一旦目录中有正确的文件。如果您需要修改的文件是隐藏的，运行`ls -la`命令，如果不是，就运行 or `ls -l`命令。这将输出类似如下的内容:

`drwxr-xr-x 4 ackerman staff 128 Feb 19 11:32 mydir
-rw-r--r-- 1 ackerman staff 157 Feb 19 15:22 myfile`

首先要注意的是，第一个字符要么是一个`d`要么是一个`-`，它表示该文件是`d`一个目录还是`-`一个文件。接下来的 3 个字符表示**所有者**权限，之后的 3 个字符表示**组**权限，最后 3 个字符表示**世界/其他的权限。**

阅读它们非常简单。

> **r:** 表示用户可以**读取**文件。
> 
> **w:** 表示用户可以**将**写入文件。
> 
> **x:** 表示用户是否可以**执行**该文件。

这一行中的其他信息与我在这里讨论的内容并不十分相关。但是如果你需要的话，有很多资源可以帮助你更好的理解。

# 但是我怎么改变权限呢？

为了改变权限，你需要使用`chmod`(改变模式)命令，然后选择你想改变访问权限的类别。

**a:** 所有用户
**u:** 业主用户
**g:** 业主组
**o:** 其他(既不是 U，也不是 G)

这与三个不同操作符中的一个或多个相结合。

**+** 将指定的权限添加到文件或目录中。
**——**解除权限。
**=** 设置权限。

然后是我们之前讨论过的权限操作。( **r，w 或 x** )。这里有一些这样的例子。

**chmod a+r *myfile*** 文件对所有
T5】chmod a-r*my file*文件取消所有人读取文件的能力
**chmod a-rwx*my file***取消对所有
**chmod g+rw*my file***文件的所有访问权限给予组读写权限

 **这对于更改 **file_ permissions 来说很好，但是 _ _ directories】呢？这些需要用一个编号系统来改变，用这种方式被称为绝对形式。每个数字都表明谁被允许做什么。**

> 400 由所有者读取
> 200 由所有者写入
> 100 由所有者执行
> 040 由群组读取
> 020 由群组写入
> 010 由群组执行
> 004 由他人读取
> 002 由他人写入
> 001 由他人执行

为了使用它们，只需将它们添加到您的`chmod`命令和文件/目录名之后。这不仅适用于目录，也可以简单地用于编辑单个文件。你也不必担心学习所有这些数字，因为你可以使用一个[权限计算器](http://permissions-calculator.org)

举个例子，下面是解决我的问题的方法。我想做的只是让它的所有者可以读取(400)、写入(200)和执行(100)文件，所以我运行了`chmod 700 ./t`。当我在这之后使用`ls -la`命令时。您可以看到该文件已被更改，允许我作为所有者对该文件执行所有我需要的操作。

`-rwx------@ 1 ackerman staff 63 Mar 29 09:46 t`

我希望这对希望对 Unix 权限有基本了解的人有所帮助。如果你有任何问题，请随时告诉我！**