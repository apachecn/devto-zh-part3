# Python 列表理解太棒了

> 原文：<https://dev.to/iamfeysal/python-list-comprehensions-are-awesome-522>

# Python 的列表理解很牛逼。

> vals =[集合
> 中值的表达式
> if 条件]

# 这相当于:

> 集合中值的 vals =[]
> :
> if 条件:
> vals.append(表达式)

# 举例:

> even _ squares =[x * x for x in range(10)if not x % 2]
> even _ squares
> [0，4，16，36，64] #result