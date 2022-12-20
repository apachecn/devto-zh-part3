# 科特林的李特码:20。有效括号

> 原文：<https://dev.to/rkowase/leetcode-in-kotlin-20-valid-parentheses-3g7h>

## 问题

[https://leetcode.com/problems/valid-parentheses/](https://leetcode.com/problems/valid-parentheses/)

给定一个仅包含字符'('，')'，' { '，' } '，'['和']'的字符串，确定输入字符串是否有效。

在以下情况下，输入字符串有效:

左括号必须用相同类型的括号括起来。
左括号必须以正确的顺序关闭。
注意，空字符串也被认为是有效的。

示例:

```
Input: "()"
Output: true
Example 2:

Input: "()[]{}"
Output: true
Example 3:

Input: "(]"
Output: false
Example 4:

Input: "([)]"
Output: false
Example 5:

Input: "{[]}"
Output: true 
```

## 解

```
fun isValid(s: String): Boolean {
    val stack = Stack<Char>()

    val map = mapOf(
        '}' to '{',
        ')' to '(',
        ']' to '['
    )

    s.forEach {
        stack.push(it)

        if (map.containsKey(it)) {
            if (stack.size < 2) {
                return false
            }

            if (stack[stack.size - 2] != map[it]) {
                return false
            }

            stack.pop()
            stack.pop()
        }
    }

    return stack.isEmpty()
} 
```

```
fun isValid(s: String): Boolean {
    val stack = Stack<Char>()
    val range = (1..2)
    s.forEach { c ->
        when {
            stack.empty() -> stack.push(c)
            (c - stack.peek()) in range -> stack.pop()
            else -> stack.push(c)
        }
    }
    return stack.empty()
} 
```

```
fun isValid(s: String): Boolean {
    val stack = Stack<Char>()
    val map = mapOf(
        '(' to ')', ')' to '(',
        '[' to ']', ']' to '[',
        '{' to '}', '}' to '{'
    )
    s.forEach { c ->
        when {
            stack.empty() -> stack.push(c)
            stack.peek() == map[c] -> stack.pop()
            else -> stack.push(c)
        }
    }
    return stack.empty()
} 
```

## 测试代码

```
@Test
fun isValid() {
    assertTrue(solution.isValid("()"))
    assertTrue(solution.isValid("[]"))
    assertTrue(solution.isValid("{}"))
    assertTrue(solution.isValid("{()}"))
    assertTrue(solution.isValid("{([])}"))
    assertFalse(solution.isValid("{{"))
    assertFalse(solution.isValid("{)"))
    assertFalse(solution.isValid(")"))
    assertFalse(solution.isValid(")("))
} 
```

## 模型解

[https://leetcode.com/problems/valid-parentheses/solution/](https://leetcode.com/problems/valid-parentheses/solution/)

### 方法一:叠加

```
class Solution {

  // Hash table that takes care of the mappings.
  private HashMap<Character, Character> mappings;

  // Initialize hash map with mappings. This simply makes the code easier to read.
  public Solution() {
    this.mappings = new HashMap<Character, Character>();
    this.mappings.put(')', '(');
    this.mappings.put('}', '{');
    this.mappings.put(']', '[');
  }

  public boolean isValid(String s) {

    // Initialize a stack to be used in the algorithm.
    Stack<Character> stack = new Stack<Character>();

    for (int i = 0; i < s.length(); i++) {
      char c = s.charAt(i);

      // If the current character is a closing bracket.
      if (this.mappings.containsKey(c)) {

        // Get the top element of the stack. If the stack is empty, set a dummy value of '#'
        char topElement = stack.empty() ? '#' : stack.pop();

        // If the mapping for this bracket doesn't match the stack's top element, return false.
        if (topElement != this.mappings.get(c)) {
          return false;
        }
      } else {
        // If it was an opening bracket, push to the stack.
        stack.push(c);
      }
    }

    // If the stack still contains elements, then it is an invalid expression.
    return stack.isEmpty();
  }
} 
```