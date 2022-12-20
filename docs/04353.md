# 我对 Python 的爱恨情仇

> 原文：<https://dev.to/bailey/my-love-hate-relationship-with-python-3j3>

> 最初发布于 [bailey.guru](https://bailey.guru/my-love-hate-relationship-with-python/) 。

Python 是我 8 岁时学习的第一门语言，我很喜欢它。我可以制作一个很酷的基于游戏机的游戏，或者假装我在做黑客，谁不想这样做呢？我的 Github Gist 页面上有一些我在旧电脑上找到的早期作品。他们真的超级酷！我喜欢这样一个事实，我可以键入 5-10 行，但仍然有一些很酷的东西要展示——这很简单。它没有任何 C#或其他 C 语言的复杂结构。

但是现在我已经改变了，我已经学会了诸如 JavaScript 和 Go 之类的语言，我只是*不能*回到过去——这是**太**简单了。我发现在为我的 [GCSE 项目](https://github.com/baileyjm02/GCSE-Python-Programming-Project)编程时，我喜欢再次使用 Python**但是**讨厌缺乏结构...我的支架在哪里？

如果我能做下面这样的事情，那么我会爱上 Python。

```
def function(text) {
  ... do something 
} 
```

Enter fullscreen mode Exit fullscreen mode

# 括号

因此，很自然地，为了给我的 Python 项目增加一种自我安全感和结构感，我决定按照“如何在 Python 中使用花括号”的思路在谷歌上搜索，在那里我找到了一篇关于堆栈溢出的文章，标题是“我真的不能在 Python 中使用花括号吗？”。不管是谁写的，看起来都有点不安。尽管如此，我还是点击了它，开始阅读。有许多讽刺性的回应，如:

```
if foo: #{
    print "it's true"
#} else: #{
    print "it's false!"
#} 
```

Enter fullscreen mode Exit fullscreen mode

我确实觉得很有趣，但是有一个吸引了我的注意，我看到了一个导入函数👀。它说:
“您可以尝试使用未来的 import 语句来添加对大括号的支持，但它还不被支持，所以您会得到一个语法错误。”并在前面显示了这个片段:

```
>>> from __future__ import braces
  File "<stdin>", line 1
SyntaxError: not a chance 
```

Enter fullscreen mode Exit fullscreen mode

如果你仔细观察，你会发现语法错误“没戏”。原来这是 Python 开发者的一个小复活节彩蛋，意思是“不，我们永远不会添加它！”这让我有点难过，但我认为他们坚持简单的设计方案是好事。

# 进一步研究

因此，尽管我现在知道括号永远不会成为 Python 的一部分，但我还是决定看看是否有人为这类东西制作了编译器或预处理器。令我惊讶是，他们做到了！我找到了[by thon](https://github.com/mathialo/bython)——带括号的 Python。从这个项目来看，它看起来很容易配置，你安装并使用一个单独的命令而不是 Python 来运行。*和*它带有一个将 Python 转换成 Bython 的命令。很整洁吧！

虽然我可能再也不会使用 Python 了(至少在一段时间内)，但知道 Bython 是一个东西还是很好的。Mathias 很好地总结了这篇文章:因为 Python 很棒，但是空白很糟糕。