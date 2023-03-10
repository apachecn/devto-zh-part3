# “grep -liz”是做什么的？

> 原文：<https://dev.to/grepliz/what-does-grep-liz-do-1ndc>

我喜欢 linux 命令。这是我离不开的基本命令之一。我太喜欢它了，所以我用“grepliz”作为我的许多社交媒体账户的别名。

一天，当我正在思考`grep`的奇迹时，我想知道`grep -liz`会做什么。

所以这里开始这个博客的想法。

# `-l`是为清单调出文件

`grep -l`或`grep --files-with-matches`将只显示匹配的文件名，而不是每一行或每一处。例如，假设我们有一个名为`test.txt`的文件，其内容如下:

```
Hello Grep!
Hello Grep! 
```

Enter fullscreen mode Exit fullscreen mode

如果您在目录中运行`grep "Hello" *`，结果将如下所示:

```
$ grep "Hello" *
test.txt:Hello Grep!
test.txt:Hello Grep! 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果运行`grep -l "Hello" *`，只会得到文件名:

```
$ grep -l "Hello" *
test.txt 
```

Enter fullscreen mode Exit fullscreen mode

# `-i`是对案件的无视

默认情况下`grep -i`或`grep --ignore-case`T5`grep`区分大小写，使用`-i`标志我们使其不区分大小写*。

例如，让我们在上面的`test.txt`文件中添加另一行，这样它现在包含:* 

```
Hello Grep!
Hello Grep!
hello grep! 
```

Enter fullscreen mode Exit fullscreen mode

运行`grep -i "Hello" *`将显示所有 3 行:

```
$ grep -i "Hello" *
test.txt:Hello Grep!
test.txt:Hello Grep!
test.txt:hello grep! 
```

Enter fullscreen mode Exit fullscreen mode

# `-Z`是为 zgrep

`grep -Z`或`zgrep`将搜索压缩文件。因此，让我们以`test.txt`文件为例，使用`gzip`对其进行压缩。

```
$ gzip test.txt 
```

Enter fullscreen mode Exit fullscreen mode

这将产生一个名为`test.txt.gz`的压缩文件。
现在，如果你尝试用 grep 搜索“Hello”，它将不会返回任何结果:

```
$ grep "Hello" test.txt.gz 
```

Enter fullscreen mode Exit fullscreen mode

使用`-Z`标志，grep 将像`zgrep`一样解压缩文件进行搜索:

```
$ grep -Z "Hello" test.txt.gz
Hello Grep!
Hello Grep! 
```

Enter fullscreen mode Exit fullscreen mode

根据手册,`-z`和`-Z`应该做同样的事情，但我的测试显示并非如此。似乎 grep 不能识别小写的 z 标志。

你可能想知道`-L`或`-I`是做什么的。我将把它留给读者(或者另一个博客)作为练习。*