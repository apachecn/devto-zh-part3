# 使用 bash 的输出重定向

> 原文：<https://dev.to/adzubla/output-redirection-with-bash-5c4c>

下面的命令在不同的 I/O 流中生成输出:错误消息应该发送到 *stderr 流*，而“正常”消息应该发送到 *stdout 流*。但是如果你在终端中运行它，两个流都会被写到屏幕上:

```
$ ls -d /tmp nothing
ls: cannot access 'nothing': No such file or directory
/tmp 
```

您可以将输出重定向到一个文件。注意，错误信息写在屏幕上，而不是在文件:

```
$ ls -d /tmp nothing > stdout.txt
ls: cannot access 'nothing': No such file or directory
$ cat stdout.txt
/tmp 
```

或者您可以只重定向错误消息:

```
$ ls -d /tmp nothing 2> stderr.txt
/tmp
$ cat stderr.txt
ls: cannot access 'nothing': No such file or directory 
```

此外，您可以同时将两个流重定向到*个不同的文件* :

```
$ ls -d /tmp nothing 2> stderr.txt > stdout.txt
$ cat stderr.txt 
ls: cannot access 'nothing': No such file or directory
$ cat stdout.txt 
/tmp 
```

或者你可以将所有内容重定向到*同一个文件* :

```
$ ls -d /tmp nothing &> all.txt
$ cat all.txt 
ls: cannot access 'nothing': No such file or directory
/tmp 
```

另一种可能是将 stderr 重定向到 stdout。当您将数据传递给另一个命令时，它会很有用。在下面的例子中，两行都被发送到 stdout，而 *wc* 将计算两行:

```
$ ls -d /tmp nothing 2>&1 | wc -l
2 
```

将 stdout 重定向到 stderr 也是可能的。在这种情况下，两行都被发送到终端屏幕上显示的 stderr，没有任何内容被发送到 stdout，因此 *wc* 将计数 0 行:

```
$ ls -d /tmp nothing 1>&2 | wc -l
0
ls: cannot access 'nothing': No such file or directory
/tmp 
```