# 将相同的元素打包到一个列表中。

> 原文：<https://dev.to/antonrich/pack-the-same-elements-into-a-list-inside-a-list-2l66>

再次挑战来自 [99 榆树的问题](https://legacy.gitbook.com/book/johncrane/ninety-nine-elm-problems/details)

将列表转换为列表列表，其中源列表的重复元素被打包到子列表中。不重复的元素应该放在一个元素子列表中。

```
pack [1,1,1,2,3,3,3,4,4,4,4,5,6,6] ==
    [ [1,1,1]
    , [2]
    , [3, 3, 3]
    , [4, 4, 4, 4]
    , [5]
    , [6, 6]
    ] 
```