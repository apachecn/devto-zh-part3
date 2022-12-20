# Ruby 中的 leet code:387。字符串中的第一个唯一字符

> 原文：<https://dev.to/algobot76/leetcode-in-ruby-387-first-unique-character-in-astring-1pol>

# Index 和 RIndex

```
def first_uniq_char(s)
  s.each_char.with_index do |char, i|
    if s.index(char) == s.rindex(char)
      return i
    end
  end

  return -1
end 
```

`String#index`返回第一次出现的索引，`String#rindex`返回最后一次出现的索引。如果在遍历`s`时两者都返回相同的索引，那么这是第一个唯一字符。

*时间复杂度* : `O(n)`

*额外内存* : `O(1)`

# 哈希

```
def first_uniq_char(s)
  return -1 if s.length == 0

  counts = Hash.new 0
  s.each_char do |char|
    counts[char] += 1
  end

  s.each_char.with_index do |char, i|
    if counts[char] == 1
      return i
    end
  end

  return -1
end 
```

首先，我们创建一个名为`counts`的散列来存储`s`中每个字符的计数，然后我们遍历`s`来查看是否有一个字符的计数正好为 1。如果是，则返回其索引，否则返回-1。

*时间复杂度* : `O(n)`

*额外内存* : `O(n)`

# 两个数组

```
def first_uniq_char(s)
  counts = Array.new(26, 0)
  positions = Array.new(26, nil)
  offset = 'a'.ord

  (0...s.length).each do |index|
    char = s[index]
    pos = char.ord - offset
    counts[pos] +=1
    positions[pos] = index if positions[pos].nil?
  end

  s.each_char do |char|
    pos = char.ord - offset
    if counts[pos] == 1
      return positions[pos]
    end
  end

  return -1
end 
```

这与前面的方法非常相似。基本上，我们使用两个数组来模拟在*散列*方法中名为`counts`的散列。

*时间复杂度* : `O(n)`

*额外内存* : `O(n)`