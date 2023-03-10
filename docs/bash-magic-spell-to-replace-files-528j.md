# Bash 替换文件的魔法

> 原文：<https://dev.to/maciekchmura/bash-magic-spell-to-replace-files-528j>

[![bash](img/a7c79425a325bfb5361127271f1e6913.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I18XJX03--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mahrtqnqhpmbssxs0hkr.jpg)

最近，在我的工作项目中，我给自己分配了一项任务，用新的图形替换项目中的 png 文件。
相同的文件名，不同的像素。

在 VSCode 中快速搜索该文件，会在不同的文件夹中显示大约 35 个结果。

> 但是为什么项目中有 35 个相同的 png 副本呢？好问题，我没有答案🤔

这里能做什么来完成这个任务？

1.  手动跟踪文件资源管理器中的所有文件并替换它们。这可能需要大约 15 到 25 分钟，取决于您是否仔细检查副本以及您点击的速度...boooooring！

2.  或者找一个 bash 命令来为您完成这项工作。

接受挑战！
应该怎么做？

*   引用包含新数据的文件

*   按名称查找文件

*   用新数据替换找到的文件

*   重复此操作，直到文件夹中的所有文件都被更改

经过一些谷歌搜索，阅读堆栈溢出和手册页，我缝合了这一行，用新数据替换一个文件:

```
find . -print -type f -name apple.PNG -execdir cp /mnt/c/Users/Maciej_Chmura/replace_source/orange.PNG {} \; 
```

让我们把它分解一下，以便确切地知道发生了什么:

```
find 👉 bash find command
. 👉 current execution context, current folder
-print 👉 log the process
-type f 👉 specify to look for files
-name file_name 👉 specify file name to search
-execdir 👉 execute command from the subdirectory
cp from_file to_file 👉 copy from one file to another
{} 👉 is a placeholder for file that `find` will find 
```

不要停在这里，多了解一下这些命令:

[找](https://ss64.com/bash/find.html)
T5】CP

有了这一行，当前文件夹和子文件夹中的所有文件都将被替换。

经过这个研究，我想到了另一个用例。
如果需要重命名文件怎么办？
命令几乎一样:

```
find . -print -type f -name apple.PNG -execdir mv {} orange.PNG \; 
```

希望这能为你在将来遇到类似的情况时节省一些时间。你知道什么魔法攻击咒语吗？