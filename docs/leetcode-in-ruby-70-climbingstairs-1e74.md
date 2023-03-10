# LeetCode in Ruby: 70。爬楼梯

> 原文：<https://dev.to/algobot76/leetcode-in-ruby-70-climbingstairs-1e74>

# 动态编程

```
def climb_stairs(n)
  p = 1
  q = 1

  (n - 1).times do
    temp = q
    q += p
    p = temp
  end

  q
end 
```

这个问题可以通过使用动态规划来解决。我们将`f(n)`定义为你从以下位置爬到第`n`步的次数:

1.  从第`n-1`步开始的一步，
2.  从第`n-2`步开始的两步。

因此，`f(n) = f(n-1) + f(n-2)`，与斐波那契数列相同。我们有两种基本情况:`f(1) = 1`和`f(2) = 2`。第 4 行和第 5 行是斐波那契数列的前两个数字。为了得到第 n 个数，我们把这些数相加，直到第 n 个数。这里，我们通过存储前两个数字来优化性能。

*时间复杂度* : `O(n)`

*额外内存* : `O(1)`