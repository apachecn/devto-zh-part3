# 如何在 for-loop 中获取有序数组列表中的元素

> 原文：<https://dev.to/kozmof/how-to-get-elements-in-a-list-of-tuples-with-ordered-numbers-in-for-loop-35d2>

我遇到过一个案例，在有序数字的元组列表中使用元素。这很有效。

```
example_list = [("a", "b"), ("c", "d"), ("e", "f"), ("g", "h"), ("i", "j")]

for num, (el1, el2) in enumerate(example_list):
    print(num, el1, el2) 
```

结果:

```
 0 a b
 1 c d
 2 e f
 3 g h
 4 i j 
```

版本:Python 3