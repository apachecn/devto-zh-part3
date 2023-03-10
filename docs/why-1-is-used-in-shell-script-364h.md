# shell 脚本中使用${1+"$@"}的原因

> 原文：<https://dev.to/greymd/why-1-is-used-in-shell-script-364h>

有时，当传递参数给函数或外部命令时，我使用`${1+"$@"}`而不是`"$@"`。
下面是例子。

```
#!/bin/bash

main () {
  # Something...
}

main ${1+"$@"} 
```

`${1+"$@"}`的行为与`"$@"`几乎相同(如果你不了解`"$@"`，请看[这篇有帮助的文章](https://wiki.bash-hackers.org/scripting/posparams#all_positional_parameters))。

但是全世界有很多 shell 脚本都是包含`${1+"$@"}`而不是`"$@"`的。

哎呀，你没见过他们吗？

好，让我们在您的机器上执行这个命令。

```
$ grep -a -F '${1+"$@"}' /bin/* /usr/bin/* 
```

无论是在 Linux 还是 BSD 上，您都会得到大量结果。

```
$ grep -a -F '${1+"$@"}' /bin/* /usr/bin/*
/bin/c2ph:    eval 'exec /usr/bin/perl -S $0 ${1+"$@"}'
/bin/c89:exec gcc $fl ${1+"$@"}
/bin/c99:exec gcc $fl ${1+"$@"}
/bin/catchsegv:"$prog" ${1+"$@"} 2>&3 3>&-)
/bin/find2perl:    eval 'exec /usr/bin/perl -S $0 ${1+"$@"}'
...
...
/usr/bin/xzgrep:eval "set -- $operands "'${1+"$@"}'
/usr/bin/zgrep:    eval "set -- $arg2 "'${1+"$@"}'
/usr/bin/zgrep:eval "set -- $operands "'${1+"$@"}' 
```

我知道一些 OSS 项目使用这个表达。
另外，我也在自己的项目中使用。

*   [tmux-xpanes](https://github.com/greymd/tmux-xpanes)
*   [超级 _ 未知](https://github.com/unkontributors/super_unko/blob/master/bin/unko.tower#L141)

让我记下**我的**为什么我宁愿使用`${1+"$@"}`而不是`"$@"`的原因。我有点担心我的目的是否合理。
因为与代码语法表达相关的历史背景很难在网上找到(因为很难搜索)。
如果你使用`${1+"$@"}`并有任何其他意图，请评论并告诉我！

# 我先下结论

## 为什么要用？

*   为了提高便携性
*   `"$@"`在特定环境下无法正常工作。
    *   伯恩 shell 的早期版本
    *   Bash 的特定版本

## 是不是用起来比较好？

取决于你的情况。

*   如果可以确保`"$@"`扩展 1 个或多个位置参数，就不需要使用`${1+"$@"}`。
*   如果`"$@"`的位置参数个数能为 0 就更好用了。
*   但是我认为，你不应该花大价钱去使用它。

# `${1+"$@"}`做什么？

这个表达式来自 Bourne shell 提供的变量扩展`${parameter+word}`。

[Bourne Shell 手册，第 7 版](https://www.in-ulm.de/~mascheck/bourne/v7/)

> ${parameter+word}
> 如果设置了参数，则替换 word；否则
> 什么都不替代。

`1`位于`parameter`的位置，`"$@"`位于`word`的位置。

因此，`${1+"$@"}`意为...
**如果定义了`$1`，则求值`"$@"`** 。

这个表达式不仅可以在 Bourne shell 中使用，还可以在 Bourne Again Shell (Bash)中使用。Bash 的手册上似乎没有记录这一点。但是你仔细看的话是可以找到的。

Bash 有`${var:+word}`变量扩展，它的冒号可以省略。

[Bash 参考手册](https://www.gnu.org/software/bash/manual/bashref.html#Shell-Parameter-Expansion)

> 如果省略冒号，运算符只测试是否存在。

正常情况下，`${var:+word}`检查`var`是否为空。
但是如果省略了冒号，它只是检查`var`是否被定义。
因此，它的行为与 Bourne shell 的`${parameter+word}`完全相同

## `"$@"`怎么了？

下面是简单的 shell 脚本。它只是打印出`echo`的参数。
如果使用了任何未定义的变量，脚本会因为`set -u`被声明而不成功退出。
但是好像没有你可能知道的未定义变量。

#### `myecho.sh`

```
set -u
echo "$@" 
```

#### 痛击 3.2

先在 Bash 老版本上试试。

```
~ $ bash --version
GNU bash, version 3.2.57(1)-release (x86_64-apple-darwin18)
Copyright (C) 2007 Free Software Foundation, Inc. 
```

它像预期的那样工作。

```
$ /bin/sh myecho.sh A B C
A B C

$ /bin/sh myecho.sh
### empty result 
```

#### V7 上的伯恩外壳

接下来，让我们测试伯恩的外壳。
但是什么是《谍影重重外壳》？(老实说我不知道)
让我们查一下维基百科:p

[伯恩·谢尔-维基百科](https://en.wikipedia.org/wiki/Bourne_shell)

> Bourne shell 是版本 7 Unix 的默认 shell。

好了，我们用 7 版 Unix(本文称为 V7)。
V7 可以在 [SIMH](http://simh.trailing-edge.com/) 上模拟(我用的是 docker 容器 [alpine-simh](https://hub.docker.com/r/rattydave/alpine-simh) )。
当然，`vi`不会安装到 V7 上，因为`myecho.sh`必须由`ed`创建。

```
$ ed myecho.sh
a
set -u
echo "$\@
"
.
w
17
q

$ cat myecho.sh
set -u
echo "$@" 
```

并测试它。

```
$ /bin/sh myecho.sh A C D
A B C

$ /bin/sh myecho.sh
myecho.sh: @: parameter not set 
```

出错退出！

但是，这对你来说是个大问题吗？许多人可能认为 V7 上的所有人都不再使用 Bourne shell 了。
但见下。

#### Bash 4.0.0

我构建了 Bash 4.0.0。

```
$ /usr/bin/bash --version
GNU bash, version 4.0.0(1)-release (x86_64-unknown-linux-gnu)
Copyright (C) 2009 Free Software Foundation, Inc. 
```

并测试这个脚本。
结果是这样的。

```
$ /usr/bin/bash myecho.sh A B C
A B C

$ /usr/bin/bash myecho.sh
myecho.sh: line 2: $@: unbound variable 
```

再次出错退出！特定版本的 Bash 无法正常工作。

但是，用`${1+"$@"}`代替`"$@"`，可以避免上述错误。

## 本期历史

对了，谁开始用`${1+"$@"}`？
我不确定，但是在 1994 年出版的一本名为[的书中有相关讨论。
关于这本书，壳牌专家从 1991 年就开始使用这种表达方式。](https://web.mit.edu/~simsong/www/ugh.pdf)

> milt >“$ { 1+”$ @“}”是什么意思？我敢肯定这是给
> 米特>读入的其余命令行参数，但
> 米特>我不确定它到底是什么意思

它说原因是...

> 如果我们只使用“$@”，那么如果没有调用参数，它将替换为“”(空参数)，但我们不希望在这种情况下再现任何参数，而不是“”。

我来详细解释一下这部分。
一般来说(从语法上来说)，如果参数的数量为 0，`"$@"`应该不被处理。

```
my_command "$@"

### Same as ...
my_command 
```

然而，`"$@"`在特定的环境中被作为空字符串`""`处理，比如 V7 上的 Bourne shell。

```
my_command "$@"

### Same as this !
my_command "" 
```

我想，这是许多开发人员意想不到的行为。
另一方面，`${1+"$@"}`可以如预期的那样什么都没有。

```
my_command ${1+"$@"}

### same as below
my_command 
```

而且书上说这种方式兼容 V7。

> 我认为${1+"$@"}可以一直移植到“版本 7 Unix”

### 对 V7 的调查

我在 V7 上查过这个行为。首先，创建如下两个文件。

#### `main1.sh`

```
./sub.sh "$@" 
```

#### `sub.sh`

```
echo "# = $#"
echo "@ = $*" 
```

(我无法将它们统一到一个文件中，因为从 V7 开始就不支持 shell 的“函数”。)

`main1.sh`的结果是这样的。

```
$ /bin/sh main1.sh A B C
# = 3
@ = A B C

$ /bin/sh main1.sh
# = 1
@ = 
```

`$#`是 1。因为传递了空字符串`""`。

接下来，创建使用`${1+"$@"}`而不是`$@`的`main2.sh`。

#### `main2.sh`

```
./sub.sh ${1+"$@"} 
```

结果是...

```
$ /bin/sh main2.sh A B C
# = 3
@ = A B C

$ /bin/sh main2.sh
# = 0
@ = 
```

`$#`为 0。
因为什么都没通过。

## Bash 4 . 0 . 0 上的调查

就个人而言，保持与特定 Bash 版本的兼容性是使用这种表达方式的主要原因。

正如我上面提到的，Bash 4.0.0 中的`"$@"`不等于`${1+"$@"}`和 Bourne shell 一样。

(我在为我的个人项目运行自动化测试时注意到了这个 bug。)

奇怪的是，`"$@"`将什么都没有。
这个和伯恩 shell 不一样。

```
$ /usr/bin/bash main1.sh
# = 0
@ = 
```

```
$ /usr/bin/bash main2.sh
# = 0
@ = 
```

然而，由于 Bash 认为使用了未绑定变量，脚本将会失败，并显示`set -u`。

```
$ /usr/bin/bash -u main1.sh
main1.sh: line 1: $@: unbound variable

$ /usr/bin/bash -u main2.sh
# = 0
@ = 
```

这个 bug 也可以通过`${1+"$@"}`来避免。
在后来的版本中已经修复了。
但我知道 Bash 历史上有过很多 bug。
比如参数扩展到 bash 5.0.3 还是有 bug。

```
$ bash -c 'set -u; typeset -a a; echo "${#a[@]}"'
bash: a: unbound variable 
```

(这个 bug 已经被 [@satoh_fumiyasu](https://twitter.com/satoh_fumiyasu/status/883228541457899520) 举报了)

我仍然使用`${1+"$@"}`的原因主要是出于对 Bash 健壮性的考虑。
老实说，我对早期版本 Bourne shell 的兼容性不感兴趣(它甚至不支持 Shebang！).

# 结论

正如我提到的，如果位置参数的数量为 0，`${1+"$@"}`可能不等于`"$@"`。
因此，如果参数的数目应该是 1 或更多，就没有必要使用(你最好不要使用它，因为它会引起混乱)。

如果参数个数可以是 0，那么值得使用这个表达式。Bash 可以从语法上识别它。

然而，截至 2019 年，存在这一问题的环境相当罕见。

*   这种行为从 1986 年开始在 SVR3 shell 中得到修复(我猜)。

[伯恩·谢尔-维基百科](https://en.wikipedia.org/wiki/Bourne_shell)

> 1979 年后推出的功能
> ...
> 现代“$ @”–SVR 3 shell(1986 年)

*   据我所知，几乎所有的 Bash 版本都没有这个问题。

*   其实 Google 在 2013 年提供的 [Shell 编码风格指南推荐使用`main "$@"` (](https://google.github.io/styleguide/shell.xml) [链接](https://google.github.io/styleguide/shell.xml?showone=main#main))。

当然，使用`${1+"$@"}`没有技术上的问题。
另一方面，我认为采用这种表达方式并不值得重视和大成本。