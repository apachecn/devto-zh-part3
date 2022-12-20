# 4 个简单的部分

> 原文：<https://dev.to/oshkoshbgoshjos/4-easy-pieces-3bk2>

这里有一些小窍门，可以改善你使用熊猫和 Jupyter 笔记本的体验。Jupyter 笔记本是不可思议的工具；Donald Knuth 花了几年时间试图让有文化的编程成为一件事，但没有成功，但这可能最终是它起飞的形式。

(顺便说一句，你可能会认为这篇文章是以《费曼傻瓜讲座》一书[六个简单的片段](https://www.goodreads.com/book/show/5553.Six_Easy_Pieces)命名的，但事实上那本书可能参考了 1970 年的电影*五个简单的片段*，这篇文章在标题上是这部电影的前传，但实际上不是。)

#### 0

在通常的重要陈述之后，我通常会用
开始我的笔记本

```
pd.options.display.max_rows = 999
pd.options.display.max_columns = 99 
```

在使用它的时候，能够看到整个 df 或者它的一大块通常是很方便的。还有，如果你的笔记本因为总是用同一个 Untitled7.ipynb(我们当中谁没有过等等)而变得一发不可收拾。)，考虑使用 nbextensions 中的 [Codefolding 扩展，这样你就可以隐藏所有你发誓有一天可能需要的旧代码。](https://jupyter-contrib-nbextensions.readthedocs.io/en/latest/nbextensions/codefolding/readme.html)

#### 1

如果你在 Jupyter 笔记本上做了一个计算，但忘记了分配，Jupyter 会帮你的。在下一个单元格中，您可以将其指定为`_`。

```
[1] long_calculation.solve() 
# Aaaah, no!
[2] result = _
# Phew 
```

#### 2

本·林赛[说](https://twitter.com/ben_j_lindsay/status/1108427124518645762)

> [# **Python**](https://twitter.com/hashtag/Python?src=hash) 中最被低估的 [# **熊猫**](https://twitter.com/hashtag/Pandas?src=hash) 函数之一就是`.query()`。我一直在用它。
> data = data . query(' age = = 42 ')
> 看起来比:
> data = data[data[' age ']= = 42]
> 好看多了，而且它允许类似于:
> data = data . query(' age>18 ')的链接。查询('年龄< 32 ')

我倾向于同意。我喜欢简洁，尽管很难克服它看起来是多么的不和谐(即使是以熊猫已经很低的标准)。

#### 2.5

最后:你不能在熊猫`(18 < df.age < 60 )`中做链式比较，但是[栈溢出 rando]((https://stackoverflow.com/a/18091240)) 指出你可以喜欢这个

```
df['18 <= age <= 60'] 
```

或者用前面提到的`query`

```
df.query('18 <= age <= 60') 
```

这一切都要经过某种类似于`eval`的词法解析，这在 Python 中通常不是一个好主意。我知道，我也不喜欢。

快乐的数据科学。