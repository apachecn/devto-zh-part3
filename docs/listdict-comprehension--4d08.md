# 列表/字典理解

> 原文：<https://dev.to/mandrewcito/listdict-comprehension--4d08>

List/Dict comprehension 是一种简单的方法，可以用我们需要的一堆值来创建列表和 Dict。为了真正利用这个操作符，我们必须将它与 lambda 操作符结合起来，但是首先，我们将看到一些简单的例子。

```
 [x * 2 for x in range(3)]
# output: [0, 2, 4] 
{chr(65+x) : x for x in range(3)}
# output: {'A': 0, 'C': 2, 'B': 1} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将结合这两个操作符:

```
serie = [x for x in range(20)]
f = lambda x: chr(x + 66) if x % 2 == 0 else chr(x + 65)
[f(x) for x in serie]
# output: ['B', 'B', 'D', 'D', 'F', 'F', 'H', 'H', 'J', 'J', 'L', 'L', 'N', 'N', 'P', 'P', 'R', 'R', 'T', 'T'] 
```

Enter fullscreen mode Exit fullscreen mode