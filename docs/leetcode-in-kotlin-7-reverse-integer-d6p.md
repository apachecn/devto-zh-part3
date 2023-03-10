# 科特林的李特码:7。反向整数

> 原文：<https://dev.to/rkowase/leetcode-in-kotlin-7-reverse-integer-d6p>

## 问题

[https://leetcode.com/problems/reverse-integer/](https://leetcode.com/problems/reverse-integer/)

给定一个 32 位有符号整数，整数的反数位。

例 1:

```
Input: 123
Output: 321
Example 2:

Input: -123
Output: -321
Example 3:

Input: 120
Output: 21 
```

注意:
假设我们处理的环境只能存储 32 位有符号整数范围内的整数:【231，231 1】。为了解决这个问题，假设当反向整数溢出时，函数返回 0。

## 解

```
fun reverse(x: Int): Int {
    val str = x.toString()
    val stack = Stack<Char>()
    var result = ""

    str.forEach {
        stack.push(it)
    }

    while (true) {
        if (stack.size == 0) break

        if (result.isEmpty() && stack.peek() == '0') {
            if (stack.size == 1) {
                result = "0"
                break
            }

            stack.pop()
            continue
        }

        if (stack.peek() == '-') {
            result = stack.peek() + result
            break
        }

        result += stack.pop()
    }

    return try {
        result.toInt()
    } catch (e: NumberFormatException) {
        0
    }
} 
```

```
fun reverse(x: Int): Int {
    val output = StringBuilder()
    var currVal = x

    if (currVal < 0) {
        currVal *= -1
        output.append("-")
    }

    output.append(currVal.toString().reversed())

    return output.toString().toIntOrNull() ?: 0
} 
```

```
fun reverse(x: Int): Int {
    val y = abs(x)
        .toString()
        .reversed()
        .toIntOrNull() ?: return 0

    return when {
        0 <= x -> y
        else -> y * -1
    }
} 
```

## 测试代码

```
@Test
fun reverse() {
    assertEquals(321, solution.reverse(123))
    assertEquals(-321, solution.reverse(-123))
    assertEquals(21, solution.reverse(120))
    assertEquals(21, solution.reverse(1200))
    assertEquals(0, solution.reverse(0))
    assertEquals(0, solution.reverse(1534236469))
} 
```

## 模型解

[https://leetcode.com/problems/reverse-integer/solution/](https://leetcode.com/problems/reverse-integer/solution/)

### 方法 1:弹出和推送数字&溢出前检查

```
class Solution {
    public int reverse(int x) {
        int rev = 0;
        while (x != 0) {
            int pop = x % 10;
            x /= 10;
            if (rev > Integer.MAX_VALUE/10 || (rev == Integer.MAX_VALUE / 10 && pop > 7)) return 0;
            if (rev < Integer.MIN_VALUE/10 || (rev == Integer.MIN_VALUE / 10 && pop < -8)) return 0;
            rev = rev * 10 + pop;
        }
        return rev;
    }
} 
```