# 用 JS、Python 和 Java 学习算法#11:统计元音

> 原文：<https://dev.to/tommy3/learning-algorithms-with-js-python-and-java-11-count-the-vowels-91m>

这一系列文章以三种不同的语言讲述了斯蒂芬·格里德的 Udemy 课程。

今天的问题是正则表达式的简单介绍。

> - Directions
> 写一个函数，返回一个字符串中使用的元音数
> 。元音是字符' a '，' e'
> ，' I '，' o '，和' u '。
> -例句
> 元音(‘你好！’)- > 3
> 元音(‘为什么这么问？’)- > 4
> 元音(‘为什么？’)- > 0

# 1:迭代求解

JavaScript:

```
function vowels(str) {
  let count = 0;
  let checker = ['a', 'e', 'i', 'o', 'u'];
  for (char of str.toLowerCase()) {
    if (checker.includes(char)) {
      count++;
    }
  }
  return count;
} 
```

Python:

```
def vowels(string):
    count = 0
    checker = ['a', 'e', 'i', 'o', 'u']
    for char in string.lower():
        if char in checker:
            count += 1
    return count 
```

Java:

```
import java.util.Arrays;
import java.util.List;

static int vowels(String str) {
    int count = 0;
    List<Character> checker = Arrays.asList('a', 'e', 'i', 'o', 'u');
    for (char chr : str.toLowerCase().toCharArray()) {
        if (checker.contains(chr)) {
            count++;
        }
    }
    return count;
} 
```

# 2:使用正则表达式

JavaScript:

```
function vowels(str) {
  const matches = str.match(/[aeiou]/gi); // an array or null
  return matches ? matches.length : 0;
} 
```

Python:

```
import re

def vowels(string):
    matches = re.findall('[aeiou]', string, re.IGNORECASE)  # a list
    return len(matches) 
```

Java:

```
import java.util.regex.Matcher;
import java.util.regex.Pattern;

static int vowels(String str) {
    Pattern pattern = Pattern.compile("[aeiou]", Pattern.CASE_INSENSITIVE);
    Matcher matcher = pattern.matcher(str);
    int count = 0;
    while (matcher.find()) {
        count++;
    }
    return count;
} 
```

Java 9+:

```
import java.util.regex.Pattern;

static long vowels(String str) {
    return Pattern.compile("[aeiou]", Pattern.CASE_INSENSITIVE)
            .matcher(str)
            .results()
            .count();
} 
```