# __str__ 与 __repr__

> 原文：<https://dev.to/switowski/str-vs-repr-gpj>

时不时地，当我休息一会儿后回到编写 Python 代码的时候，一个问题浮现在我的脑海中:

> 我应该在 __str__ 和 __repr__ 函数中放入什么消息？

当你搜索它们之间的区别时，你会发现`__str__`应该是**人类可读的**而`__repr__`应该是**无歧义的**(如[这个 StackOverflow 问题](https://stackoverflow.com/questions/1436703/difference-between-str-and-repr)中所解释的)。这是一个非常好的、详细的回答。但出于某种原因，它从未真正困扰过我。我不是最聪明的开发人员，有时为了记住一些东西，我需要一个非常简单的例子。我真正发现有用的东西直接写在了 *repr()* 函数的[文档中:](https://docs.python.org/3/library/functions.html#repr)

> 对于许多类型，此函数会尝试返回一个字符串，该字符串在传递给 eval()时会产生一个具有相同值的对象

一个很好的例子就是`datetime`模块:

```
>>> import datetime
>>> now = datetime.datetime.now()
>>> str(now)
'2019-01-21 19:26:40.820153'
>>> repr(now)
'datetime.datetime(2019, 1, 21, 19, 26, 40, 820153)' 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`repr`函数返回一个字符串，该字符串可用于创建一个具有与`now`相同属性的**(不是与`now`相同属性**的**，而是与**相同属性**)的对象。你可以用下面的代码来验证它:** 

```
>>> timestamp = datetime.datetime(2019, 1, 21, 19, 26, 40, 820153)
>>> now == timestamp
True
# But! >>> id(now) == id(timestamp)
False 
```

Enter fullscreen mode Exit fullscreen mode

那么如何在自己的课堂上使用它呢？例如，如果你正在编写一个类`Car`，它具有属性`color`和`brand`，并以如下方式初始化:

```
red_volvo = Car(brand='volvo', color='red') 
```

Enter fullscreen mode Exit fullscreen mode

那么这就是汽车的`__repr__`函数应该返回的内容:

```
>>> repr(red_volvo)
"Car(brand='volvo', color='red')" 
```

Enter fullscreen mode Exit fullscreen mode

并不总是有可能编写出能够重新创建给定对象的`__repr__`函数，但是简单地记住那些带有`datetime`和`Car`的例子帮助我记住了`__repr__`和`__str__`之间的区别。

致谢:我是在丹·巴德的《 [Python 窍门](https://www.google.com/search?q=Python+Tricks:+A+Buffet+of+Awesome+Python+Features)》一书中发现这个窍门的。如果您没有听说过它，那么它是关于 Python 中级知识的一个很好的来源。我和 Dan 没有任何关系，但是他的书是我很长时间以来最喜欢的 Python 技术读物之一。

更多学分:图片来自[像素](https://www.pexels.com/photo/accessory-bobbin-close-up-clothing-355148/)