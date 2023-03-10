# SSTNPL v0.3:模块和命令提示符。

> 原文：<https://dev.to/thomasthespacefox/sstnpl-v03-modules--command-prompts-5g4m>

# 概述:

你们中的一些人可能已经看过我过去关于 SSTNPL 的教程，但是考虑到从那时起已经增加了多少，我想我应该发布一些它的最新特性。

[这个帖子将引用这个 SSTNPL 源文件](https://github.com/SBTCVM/SBTCVM-Gen2-9/blob/master/demos/shelldemo/auto_main.stnp)

### 简单，却有无穷的帮助。

如果您向下滚动注释，您可能注意到的第一件事就是代码缩进。类似于 C 语言，它没有任何意义，但是有助于提高代码的可读性。

### 命令提示 ahoy！

链接的 SSTNPL 源代码实际上创建了基本的命令行 shell。现在，其中一部分涉及到新的模块系统，以及一个名为 comprompt 的模块。但是后者所做的只是接受用户输入并将其传递到 SSTNPL 表中(2D 数组，注意 SSTNPL 没有普通的 1D 数组)。

```
gsub cp.getentry 
```

实际的语法检查是通过从第 57 行开始的 tabstrc 操作[完成的。它所做的只是检查给定的字符串是否在给定的表中的给定位置。注意，表名以`cp.`开头。](https://github.com/SBTCVM/SBTCVM-Gen2-9/blob/master/demos/shelldemo/auto_main.stnp#L57)

```
tabstrc cp.buffer,@0,@0,help
    set cmdflg
    if cmdflg,@1 =validcom @1
    if cmdflg,@1 gsub help 
```

### 模块系统。

如果您滚动到文件的末尾，您将会看到一个类似这样的命令:

```
include comprompt as cp 
```