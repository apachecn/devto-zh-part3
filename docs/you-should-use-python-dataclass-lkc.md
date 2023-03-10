# 您应该使用 Python @dataclass

> 原文：<https://dev.to/dbanty/you-should-use-python-dataclass-lkc>

# TL；速度三角形定位法(dead reckoning)

如果你使用的是 Python 3.7+，那么 [dataclasses](https://docs.python.org/3/library/dataclasses.html) 将会为你节省大量时间，让你的代码更加整洁，甚至可能让你的代码更加正确。

# 为什么不应该使用数据类

1.  🕸你被困在 python 以下的版本上了。很抱歉，3.7 增加了几个非常棒的功能，所以尽可能升级。
2.  🚫没有 2。如果您使用的是 Python 3.7+数据类，那么在某些时候几乎肯定会对您有所帮助。

# 什么是[数据类](https://docs.python.org/3/library/dataclasses.html)？

我很高兴你问了🤓！Python 3.7 增加了一个叫做`@dataclass`的整洁的小装饰器。与其用英语解释为什么它很棒，我不如直接展示给你看。

### 你写什么

```
from dataclasses import dataclass, field
from typing import List

@dataclass
class Pizza:
    # Each of these is called a field
    crust: str
    has_cheese: bool = True
    toppings: List[str] = field(default_factory=list) 
```

Enter fullscreen mode Exit fullscreen mode

### 你现在能做什么

```
from dataclasses import asdict, astuple, replace

# An __init__ is created which takes all fields as args or kwargs! thick_cheesy = Pizza(crust='thick')

# __repr__ is generated, exclude any fields you don’t want print(thick_cheesy)
# Prints "Pizza(crust='thick', has_cheese=True, toppings=[])" 
# Handy method to create a dict, only includes fields d = asdict(thick_cheesy)
assert d == {
    'crust': 'thick',
    'has_cheese': True,
    'toppings': [],
}

# Create a new object based on another object with_olives = replace(thick_cheesy, toppings=['olives'])

# Make a tuple from the fields t = astuple(with_olives)
assert t == ('thick', True, ['olives'])

# __eq__ is generated assert with_olives != thick_cheesy # Effectively compares as tuples 
```

Enter fullscreen mode Exit fullscreen mode

### 但是等等，还有！💸

通过将一些选项传递给`@dataclass`装饰器，您还可以:

1.  让你新形成的物体不可变(又名冻结的🥶)
2.  加载不显示为字段的附加属性(也不在 asdict 中！)
3.  快速添加比较器(等)。)

# 没卖出去？

如果数据类不能立即让你兴奋，为什么不呢？我很想看看你的评论。