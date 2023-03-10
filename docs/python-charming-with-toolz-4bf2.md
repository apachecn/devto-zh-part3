# Python 与 Toolz 的魅力

> 原文：<https://dev.to/derekenos/python-charming-with-toolz-4bf2>

Toolz 是一个非常有用的实用函数集合，它可以帮助你做你一直在做的事情，但是更明确，而且通常代码更少。

我将重点介绍几个我最常用的函数，但还有许多其他函数，它们都是纯金的。

# 从现有的`dict`中创建一个新的`dict`，并排除一些项目

给定原文`dict` :

```
original = {
    'A': 1,
    'B': 2,
    'C': 3,
} 
```

Enter fullscreen mode Exit fullscreen mode

## 按键过滤项目

要从`original`创建一个新的`dict`，同时排除带有`key='C'`的项目，没有`toolz`，我可能会做:

```
new = {
    k: v
    for k, v in original.items()
    if k != 'C'
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 [dicttoolz.keyfilter](https://toolz.readthedocs.io/en/latest/api.html#toolz.dicttoolz.keyfilter) ，这变成:

```
new = keyfilter(lambda k: k != 'C', original) 
```

Enter fullscreen mode Exit fullscreen mode

除了更简洁、更实用等，我真正喜欢的是这个`keyfilter`解决方案。，就是它的名字清楚的告诉你是怎么回事。

## 按值过滤项目

给定一个名为`is_even`的帮助函数，它返回一个`bool`来指示单个数字参数是否为偶数:

```
is_even = lambda x: x % 2 == 0 
```

Enter fullscreen mode Exit fullscreen mode

或者，作为不是`lambda` :

```
def is_even(x):
    return x % 2 == 0 
```

Enter fullscreen mode Exit fullscreen mode

要从`original`创建一个新的`dict`，同时只包含偶数值，不包含`toolz`，我可能会做:

```
new = {
    k: v
    for k, v in original.items()
    if is_even(v)
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 [dicttoolz.valfilter](https://toolz.readthedocs.io/en/latest/api.html#toolz.dicttoolz.valfilter) ，这变成:

```
new = valfilter(is_even, original) 
```

Enter fullscreen mode Exit fullscreen mode

# 对于单个键，从`dict`的`list`中的每个`dict`中获取值

给定代表用户的`dict`列表:

```
users = [
    { 'name': 'User A',
      'age': 30,
    }, 
    { 'name': 'User B',
      'age': 32,
    }, 
    { 'name': 'User C',
      'age': 41,
    }, 
    { 'name': 'User D',
      'age': 43,
    }, 
] 
```

Enter fullscreen mode Exit fullscreen mode

要得到年龄，没有`toolz`，我会做:

```
ages = [ user['age'] for user in users ] 
```

Enter fullscreen mode Exit fullscreen mode

使用[itertoolz . puck](https://toolz.readthedocs.io/en/latest/api.html#toolz.itertoolz.pluck)，这变成:

```
ages = pluck('age', users) 
```

Enter fullscreen mode Exit fullscreen mode

注意，这个^(像其他一些`toolz`函数一样)实际上返回了一个[生成器](https://wiki.python.org/moin/Generators)类型的`itertools.imap`对象，如果您打算迭代它，这是没问题的，但是如果您想像`ages[0]`那样索引它，您将需要把它转换成一个列表(或元组):

```
>>> ages[0]
TypeError: 'itertools.imap' object has no attribute '__getitem__'

>>> ages = list(pluck('age', users))
>>> ages[0]
30 
```

Enter fullscreen mode Exit fullscreen mode

您也可以通过将多个键指定为一个列表来一次`pluck`多个键:

```
age_name_pairs = pluck(['age', 'name'], users) 
```

Enter fullscreen mode Exit fullscreen mode

看起来像:

```
>>> list(age_name_pairs)
[(30, 'User A'), (32, 'User B'), (41, 'User C'), (43, 'User D')] 
```

Enter fullscreen mode Exit fullscreen mode

# a 组`list`s 组`dict`按某种标准

给定与之前相同的用户列表`dict`:

```
users = [
    { 'name': 'User A',
      'age': 30,
    }, 
    { 'name': 'User B',
      'age': 32,
    }, 
    { 'name': 'User C',
      'age': 41,
    }, 
    { 'name': 'User D',
      'age': 43,
    }, 
] 
```

Enter fullscreen mode Exit fullscreen mode

如果我想将用户分成十年分辨率的年龄组(即 30 岁、40 岁等)。)，如果没有`toolz`，也许我会做类似于:
的事情

```
age_decade_users_map = defaultdict(list)
for user in users:
    age_decade = int(user['age'] / 10) * 10
    age_decade_users_map[age_decade].append(user) 
```

Enter fullscreen mode Exit fullscreen mode

这产生:

```
>>> dict(age_decade_users_map)
{30: [{'age': 30, 'name': 'User A'}, {'age': 32, 'name': 'User B'}],
 40: [{'age': 41, 'name': 'User C'}, {'age': 43, 'name': 'User D'}]} 
```

Enter fullscreen mode Exit fullscreen mode

使用 [itertoolz.groupby](https://toolz.readthedocs.io/en/latest/api.html#toolz.itertoolz.groupby) ，这可以通过
来完成

```
age_decade_users_map = groupby(
    lambda user: int(user['age'] / 10) * 10,
    users
) 
```

Enter fullscreen mode Exit fullscreen mode

荣誉奖授予:

*   [itertoolz.first](https://toolz.readthedocs.io/en/latest/api.html#toolz.itertoolz.first) -从 iterable 中获取第一个元素(偶数`set` s！)

*   [ITER toolz . partitional _ all](https://toolz.readthedocs.io/en/latest/api.html#toolz.itertoolz.partition_all)——将一个 iterable 拆分成指定最大长度的元组

*   获得一个添加了指定项目的字典副本

*   [dict toolz . dissoc](https://toolz.readthedocs.io/en/latest/api.html#toolz.dicttoolz.dissoc)——获得一个删除了指定键的 dict 的副本(功能上类似于我的`keyfilter`示例，但方式更好)

*   [dict toolz . get _ in](https://toolz.readthedocs.io/en/latest/api.html#toolz.dicttoolz.get_in)——类似`dict.get()`，但支持多深度键，例如`get_in(['a', 'b'], {'a': {'b': 1}}) = 1`

快乐！