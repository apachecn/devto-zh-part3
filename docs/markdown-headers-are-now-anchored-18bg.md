# 降价标题现在被锚定！

> 原文：<https://dev.to/devteam/markdown-headers-are-now-anchored-18bg>

你现在可以链接到你文章的标题了！这是在创建标题时自动发生的。它将创建一个基于标题文本的锚链接。比如你有一个类似“实施”的标题，可以访问`https://dev.to/andy/andys-post#implementation`直接跳转到帖子的“实施”部分。

这里有一个更直接的例子:
[点击跳转到页面底部](#bottom)

# 如何使用

```
[Jump to "How to use"](#how-to-use) 
```

Enter fullscreen mode Exit fullscreen mode

[跳转至“如何使用”标题](#how-to-use)

使用多字标题时，锚定链接会将空格转换为破折号。

# 如何链接到不同帖子的标题

你可能想链接到不同文章的特定部分。嗯，你也可以这么做:

```
[External post header jump example](https://dev.to/rhymes/changelog-export-posts-1kdm#how-do-i-do-that?) 
```

Enter fullscreen mode Exit fullscreen mode

[外部帖子标题跳转示例](https://dev.to/rhymes/changelog-export-posts-1kdm#how-do-i-do-that?)

如果你想让你以前的文章也有这个功能，你需要重新保存文章本身。

感谢 [@glebec](https://dev.to/glebec) 和 [@bennypowers](https://dev.to/bennypowers) 获得公关滚动！

# 底部

我的标题叫“底部”，所以访问`#bottom`会把你移到这一段内容。

[跳回到“如何使用”部分](#how-to-use)

点击此处查看公关。

# 快乐编码！