# 如何找到一个整数数组中所有和等于给定数字的对？

> 原文：<https://dev.to/mu/how-do-you-find-all-pairs-of-an-integer-array-whose-sum-is-equal-to-a-given-number-509e>

```
input = [10,18,20,5,11,2,10,15]
sum = 20

length = len(input) 
for i in range(length): 
  for j in range(i+1, length):
    if(input[i] + input[j] == sum):
      print(input[i], input[j]) 
```

Enter fullscreen mode Exit fullscreen mode

并且输出是

```
10 10
18 2
5 15 
```

Enter fullscreen mode Exit fullscreen mode