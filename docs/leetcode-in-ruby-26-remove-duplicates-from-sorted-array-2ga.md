# LeetCode in Ruby: 26。从排序数组中删除重复项

> 原文：<https://dev.to/algobot76/leetcode-in-ruby-26-remove-duplicates-from-sorted-array-2ga>

```
def remove_duplicates(nums)
  nums.uniq!
  return nums.length
end 
```

首先，使用`uniq!`删除`nums`中的所有重复项。然后返回它的长度。

*时间复杂度* : `O(n)`

*额外内存* : `O(1)`