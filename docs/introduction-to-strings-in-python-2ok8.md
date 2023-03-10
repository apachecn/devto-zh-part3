# Python 中的字符串介绍

> 原文：<https://dev.to/thefern/introduction-to-strings-in-python-2ok8>

## Python 中的字符串介绍

字符串几乎是任何编程语言的重要组成部分。字符串是字符的数组。值得注意的是，字符串在 python 中是不可变的，这意味着您不能将任何内容重新分配给已经创建的字符串。如果你是一个视觉学习者，请点击这个[链接](https://youtu.be/m3U22xrpBF4)获得简短的字符串解释。[字符串 Python Doc](https://docs.python.org/3/library/string.html)

就拿“你好，世界！”举个例子。

| Zero | one | Two | three | four | five | six | seven | eight | nine | Ten | Eleven |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| H | e | l | l | o | , | W | o | r | l | d | ！ |

在上面的例子中，每个字母都被分配了一个索引。这使得我们很容易访问任何我们想要的东西。在 IDLE 或 Spyder 上尝试下面的代码。

```
hello = "Hello, World!"

hello[0]
Out[3]: 'H'

hello[5]
Out[4]: ','

hello[7]
Out[5]: 'W' 
```

Enter fullscreen mode Exit fullscreen mode

## 难道是在？(无意双关)

在“In”操作符的帮助下，我们可以搜索字符串中的字符。

```
'W' in hello
Out[17]: True

'O' in hello
Out[18]: False 
```

Enter fullscreen mode Exit fullscreen mode

## 指数超出范围

目前为止还好吗？不要太疯狂，好吗？但是如果我们请求索引 14 会发生什么呢？

```
hello[14]
Traceback (most recent call last):

  File "<ipython-input-7-769cefb1e06b>", line 1, in <module>
    hello[14]

IndexError: string index out of range 
```

Enter fullscreen mode Exit fullscreen mode

我们得到一个很好的 IndexError，我们在要求一些不存在的东西。有几种方法可以说明这一点，但首先让我们顺便介绍一个新的内置函数。了解对象可用函数的一种快速方法是调用另一个有用的函数“dir”。知道字符串的长度当然可以帮助我们避免遍历索引。

[![dir(str)](img/05b5367982c5137022a4d9f81e8a29a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P3dyhoeq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4ucn7ujcg53b4k01cjok.jpeg)

```
 len(hello)
  Out[26]: 13 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在 hello 字符串上运行 len 函数，就会得到字符串的长度。注意长度不是从 0 开始，而是从 1 开始。因此，如果您请求 hello[13]，您将得到另一个很好的索引超出范围异常，但是如果您请求 hello[12]，您将得到“！”。让我们回到最初遇到 IndexError 的问题:字符串索引超出范围。处理错误和异常本身是另一个主题，但是我们将简要地展示如何用字符串索引来防止它。如果你想了解更多关于这个话题的信息，请点击此链接[错误处理文档](https://docs.python.org/3/tutorial/errors.html)
[异常处理文档](https://docs.python.org/3/library/exceptions.html)

用不同的索引号尝试下面的代码:

```
 hello = "Hello, World!"
    indexNumber = 11
    try:
        char = hello[indexNumber]
        print(char)
    except ValueError:
        print("Index out of range") 
```

Enter fullscreen mode Exit fullscreen mode

## 比较

偶尔你会想比较字符串是否相等

```
 hello == "Hello, World!"
    Out[39]: True

    hello == "Hello, world!"
    Out[40]: False 
```

Enter fullscreen mode Exit fullscreen mode

## 永恒性

还记得我说过字符串是不可变的吗？让我们看看下面的例子:

```
 hello[0] = 'g'
    Traceback (most recent call last):

      File "<ipython-input-41-2ece3e8e1e48>", line 1, in <module>
        hello[0] = 'g'

    TypeError: 'str' object does not support item assignment 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我真的希望你喜欢这个关于字符串的简洁的博客。我将会写一些关于其他有用的字符串方法的博客。任何错误或反馈请在下面评论。