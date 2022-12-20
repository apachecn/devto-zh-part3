# 如何在 Python 中对两个列表的元素求和

> 原文：<https://dev.to/renegadecoder94/how-to-sum-elements-of-two-lists-in-python-17c7>

欢迎回到 Python 入门系列的另一个版本。这次我想用 Python 对两个列表的元素求和。我在工作中尝试这样做的时候得到了这个主题的灵感，所以让我们看看它是如何进行的！

## 问题介绍

最近，我遇到了一个问题，一个库并不完全按照我想要的方式工作，所以我不得不将结果拼凑在一起，让我的生活稍微轻松一些。在这个场景中，我有一个连接库，用来列出所有可用的设备。然而，列表功能对于某些类型的连接来说是短路的，所以它实际上从来没有列出过所有的内容。结果，我被迫运行了两次该功能:一次用于 USB，另一次用于以太网。

这个 list 函数的结果返回了一个类似如下的列表:

```
# [size, [types], [interfaces], [serial numbers], [IP addresses]] [2, [7, 7], [1, 2], [2314567, 8374163], [0, 84302738]] 
```

Enter fullscreen mode Exit fullscreen mode

然而，我被困在单独检索列表:

```
[1, [7], [2], [8374163], [84302738]]  # Ethernet [1, [7], [1], [2314567], [0]]  # USB 
```

Enter fullscreen mode Exit fullscreen mode

很自然，我希望能够将这两个列表合并成我最初所期望的那样。然而，我并不完全确定我该如何去做。那么，让我们来看看一些可能的解决方案。

## 方案

当合并两个列表时，有几种可能的方法。这里只是几个。

### 手动合并两个列表

首先，我们可以尝试手工合并这两个列表。换句话说，我们可以尝试下面的方法:

```
ethernet_devices = [1, [7], [2], [8374163], [84302738]]
usb_devices = [1, [7], [1], [2314567], [0]]
all_devices = [
    ethernet_devices[0] + usb_devices[0],
    ethernet_devices[1] + usb_devices[1],
    ethernet_devices[2] + usb_devices[2],
    ethernet_devices[3] + usb_devices[3],
    ethernet_devices[4] + usb_devices[4]
] 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个解决方案并不完美，但是它完成了任务。然而，一定有更好的方法。毕竟，我们已经利用了这样一个事实，即可以使用与加法运算相同的操作符合并[列表。为什么不在循环中利用这个属性呢？](https://www.pythoncentral.io/how-to-merge-lists-in-python/)

### 用一个理解对两个列表的元素求和

就像上一课关于用 Python 反转字典一样，我们可以利用理解来极大地简化这个问题。我们来看看:

```
ethernet_devices = [1, [7], [2], [8374163], [84302738]]
usb_devices = [1, [7], [1], [2314567], [0]]
all_devices = [x + y for x, y in zip(ethernet_devices, usb_devices)] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们正在谈话！这是五行冗长的映射压缩成一个简单的列表理解。但是等等，会更好的:

```
all_devices = [sum(pair) for pair in zip(ethernet_devices, usb_devices)] 
```

Enter fullscreen mode Exit fullscreen mode

或者，是吗？多亏了我们的 fried，[押韵](https://dev.to/rhymes/comment/94i5)，我们会注意到这个解决方案**实际上并不适用于我们的情况**。虽然它在对 iterable 中的整数求和方面做得很好，但是当试图合并两个子列表时，它崩溃了，并出现以下错误:

```
Traceback (most recent call last):
  File "<pyshell#3>", line 1, in <module>
    all_devices = [sum(pair) for pair in zip(ethernet_devices, usb_devices)]
  File "<pyshell#3>", line 1, in <listcomp>
    all_devices = [sum(pair) for pair in zip(ethernet_devices, usb_devices)]
TypeError: unsupported operand type(s) for +: 'int' and 'list' 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，您可能会发现它对于合并两个严格整数列表非常有用。

### 用映射对两个列表的元素求和

至此，我们基本上回答了我们的问题。然而，还有另一个解决方案，它需要的代码可能更少。来看看:

```
ethernet_devices = [1, [7], [2], [8374163], [84302738]]
usb_devices = [1, [7], [1], [2314567], [0]]
import operator
all_devices = list(map(operator.add, ethernet_devices, usb_devices)) 
```

Enter fullscreen mode Exit fullscreen mode

不完全确定这里的性能影响，但它肯定是一个漂亮的解决方案。当然，我们还需要添加一个依赖项，这会使这个解决方案不那么吸引人。事实上，如果我们要走这条路，我们可以很容易地利用 numpy:

```
ethernet_devices = [1, [7], [2], [8374163], [84302738]]
usb_devices = [1, [7], [1], [2314567], [0]]
import numpy as np
all_devices = np.add(ethernet_devices, usb_devices) 
```

Enter fullscreen mode Exit fullscreen mode

然而，numpy 是一个巨大的库，所以我们可能应该坚持我们的列表理解。

## 稍微重述一下

使用上面的一些方法，我们应该能够对两个列表的元素求和。

```
ethernet_devices = [1, [7], [2], [8374163], [84302738]]
usb_devices = [1, [7], [1], [2314567], [0]]

# The long way all_devices = [
    ethernet_devices[0] + usb_devices[0],
    ethernet_devices[1] + usb_devices[1],
    ethernet_devices[2] + usb_devices[2],
    ethernet_devices[3] + usb_devices[3],
    ethernet_devices[4] + usb_devices[4]
]

# Some comprehension magic all_devices = [x + y for x, y in zip(ethernet_devices, usb_devices)]

# Let's use maps import operator 
all_devices = list(map(operator.add, ethernet_devices, usb_devices))

# We can't forget our favorite computation library import numpy as np 
all_devices = np.add(ethernet_devices, usb_devices) 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，有很多方法可以对两个列表进行元素求和。你挑吧。

一如既往，谢谢你的来访！如果你有兴趣学习更多关于 Python 的知识，可以考虑订阅 Renegade Coder ，这样你就不会错过另一篇文章了。