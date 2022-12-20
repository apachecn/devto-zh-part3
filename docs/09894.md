# Ruby 中的 leet code:242。有效变位词

> 原文：<https://dev.to/algobot76/leetcode-in-ruby-242-valid-anagram-37f4>

# 计数

```
def is_anagram(s, t)
  return false unless s.length == t.length

  ('a'..'z').each do |char|
    return false unless s.count(char) == t.count(char)
  end

  true
end 
```

如果两个字符串`s`和`t`在每个字符中出现的次数相同，那么它们就是变位词。即使它的时间复杂度是`O(n^2)`，它也比后两种方法要快。

*时间复杂度* : `O(n^2)`

*额外内存* : `O(1)`

# 排序

```
def is_anagram(s, t)
  return false unless s.length == t.length

  s_ary = s.split('').sort
  t_ary = t.split('').sort

  (0...s.length).each do |index|
    return false if s_ary[index] != t_ary[index]
  end

  return true
end 
```

我们将字符串`s`和`t`转换成两个数组:`s_ary`和`t_ary`，对字符进行排序。如果`s`和`t`是变位词，那么`s_ary`和`t_ary`对于每个索引必须具有相同的字符。这就是循环正在检查的内容。

*时间复杂度* : `O(nlgn)`

*额外内存* : `O(n)`

# 排序(一种更快的变体)

```
def is_anagram(s, t)
  return false unless s.length == t.length

  s.bytes.sort! == t.bytes.sort!
end 
```

这与前一个相同，除了我们不是比较字符而是字节。

*时间复杂度* : `O(nlgn)`

*额外内存* : `O(n)`