# Python 编程风格

> 原文：<https://dev.to/mahmoudai/python-programming-styles-4o4m>

[![](img/30cf482cb650b3c1cf6be51e7122ae5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V9Nehc_T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fgdzu111d54kzrgsg0b4.jpg)

在读一篇关于 PyTorch 新版本的文章时，我的目光停在了句子上

> 从本质上来说，PyTorch 有两个主要特征，使其区别于其他深度学习框架:
> 
> *   命令式编程
> *   动态计算绘图

并且希望探索更多关于命令式编程的知识，发现它非常漂亮和清晰，但是其他编程风格呢？并且可以用 python 来编码它们？从这里，我开始了我关于 **python 编程风格**的搜索之旅，然后在这里分享它。

最初，python 是一种伟大的语言，不仅仅是因为编码简单，而是因为它在编码风格的限制方面与其他语言不同，大多数其他语言只使用一种编码风格，这降低了程序员的灵活性，但在 Python 中，程序员可以使用许多编码风格来实现不同的效果。

以下是 Python 编码风格的概述:

**1。程序性**
任务一次进行一步，你可以想象程序的顺序流程，它主要用于排序、选择和模块化。[更多阅读](https://www.101computing.net/sequencing-selection-iteration/)

```
def get_total(my_list):
    total=0
    for x in my_list:
        total+=x
    return total

print(get_total([1,2,3,4,5])) 
```

Enter fullscreen mode Exit fullscreen mode

**2。函数式**
它就像一个数学等式，避免了任何形式的状态或可变数据，一些开发人员称之为过程风格的一部分，它支持以解决方案为中心的思维(不是*做什么*，而是*完成什么*)。因此，学者们把函数式语言作为第一编程语言来教授，而数据科学家们更喜欢递归和 lambda。

```
from functools import reduce

def sum_nums(a, b):
    return a + b

print(reduce(lambda a, b: sum_nums(a,b), [1, 2, 3, 4, 5])) 
```

Enter fullscreen mode Exit fullscreen mode

**3。命令式**
计算是作为直接改变程序状态来执行的，它产生简单的代码，在数据结构操作中非常有用。

```
total = 0
for x in my_list:
    total += x
print(total) 
```

Enter fullscreen mode Exit fullscreen mode

**4。面向对象**
它通过使用对象来模拟现实世界来简化代码，并赋予程序员重用代码的能力，封装允许将代码视为黑盒，而继承使扩展功能变得更容易。

```
class list_opertaions(object):
    def __init__(self, my_list):
        self.my_list = my_list
        self.total = 0

    def get_total(self):
      self.total = sum(self.my_list)

obj = list_opertaions(my_list)
obj.get_total()
print(obj.total) 
```

Enter fullscreen mode Exit fullscreen mode