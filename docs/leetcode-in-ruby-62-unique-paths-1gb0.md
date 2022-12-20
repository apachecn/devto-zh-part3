# LeetCode in Ruby: 62。独特的路径

> 原文：<https://dev.to/algobot76/leetcode-in-ruby-62-unique-paths-1gb0>

# 回溯

```
def unique_paths(m, n)
  backtrack(0, 0, m, n)
end

def backtrack(r, c, m, n)
  if r == m - 1 && c == n - 1
    return 1
  end
  if r >= m || c >= n
    return 0
  end
  backtrack(r + 1, c, m, n) + backtrack(r, c + 1, m, n)
end 
```

这是一种解决问题的蛮力方法。不要在 LeetCode 上提交这个解决方案，因为它会超过时间限制。它通过使用一种叫做[回溯](https://en.wikipedia.org/wiki/Backtracking)的技术来遍历所有可能的路径。

# 识记(自上而下)

```
def unique_paths(m, n)
  @cache = Array.new(m + 1) { Array.new(n + 1, -1) }
  backtrack(0, 0, m, n)
end

def backtrack(r, c, m, n)
  return 1 if r == m - 1 && c == n - 1
  return 0 if r >= m || c >= n

  @cache[r + 1][c] = backtrack(r + 1, c, m, n) if @cache[r + 1][c] == -1
  @cache[r][c + 1] = backtrack(r, c + 1, m, n) if @cache[r][c + 1] == -1
  @cache[r + 1][c] + @cache[r][c + 1]
end 
```

# 识记(自下而上)

```
def unique_paths(m, n)
  @cache = Array.new(m + 1) { Array.new(n + 1, 0) }
  @cache[m - 1][n] = 1
  (m - 1).downto(0).each do |r|
    (n - 1).downto(0).each do |c|
      @cache[r][c] = @cache[r + 1][c] + @cache[r][c + 1]
    end
  end
  @cache[0][0]
end 
```