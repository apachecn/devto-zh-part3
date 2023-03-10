# 在 Linux 中创建符号链接

> 原文：<https://dev.to/devdiaries/creating-symlinks-in-linux-576j>

Linux 中的符号链接是一种特殊的文件，它指向一个实际的文件或目录，基本上是一个快捷方式，但有点不同，因为符号链接是一个指向源文件或目录的实际指针。符号链接可能非常有用，例如，如果您想要对文件进行版本控制，您可以删除版本 app_v2，但只有一个引用 app_latest 的符号链接，该链接仅引用最新版本。

```
ln -s /path/to/source /path/to/symlink 
```

一些有用的符号链接选项有:

```
-f   If the target file already exists, then unlink it and force the link

-n   Useful with the -f option, replace the symlink that might point to a directory 
```

需要记住的重要一点是，创建符号链接时必须使用完整路径，而不是相对路径。例如，如果您将此作为您的文件系统

```
> ls image.png 
```

我们想创建另一个名为 pics 的目录(文件夹),其中包含一个指向 image.png 的符号链接。

```
> mkdir pics

> ls image.png
pics 
```

如果我们试图仅使用相对路径进行符号链接，符号链接将被破坏:

```
> ln -s image.png ./pics/image.png 
```

相反，我们应该使用完整的路径，在我们的例子中，我们可以使用扩展到当前工作目录的`$PWD`值:

```
> ln -s $PWD/image.png $PWD/pics/image.png 
```

另一个选择是将目录更改为 pics 目录，并从那里创建符号链接:

```
> cd pics
> ln -s ../image.png image.png 
```

[开发人员日记上的原始帖子](https://www.dev-diaries.com/social-posts/sym-link/)
[开发人员日记上的 Instagram 帖子](https://www.instagram.com/p/Bw2eNhIBAiv/)