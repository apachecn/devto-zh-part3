# 可选参数必须使用关键字(Python3)

> 原文：<https://dev.to/luminousmen/-optional-arguments-must-use-keywords-python3--45nh>

想象一下，你正在为一个大的航运公司开发软件(为什么你会想象小的)。您的任务是创建一个根据货物重量计算船只费用的函数。轻松的微风:

```
WEIGHT_RATES = [
      ( 10, 10.55),
      (  5, 5.05),
      (  2, 3.35),
      (  0, 1.25)
    ]

def shipping_charge(weight):
    if weight < 0:
       raise ValueError("Can't calculate shipping charge of negative weights")

    for min_weight, rate in WEIGHT_RATES:
        if weight > min_weight:
            return weight * rate 
```

Enter fullscreen mode Exit fullscreen mode

很简单。但是有一天你的程序最终会在另一个国家运行，比如说美国。一个问题出现了:我们需要用磅而不是公斤来计费。没问题，给你:

```
def shipping_charge(weight, pnds):
    if pnds:
       weight /= 2.2

    if weight < 0:
       raise ValueError("Can't calculate shipping charge of negative weights")

    for min_weight, rate in WEIGHT_RATES:
        if weight > min_weight:
            return weight * rate 
```

Enter fullscreen mode Exit fullscreen mode

这变得越来越复杂，但还有一个要求-如果重量超过 1000 公斤，你需要提出一个特殊的方向:

```
def shipping_charge(weight, pnds, exceed):
    if pnds:
       weight /= 2.2
    if exceed and weight > 1000:
       raise Exception("Weight can't exceed 1000 kg")

    if weight < 0:
       raise ValueError("Can't calculate shipping charge of negative weights")

    for min_weight, rate in WEIGHT_RATES:
        if weight > min_weight:
            return weight * rate 
```

Enter fullscreen mode Exit fullscreen mode

你看到问题了吗？在这个愚蠢的例子中，您遇到了 3 个位置参数，其中最后两个具有相同的类型。最终用户，或者作为开发人员的您，很容易忘记哪一个需要放在第一位，并把它们搞得一团糟。由于同类型 Python 程序不会失败，你会得到一个逻辑错误:

```
shipping_charge(2000, True, False) 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
shipping_charge(2000, False, True) 
```

Enter fullscreen mode Exit fullscreen mode

你可以使用一个带有默认值的关键字 arguments，这是一个很好的做法:

```
def shipping_charge(weight, pnds=False, exceed=False):
    if pnds:
       weight /= 2.2
    if exceed and weight > 1000:
       raise Exception("Weight can't exceed 1000 kg")

    if weight < 0:
       raise ValueError("Can't calculate shipping charge of negative weights")

    for min_weight, rate in WEIGHT_RATES:
        if weight > min_weight:
            return weight * rate 
```

Enter fullscreen mode Exit fullscreen mode

但是问题并没有解决。为了解决这个问题，你需要在参数列表中添加一个星号:

```
def shipping_charge(weight, *, pnds=False, exceed=False):
    if pnds:
       weight /= 2.2
    if exceed and weight > 1000:
       raise Exception("Weight can't exceed 1000 kg")

    if weight < 0:
       raise ValueError("Can't calculate shipping charge of negative weights")

    for min_weight, rate in WEIGHT_RATES:
        if weight > min_weight:
            return weight * rate 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，下次你调用这个函数的时候，你会得到一个错误:

```
>>>shipping_charge(2000, True, False)
TypeError: shipping_charge() takes 1 positional argument but 3 were given 
```

Enter fullscreen mode Exit fullscreen mode

更多信息: [PEP-3102](https://www.python.org/dev/peps/pep-3102/)

* * *

**感谢您的阅读！**

有什么问题吗？请在下面留下您的评论，开始精彩的讨论！

查看我的博客或来打个招呼👋在[推特](https://twitter.com/luminousmen)或订阅[我的电报频道](https://t.me/iamluminousmen)。
做好你的计划！