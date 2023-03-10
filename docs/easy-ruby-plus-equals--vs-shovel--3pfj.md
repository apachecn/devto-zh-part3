# Ruby:加-等于(+=) vs .铲(<

> 原文：<https://dev.to/adamlombard/easy-ruby-plus-equals--vs-shovel--3pfj> T3】

当第一次在 Ruby 中使用字符串时，加号等于(`+=`)和铲号(`<<`)之间的区别是困惑和沮丧的常见来源。

如果我们说…

```
santa = 'Saint'
claus = santa
santa += ' Nick' 
```

Enter fullscreen mode Exit fullscreen mode

…然后`santa`将产生字符串`Saint Nick`，而`claus`将产生字符串`Saint`。

有道理，对吧？改变`santa`不影响`claus`。

现在让我们毁了圣诞节！

## 困惑的北极

如果我们说…

```
santa = 'Saint'
claus = santa
santa << ' Nick' 
```

Enter fullscreen mode Exit fullscreen mode

…那么`santa`将产生字符串`Saint Nick`，而`claus`将*也产生字符串* `Saint Nick`。

搞什么鬼，圣诞老人？！

## 解释

为了理解`+=`和`<<`如何影响字符串，我们需要三个简单的心理模型:

*   变量指向对象
*   赋值运算符(`=`)影响变量
*   附加操作符(`<<`)影响对象

在 Ruby 中，一个变量在第一次被赋予一个对象时被初始化:

```
santa = 'Saint' 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码说，“计算机，我们需要你为我们存储一个对象——字符串‘Saint’。我们以后可能需要用到这个对象，甚至是改变它！事情可能会变得疯狂！但是，不管它如何变化，我们需要确保我们总是在谈论同一个对象。所以，让我们用一个代码字来表示内存中存储“Saint”的地址。我们用码字`santa`吧。围棋队！”

所以，如果我们说…

```
santa = 'Saint'
santa = 'Saint Nick' 
```

Enter fullscreen mode Exit fullscreen mode

…我们不会通过添加`Nick`来改变`Saint`对象。我们将一个*不同的*对象存储在(很可能)内存中的一个*不同的*位置，并将`santa`变量重新指向另一个内存地址。

即使我们连接并重新分配，我们也不会改变对象。它仍然只是一个变量的赋值:

```
santa = 'Saint'
santa = santa + ' Nick' 
```

Enter fullscreen mode Exit fullscreen mode

即使我们使用一些奇特的句法糖(或者，在圣诞老人的例子中，句法糖)来连接和重新分配:

```
santa = 'Saint'
santa += ' Nick' 
```

Enter fullscreen mode Exit fullscreen mode

加减运算符(`+=`)不会改变对象。它通过连接和赋值来改变变量所指向的内容。

但是，如果我们想要相反的结果——让变量指向内存中的同一个位置，同时改变位于该位置的字符串，该怎么办呢？

(提示:追加操作符(`<<`)即将在关键时刻到来，拯救圣诞节！)

```
santa = 'Saint'
santa << ' Nick' 
```

Enter fullscreen mode Exit fullscreen mode

这里我们说“计算机，请创建一个字符串对象‘Saint’，把它存储在某个地址，我们稍后会用代码字`santa`来讨论那个地址。”然后，使用`<<`，我们说，“计算机，记得你在`santa`地址为我们保存的那个对象吗？请在那个宾语的后面加上“尼克”。谢谢电脑！”

对象改变，而变量不变。变量继续指向内存中的同一个地址。

让我们回到最初的、令人困惑的例子，如果我们说…

```
santa = 'Saint'
claus = santa
santa << ' Nick' 
```

Enter fullscreen mode Exit fullscreen mode

…我们将会有…

*   `santa`产出`Saint Nick`，以及
*   `claus` *也*屈服`Saint Nick`！

这是因为`<<`操作符影响对象，而`+=`影响变量。我们将`santa`指向一个字符串对象，然后将`claus`指向`santa`——即指向内存中的同一个地址。

## Ruby 新手须知

技术上来说`<<`和`+=`不是操作符。`<<`和`+`是方法(在 Ruby 中，`santa << ' Nick'`与`santa.<<(' Nick')`相同)，而`+=`是结合赋值和串联方法的快捷方式。为了让我的 Ruby 新手同事们能够在 Google 上找到这个问题的答案，我有意而不严谨地使用了术语“operator”。

* * *

*这个帖子最初出现在[媒体](https://medium.com/@AdamLombard/easy-ruby-plus-equals-vs-shovel-6f030875e366)上。*