# 使用终端的交互式提交

> 原文：<https://dev.to/cutiko/interactive-commit-using-terminal-60>

大家好！我在这里的第一个帖子是期待有足够的东西来分享:)

### icommit

我创建了一个简单的脚本，使用终端进行交互式提交，您可以在这里看到说明

[![icommit demo gif](img/d0c31efde6e1f4b78c3a2918d7c90c1d.png)](https://i.giphy.com/media/ORTSU6rH3XxonpcV5z/giphy.gif)

【icommit 指令

#### 问题

通常，提交会是这样的:

```
git commit -am 'ADD: new feature' 
```

那么下一次提交应该是这样的:

```
git commit -am 'FIX: the feature was broken' 
```

在每次提交中，总是重复相同的模式`git commit -am 'THE_MESSAGE'`,所以我想知道如何做得更快。

我的第一次尝试是按向上键删除，但是很乏味，然后我想到:

> 如果我能拥有类似`scanner.readline`的东西，那将会非常酷

就像在基于文本的冒险游戏中，交互基本上是写台词，程序读取输入，然后做一些事情。

所以你有一个奖励，现在你的版本控制也是一个冒险游戏！