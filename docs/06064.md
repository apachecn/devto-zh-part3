# Java 中的借用代码:209

> 原文：<https://dev.to/algobot76/leetcode-in-java-209-e38>

```
public class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        int sum = 0;
        int ans = Integer.MAX_VALUE;

        for (int l = 0, r = 0; r < nums.length; r++) {
            sum += nums[r];
            while (sum >= s) {
                ans = Math.min(ans, r - l + 1);
                sum -= nums[l++];
            }
        }

        return ans == Integer.MAX_VALUE ? 0 : ans;
    }
} 
```

这个问题通过使用两个指针`l`和`r`来解决。在 for 循环中，我们递增地将`r`处的元素添加到`sum`。每当`sum`大于或等于`s`时，我们计算两个指针之间的距离(`r - l + 1`)，如果该距离小于`ans`，我们用该距离更新`ans`。然后从`sum`中减去`l`处的元素，并增加`l`。在 for 循环的末尾，我们应该得到最小子数组的长度。

*时间复杂度* : `O(n)`

*额外空间* : `O(1)`