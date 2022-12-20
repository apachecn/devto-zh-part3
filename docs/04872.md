# 查找旋转排序数组中的最小数字

> 原文：<https://dev.to/hvaminion/find-the-smallest-number-in-a-rotated-sorted-array-2pbd>

问题链接:[https://leet code . com/problems/find-minimum-in-rotated-sorted-array/](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

旋转排序数组:旋转的排序数组(\_(ツ)_/)。

Leet 代码有一个更好的定义:按升序排序的数组在某个支点旋转

排序数组:[1，2，3，4，5，6，7]
旋转排序数组:
1 旋转:[7，1，2，3，4，5，6]
2 旋转:[6，7，1，2，3，4，5]
3 旋转:[5，6，7，1，2，3，4]
7 旋转:[1，2，3，4，5，6，7] -相同排序数组

要查找给定数组中的最小元素:

方法 1:暴力——遍历数组一次，找到最小元素 O(n)

方法 2:二分搜索法 O(登录号)

1.  如果元素数量为 1，则返回元素

2.  在中点选择一个元素

3.  如果数组的第一个元素大于中间元素，那么最小元素应该在第一个元素和中间元素之间

    ```
    example : [6,7,0,1,2,3,4]
    mid element is 1
    first element is 6
    smallest element 0 is between first element and mid element [7,0,1]
    The first element can be excluded because we already know that mid element is smaller than the first and we only need smallest element 
    ```

4.  如果第一个元素小于 n，那么有两种可能:
    4.1 数组可能已经排序:检查最后一个元素是否大于 mid 处的元素，如果是则返回第一个元素

    ```
    example : [1,2,3,4,5,6,7]
    mid element is 4
    first element is 1
    last element is 7
    last element is greater than the mid, so return the first element i.e.1 
    ```

    4.2 最小元素位于数组的中间元素和末尾元素之间

    ```
    example : [2,4,5,6,7,0,1]
    mid element is 6
    first element is 2
    last element is 1
    last element is lesser than the mid, so smallest element is between mid and the end [7,0,1] 
    the mid element can be excluded because we already know the last element is lesser than the mid 
    ```

最终代码: