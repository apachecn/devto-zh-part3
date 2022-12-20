# 长久以来，我一直出于错误的原因使用 cat 和 vim...

> 原文：<https://dev.to/mcrowder65/i-ve-been-using-cat-and-vim-for-the-wrong-reasons-for-so-long-40a5>

我的情况:我有一个文件，我需要复制它的全部内容。

在终端中，我键入:`cat file.txt`，然后复制终端输出。

简单的解决方案:

`pbcopy < file.txt`

现在内容保存在我的剪贴板上。

然后，如果我必须将这些内容粘贴到其他地方，我会使用 vim 创建一个文件并将这些内容粘贴到那里，如果我幸运的话，我就不必重新启动计算机，以免卡在 vim 中。

相反，简单的解决方案是:

`pbpaste > file-two.txt`

现在，我剪贴板上的所有内容都保存在 file-two.txt 中了！

您还发现了哪些有用的 bash 命令？请在评论中告诉我！