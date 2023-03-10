# 在 Bash 中删除以连字符开头的文件

> 原文：<https://dev.to/_morgan_adams_/deleting-files-that-start-with-a-hyphen-in-bash-2gpp>

## 舞台

我运行了一个脚本，它将文件路径作为参数，并写入该文件。首先，我用-h 标志运行它，看看是否能得到帮助消息。没有错误信息输出到标准输出。然而，脚本随后做了一些意想不到的事情…

按 enter 键后，脚本继续执行，并提示额外的输入。我没有注意到，按了几次“回车”得到一个清晰的提示。这无意中让脚本完成了。结果，我以一个全新的文件调用“-h”结束。哎呀。

## 删除文件很难…

没什么大不了的，对吧？

```
$> ls
-h 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用一个简单的“rm”命令
删除它

```
$> rm -h
rm: invalid option -- 'h'
Try 'rm ./-h' to remove the file '-h'.
Try 'rm --help' for more information. 
```

Enter fullscreen mode Exit fullscreen mode

什么…？

```
$> rm \-h
rm: invalid option -- 'h'
Try 'rm ./-h' to remove the file '-h'.
Try 'rm --help' for more information.
$>
$> rm "-h"
rm: invalid option -- 'h'
Try 'rm ./-h' to remove the file '-h'.
Try 'rm --help' for more information.
$>
$> rm '-h'
rm: invalid option -- 'h'
Try 'rm ./-h' to remove the file '-h'.
Try 'rm --help' for more information.
$>
$> rm '\-h'
rm: cannot remove '\-h': No such file or directory 
```

Enter fullscreen mode Exit fullscreen mode

此时，我困惑地挠了挠头。然而，通过谷歌搜索，我找到了一些简单的解决方法

```
$>rm ./-h
# or
$>rm -- -h 
```

Enter fullscreen mode Exit fullscreen mode

这两种解决方案都可行。第一种可行，因为我们使用当前目录作为路径的一部分。这告诉 bash 不要将连字符作为选项来处理。

最后，第二个选项可行，因为双连字符是 bash 内置的。它告诉命令没有更多的选项要处理，其他的都是位置参数。

## 总结

总之，我有几点经验教训:

1.  实际上，在按回车键之前看看你的提示
2.  贝壳有很多细微差别。当你遇到他们时，花时间去了解他们是值得的，因为你很可能会再次遇到他们。
3.  在脚本中使用“set -e”。如果有错误，脚本将退出而不是继续执行。