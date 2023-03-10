# 如何使用终端让 git diff 变漂亮

> 原文：<https://dev.to/callas1900/how-to-use-terminal-to-make-git-diff-beautiful-115n>

嗨，终点站的人们！

git 中有 word diff 特性。让我们现在就激活它吧！

首先，我的 git 版本是这样的。

```
$ git version
git version 2.21.0 
```

创建一个不同的高亮二进制文件是很重要的。然后将其移动到可执行路径或创建一个符号链接。
然而，二进制的初始状态因每个 linuxs 而异。
据我所知有三种模式。

1.  二进制文件具有可执行权限
2.  存在具有不可执行权限的二进制文件
3.  有一个 make 文件而不是二进制文件

我写这篇文章只是为了第三个选择。如果你的初始状态是#1 或#2，请通过阅读下面的内容来确保它是有效的。我想你能做到。

```
cd /usr/share/doc/git/contrib/diff-highlight/
sudo make
sudo ln -s /usr/share/doc/git/contrib/diff-highlight/diff-highlight /usr/local/bin/diff-highlight
sudo chmod +x /usr/share/doc/git/contrib/diff-highlight/diff-highlight 
```

最后，编辑你的`~/.gitconfig`文件

```
[core]
    pager = diff-highlight | less -r 
```