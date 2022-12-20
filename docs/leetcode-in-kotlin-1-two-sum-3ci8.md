# 科特林的李特码:1。两个总和

> 原文：<https://dev.to/rkowase/leetcode-in-kotlin-1-two-sum-3ci8>

## 问题

[https://leetcode.com/problems/two-sum/](https://leetcode.com/problems/two-sum/)

给定一个整数数组，返回这两个数的索引，使它们加起来达到一个特定的目标。你可以假设每个输入只有一个解决方案，你不能两次使用同一个元素。

示例:

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1]. 
```

## 解

```
fun twoSum(nums: IntArray, target: Int): IntArray {
    for (i in 0 until nums.size) {
        for (j in i + 1 until nums.size) {
            if (nums[i] + nums[j] == target) {
                return intArrayOf(i, j)
            }
        }
    }

    throw IllegalArgumentException("No solution")
} 
```

```
fun twoSum(nums: IntArray, target: Int): IntArray {
    val map = HashMap<Int, Int>()
    nums.forEachIndexed { idx, item ->
        val found = map[target - item]
        found?.let {
            return intArrayOf(found, idx)
        }
        map[item] = idx
    }
    throw IllegalArgumentException("No solution")
} 
```

## 测试代码

```
@Test
fun twoSum() {
    t(intArrayOf(2, 7, 11, 15), 9, intArrayOf(0, 1))
    t(intArrayOf(3, 2, 4), 6, intArrayOf(1, 2))
}

private fun t(nums: IntArray, target: Int, expected: IntArray) {
    val actual = solution.twoSum(nums, target)

    assertEquals(expected.size, actual.size)
    expected.forEachIndexed { index, i ->
        assertEquals(expected[index], i)
    }
} 
```

## 模型解

[https://leetcode.com/problems/two-sum/solution/](https://leetcode.com/problems/two-sum/solution/)

### 方法一:蛮力

```
public int[] twoSum(int[] nums, int target) {
    for (int i = 0; i < nums.length; i++) {
        for (int j = i + 1; j < nums.length; j++) {
            if (nums[j] == target - nums[i]) {
                return new int[] { i, j };
            }
        }
    }
    throw new IllegalArgumentException("No two sum solution");
} 
```

### 方法 2:两遍哈希表

```
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        map.put(nums[i], i);
    }
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement) && map.get(complement) != i) {
            return new int[] { i, map.get(complement) };
        }
    }
    throw new IllegalArgumentException("No two sum solution");
} 
```

### 方法 3:一遍哈希表

```
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[] { map.get(complement), i };
        }
        map.put(nums[i], i);
    }
    throw new IllegalArgumentException("No two sum solution");
} 
```