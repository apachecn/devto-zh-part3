# 如何在给定的整数数组中找到重复的数字？

> 原文：<https://dev.to/mu/how-do-you-find-the-duplicate-number-on-a-given-integer-array-mlp>

这五行代码将从给定的数字列表
中挑选出重复和非重复的元素

```
input = [10,20,30,11,10]

duplicate_list, non_duplicate_list = [],[]
for el in input:
  duplicate_list.append(el) if(el in non_duplicate_list) else non_duplicate_list.append(el)

print(duplicate_list, non_duplicate_list) 
```

Enter fullscreen mode Exit fullscreen mode

并且输出是

```
([10], [10, 20, 30, 11]) 
```

Enter fullscreen mode Exit fullscreen mode

感谢[https://simple programmer . com/programming-interview-questions/](https://simpleprogrammer.com/programming-interview-questions/)发帖提问