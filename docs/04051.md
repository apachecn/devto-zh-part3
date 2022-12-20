# 从 Linux 命令行创建一个文本文件

> 原文：<https://dev.to/burdettelamar/create-a-text-file-from-linux-command-line-2md2>

以下是我可以从 Linux 命令行创建文本文件的一些方法。

## 猫

没有指定源文件的命令`cat`从`stdin`中读取。所以我可以输入这个(以`Ctrl-D`结尾):

```
$ cat > t.txt
Foo
Bar
Baz 
```

## 回声

命令`echo`有选项`-e`，可以解释反斜杠转义。所以我可以打这个:

```
$ echo -e "Foo\nBar\nBaz\n" > t.txt 
```

## 触控

最后，如果我不需要文件中的内容，我可以输入:

```
touch t.txt 
```