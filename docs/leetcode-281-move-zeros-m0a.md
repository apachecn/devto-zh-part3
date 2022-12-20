# 莉特街 281 号。移动零

> 原文：<https://dev.to/algobot76/leetcode-281-move-zeros-m0a>

```
public void moveZeroes(int[] nums) {
    int count = 0;

    for (int i = 0; i < nums.length; i++) {
        if (nums[i] == 0) {
            count++;
            continue;
        }
        if (count > 0) {
            swap(nums, i, i - count);
        }
    }
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
} 
```

这种方法的本质是用最左边的零交换一个非零元素。

我们遍历数组`nums`并在遇到 0 时将`count`加 1，也就是说，我们计算最左边的 0 和一个非零元素之间的距离。一旦我们遇到一个非零元素，我们就在`i - count`把它和零交换。在迭代结束时，零已经移动到末尾，非零元素的相对位置保持不变。