# LeetCode 31。下一个排列

> 原文：<https://dev.to/algobot76/leetcode-31-next-permutation-1f5o>

```
public class Solution {
    public void nextPermutation(int[] nums) {
        int i = nums.length - 2;
        while (i >= 0 && nums[i + 1] <= nums[i]) {
            i--;
        }
        if (i >= 0) {
            int j = nums.length - 1;
            while (j >= 0 && nums[j] <= nums[i]) {
                j--;
            }
            swap(nums, i, j);
        }
        reverse(nums, i + 1);
    }

    private void reverse(int[] nums, int start) {
        int i = start;
        int j = nums.length - 1;
        while (i < j) {
            swap(nums, i, j);
            i++;
            j--;
        }
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
} 
```

我们从右到左遍历`nums`,找到降序第一次出现的数字`nums[i]`。然后我们从右到`i+1`扫描 num，找到一个大于`nums[i]`的数字，并与之交换。最后，我们逆转`nums[i+1] ... nums[nums.length-1]`。

通过这样做，我们可以保证:

1.  下一个排列总是大于或等于当前排列(我们假设当前排列中的数字不是按降序排列的)。
2.  不存在大于当前置换并且小于由上述代码生成的下一个置换的置换。
3.  如果当前排列中的数字已经以降序排序(即最大可能值)，则下一个排列具有最小值。

*时间复杂度* : `O(n)`

*额外空间* : `O(1)`