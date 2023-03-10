# Python 字典视图

> 原文：<https://dev.to/barrachri/python-dictionary-views-1jji>

> ![unknown tweet media content](img/96dfd7f42fe0c1a25b15bfd888a93091.png)![Christian Barra profile image](img/9ce3262a12ff4a5c2fc5a4c9f3d00213.png)Christian Barra@ Christian Barra![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)你知道“dict.keys()”返回一个类集合对象的视图吗？[# Python](https://twitter.com/hashtag/Python)2018 年 7 月 12 日下午 12 点 12 分[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1024266442374557696)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1024266442374557696)2[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1024266442374557696)5

字典是 Python 最伟大的特性之一，使用`keys()`、`items()`和`values()`方法非常普遍。

```
first_dictionary = {"a": 1, "b": 2}
for key, value in first_dictionary.items():
    print(f"Key {key} with value {value}")

# Key a with value 1
# Key b with value 2 
```

但是你知道返回的是哪种对象吗？

它们都返回一个特殊的对象，叫做[视图](https://docs.python.org/3/library/stdtypes.html#dictionary-view-objects)。

为什么**视图**有用？

*   它们提供了下划线对象的动态视图(您更改了字典，视图也会随之更改)
*   由`keys()`和`items()`返回的对象表现得像一个类似集合的对象(当对[为可散列的](https://docs.python.org/3/glossary.html#term-hashable)时有`items()`

作为一个类似集合的对象意味着你可以使用[集合操作](https://docs.python.org/3.6/library/stdtypes.html#set-types-set-frozenset)。

让我们考虑一个例子，我们想要找到两个字典之间的公共键。

```
first_dictionary = {"a": 1, "b": 2}
second_dictionary = {"b": 2, "c": 3}

first_dictionary.keys() & second_dictionary.keys()
# {'b'} 
```

`&`是[交集操作符](https://docs.python.org/3.6/library/stdtypes.html#frozenset.intersection)，在这种情况下返回字典键之间的公共元素。

那些没有共同点的元素呢？

```
first_dictionary = {"a": 1, "b": 2}
second_dictionary = {"b": 2, "c": 3}

first_dictionary.keys() ^ second_dictionary.keys()
# {'a', 'c'} 
```

这叫做[公制差](https://docs.python.org/3.6/library/stdtypes.html#frozenset.symmetric_difference)。

在遍历视图对象时，不能改变字典。

```
for key, value in first_dictionary.items():
    del first_dictionary[key]

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
RuntimeError: dictionary changed size during iteration 
```