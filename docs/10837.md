# 目录权限在 Linux 中是如何工作的

> 原文：<https://dev.to/ravernkoh/how-directory-permissions-work-in-linux-2kfe>

在 Linux 中，目录可以像文件一样被分配权限。然而，尽管每种权限的名称对文件都有意义，但对目录却没有意义。

以`x`权限为例。当在文件上设置时，它表示它们能够被用户执行。然而，这并不像目录可以被执行，所以启用权限到底是什么意思呢？

*对于那些想直接了解这些信息来源的人来说，我在写这篇文章时主要参考了栈交换上的[这篇文章。](https://unix.stackexchange.com/questions/21251/execute-vs-read-bit-how-do-directory-permissions-in-linux-work)*

## 阅读

`r`权限允许读取文件。但是，当在目录上设置了该权限时，基本上意味着允许用户列出目录中的文件。

以下面的目录为例。

```
root@macos demo/ # ls -l
total 0
dr--------  4 root  staff  128 Mar 10 16:29 foo 
```

在这个例子中，目录归`root`所有，只有`root`被允许列出目录的内容。

```
root@macos demo/ # ls -l foo
total 8
-rw-r--r--  1 root  staff  16 Mar 10 16:29 bar
-rw-r--r--  1 root  staff   0 Mar  7 14:58 baz 
```

其他用户**不会**能够列出内容。

```
ravernkoh@macos demo/ $ ls -l foo
ls: foo: Permission denied 
```

## 写

`w`权限更直观。设置后，它允许用户修改目录中的文件和目录。但是，它不允许用户在目录中创建和删除文件，除非也设置了`x`权限。

## 执行

`x`权限允许用户进入目录并访问目录中的文件。

举下面这个例子。

```
ravernkoh@macos demo/ $ ls -l
total 0
d--x------  4 ravernkoh  staff  128 Mar  7 14:58 foo 
```

由于没有设置`r`权限，用户将不能列出目录的内容。

```
ravernkoh@macos demo/ $ ls -l foo
ls: foo: Permission denied 
```

然而，用户仍然能够**访问**并打印该目录中文件的内容。

```
ravernkoh@macos demo/ $ cat foo/bar
Hello from bar! 
```

## 粘稠

`t`权限声明，对于目录中的每个文件和目录，只有所有者可以重命名或删除它们。这在多个用户共享同一个目录的情况下很有用，在这种情况下，设置`t`权限可以防止一个用户更改另一个用户的文件。

## 结论

总的来说，我认为考虑这个问题的最好方法是将目录视为一个包含名称列表的文件。如果设置了`r`权限，用户可以读取(并实际列出)姓名列表。如果设置了`w`权限，用户可以写入其中的内容，并且可能(取决于是否设置了`x`)创建和/或删除文件。如果设置了`x`权限，用户可以自己更改名称列表，这将创建和/或删除文件。用户也可以`cd`到那个目录，这类似于执行它。

如果我提到了任何不准确的或者你不同意的地方，欢迎回复这篇文章😀。谢谢！