# LeetCode 20。有效括号

> 原文：<https://dev.to/algobot76/leetcode-20-valid-parentheses-1274>

# 堆栈(优化)

```
public boolean isValid(String s) {
    if (s == null || s.length() == 0) {
        return true;
    }

    int ptr = 0;
    char[] map = new char[s.length()];

    for (char ch : s.toCharArray()) {
        switch (ch) {
            case '(':
            case '{':
            case '[':
                map[ptr++] = ch;
                break;
            case ')':
                if (ptr == 0 || map[--ptr] != '(')
                    return false;
                break;
            case '}':
                if (ptr == 0 || map[--ptr] != '{')
                    return false;
                break;
            case ']':
                if (ptr == 0 || map[--ptr] != '[')
                    return false;
                break;
        }
    }

    return ptr == 0;
} 
```

这个问题可以通过使用堆栈来解决。LeetCode 提供的解决方案使用了`Stack`类。但是，可以用数组来模拟。首先，我们创建一个与字符串`s`大小相同的空数组`map`。然后我们遍历`s`的每个字符，当遇到左括号时，我们将括号存储在`ptr`索引处的`map`中。同样，我们将`ptr`加 1。(类似于`Stack`中的`push`)。如果我们遇到右括号，我们检查右括号是否有相应的左括号存储在`map`的`ptr -1`中。如果没有，返回`false`。最后，我们检查`ptr`是否等于 0(类似于空的`Stack`)。

*时间复杂度* : `O(n)`

*额外空间* : `O(n)`