# LeetCode 7。反向整数

> 原文：<https://dev.to/algobot76/leetcode-7-reverse-integer-73j>

```
public int reverse(int x) {
    int rev = 0;

    while (x != 0) {
        int pop = x % 10;
        x /= 10;

        if (rev > Integer.MAX_VALUE / 10 || (rev == Integer.MAX_VALUE / 10 && pop > 7)) {
            return 0;
        }
        if (rev < Integer.MIN_VALUE / 10 || (rev == Integer.MIN_VALUE / 10 && pop < -8)) {
            return 0;
        }

        rev = rev * 10 + pop;
    }

    return rev;
} 
```

反转整数类似于反转字符串。我们得到最后一位数字`x % 10`，并将其加到`rev`。我们重复这个过程，直到`x`为 0。两个 if 语句用于防止整数溢出。

*时间复杂度* : `O(log(x))`

*额外空间* : `O(1)`

## 关于整数溢出的一个注记

当`rev * 10 + pop`之和大于/小于最大值(`Integer.MAX_VALUE`)/最小值(`Integer.MIN_VALUE`)时，出现整数。所以我们需要检查`rev`和`pop`的值。

*   如果`rev > Integer.MAX_VALUE/10`或`rev < Integer.MIN_VALUE/10`，`rev * 10 + pop`将溢出。
*   如果`rev == Integer.MAX_VALUE / 10`，`pop`一定小于等于 7，因为`2^32 - 1 % 10 == 7`。
*   如果`rev == Integer.MIN_VALUE / 10`，`pop`必须大于或等于-8，因为`-2^32 % 10 == 8`。