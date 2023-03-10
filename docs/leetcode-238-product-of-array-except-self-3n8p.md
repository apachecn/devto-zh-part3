# LeetCode 238。除自身以外的数组乘积

> 原文：<https://dev.to/algobot76/leetcode-238-product-of-array-except-self-3n8p>

```
public class Solution {
    public int[] productExceptSelf(int[] nums) {
        int len = nums.length;
        int[] ans = new int[len];

        for (int i = 0, temp = 1; i < len; i++) {
            ans[i] = temp;
            temp *= nums[i];
        }
        for (int i = len - 1, temp = 1; i >= 0; i--) {
            ans[i] *= temp;
            temp *= nums[i];
        }

        return ans;
    }
} 
```

第一次 for 循环后，`nums[i]`等于从`0`到`i - 1`的数的乘积。在第二次 for 循环的每次迭代中，`nums[i]`乘以从`i + 1`到`len - 1`的数的乘积。因此，在两个 for 循环之后，`nums[i] = nums[0] * nums[1] * ... * nums[i-1] * ... * nums[i+1] * ... * nums[len-1]`。

*时间复杂度* : `O(n)`

*额外空间* : `O(1)`