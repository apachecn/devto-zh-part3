# 多个装载目录

> 原文：<https://dev.to/krzysztofzuraw/multiple-stow-directories-1fmi>

# 再次嗨👋

今天我想写一写我最近遇到的问题，我在谷歌上搜索了几个小时后解决了这个问题。它可能会帮助其他人，所以我写了一个关于它的帖子。

## 问题

为了管理我的[点文件](https://github.com/krzysztofzuraw/dotfiles)，我使用了
[stow](https://www.gnu.org/software/stow/) 。它对我的用例非常有用——将文件夹从我的点文件符号链接到实际需要它们的地方。然而最近我决定把我的网络文件分成两部分:公开的和私人的。

问题是在分割之后，我不能轻易地从我的私有点文件中使用`stow fish`,因为
我得到一个错误，这个目录已经被 stow 使用了。

## 收起折叠问题

原来 stow 在做一个叫`folding`的东西。这意味着它是
符号链接整个文件夹，而不是单个文件。所以`stow fish`将创建
文件夹符号链接，这就是为什么我不能符号链接其他文件到相同的位置。

## 解

然而 stow 有一个有趣的标志`--no-folding`，它在装载文件夹时调用:
`stow fish --no-folding`。因此，stow 不会符号链接文件夹，而是链接文件夹中的文件
。多亏了这一点，我可以有两个相同的鱼文件夹
的点文件，但文件不同。

## 总结&TL；速度三角形定位法(dead reckoning)

如果你想从不同的 stow 文件夹符号链接相同的两个目录，你可以
使用`--no-folding`标志。