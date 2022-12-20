# λ表达式

> 原文：<https://dev.to/mandrewcito/lambda-expressions-1p50>

这个操作符的主要目的是声明匿名函数(抛弃函数)。

## 通用语法

参数列表:表达式

## 例子

lambda 表达式的一个典型例子是算术表达式的使用，但是这个运算符可以用在很多情况下

```
f = lambda x, y: x + y
f(1,2)
# outPut : 3 
```

Enter fullscreen mode Exit fullscreen mode

```
f = lambda x: x[0]
f([2,3,4,5,6,7])
# outPut : 2 
```

Enter fullscreen mode Exit fullscreen mode

```
f = lambda x, y: x if x < y else y
f(1,2)
# outPut : 2 
```

Enter fullscreen mode Exit fullscreen mode

你可以快速实现一些有用的小函数，比如 len():

```
size = lambda x : 1 + size(x[1:]) if x != [] else 0
size([1,2,3])
# outPut : 3 
```

Enter fullscreen mode Exit fullscreen mode

lambda 运算符本身非常有用，但是我们可以将它与 list/dict 理解、映射、归约和过滤结合起来加以利用。在这种情况下，我们将看到这个运营商的真正力量。