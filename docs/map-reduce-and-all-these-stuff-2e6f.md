# 地图，缩小和所有这些东西

> 原文：<https://dev.to/mandrewcito/map-reduce-and-all-these-stuff-2e6f>

继续 python 的函数式方法，我们来看看列表函数。我们将为每个函数提供一个小例子。这些功能是映射、减少和过滤。

## 地图

Map 对序列
的每个元素应用一个函数

```
secuence = list(range(0,10))
# secuence = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] f = lambda x: x + 1
map(f, secuence)
# result [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 
```

Enter fullscreen mode Exit fullscreen mode

## 滤镜

过滤器根据布尔函数
降低安全性

```
secuence = list(range(0, 10))
# secuence = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] f = lambda x: x % 2 == 0
filter(f, secuence)
#result = [0, 2, 4, 6, 8] 
```

Enter fullscreen mode Exit fullscreen mode

## 减少

Reduce 是一个应用运算符
的“缩减列表”操作

```
from functools import reduce
secuence = list(range(0, 10))
# secuence = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] f = lambda x, y: x + y
reduce(f,secuence)
# result = 45
# Using initial value, thanks to @magicleon94 reduce(f,secuence, 10)
# result = 55 
```

Enter fullscreen mode Exit fullscreen mode