# 如何在 Python 中反转列表

> 原文：<https://dev.to/barrachri/how-to-reverse-a-list-in-python-1i97>

> ![unknown tweet media content](img/4aa69458a46866a2de8e301c408b54b6.png)![Christian Barra profile image](img/9ce3262a12ff4a5c2fc5a4c9f3d00213.png)Christian Barra@ Christian Barra![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)如何用方括号符号反转列表 [#Python](https://twitter.com/hashtag/Python) ？[# euro python](https://twitter.com/hashtag/europython)2018 年 7 月 25 日上午 11:00[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1022074115476389888)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1022074115476389888)8[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1022074115476389888)28

在 Python 中反转一个列表非常非常简单。

这不仅适用于`list`，也适用于[序列](https://docs.python.org/3/glossary.html#term-sequence)(例如字符串和元组)。

```
msg = "hello there"
print(msg[::-1])
# ereht olleh 
my_tuple = (1, 2, 3)
print(my_tuple[::-1])
# (3, 2, 1) 
```

这个魔法背后的秘密？[切片对象](https://docs.python.org/3/library/functions.html#slice)。

```
name = "christian"
print(name.__getitem__(slice(None, None, -1)))
# 'naitsirhc' 
```

虽然我很老，但我真的建议阅读一下 [Python 2.3 的新特性](https://docs.python.org/3/whatsnew/2.3.html?highlight=slice#extended-slices)，它引入了对内置类型的支持。