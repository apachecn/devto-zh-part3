# TIL:python 中 n + 1 == n 时？

> 原文：<https://dev.to/shaikhul/when-n--1--n-in-python-26p7>

今天我在 python 文档中寻找 [doctest](https://docs.python.org/3/library/doctest.html) ，发现了下面的

```
if n+1 == n:  # catch a value like 1e300
    raise OverflowError("n too large") 
```

我立刻打开外壳，试了一下:

```
>>> from math import exp
>>> n = exp(300)
>>> n
1.9424263952412558e+130
>>> n + 1 == n
True 
```

直到`n+1 == n`！以及如何防范一个**大得离谱的数字**