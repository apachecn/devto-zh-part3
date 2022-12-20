# 红宝石代码:771 颗宝石

> 原文：<https://dev.to/algobot76/leetcode-in-ruby-771-jewels-and-stones-4k3d>

# 1 行

这种方法基于 Ruby 的`String#count`方法。它将字符串`j`作为要在字符串`s`中计数的字符集。这是最快也是最简单的解决方案。

*时间复杂度* : `O(j.length+ s.length)`

*额外内存* : `O(1)`

# 哈希

```
def num_jewels_in_stones(j, s)
  stones = Hash.new 0
  s.each_char do |char|
    stones[char] += 1
  end

  sum = 0
  j.each_char do |char|
    sum += stones[char]
  end

  return sum
end 
```

首先，我们遍历字符串`s`并创建一个名为`stones`的散列，它使用`s`中的字符作为键，使用它们的出现次数作为值。然后我们浏览`j`的每个角色，并总结它们的出现次数。

*时间复杂度* : `O(j.length+ s.length)`

*额外内存* : `O(s.length)`