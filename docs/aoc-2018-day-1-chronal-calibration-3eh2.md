# Code 2018 第 1 天的到来:长期校准

> 原文：<https://dev.to/steadbytes/aoc-2018-day-1-chronal-calibration-3eh2>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/advent-of-code-2018/01/)
> 
> 完整的解决方案可以在 [GitHub](https://github.com/SteadBytes/advent-of-code-2018/blob/87cf4c1ec25b1ca956d8325e540603037b8baf0c/01/solution.py) 上找到

## 第一部分

从频率变化的[谜题输入](https://github.com/SteadBytes/advent-of-code-2018/blob/80c07556dc4fff2b52449f754cb4c2b91b5b56dd/01/input.txt)中，我们被要求计算所有这些变化发生后的**结果频率。这包括两个步骤:**

1.  将难题输入解析为一系列数值
2.  把它们加起来！

### 解析输入

检查字谜输入，我们看到每个频率都是由换行符分隔的正整数或负整数。例如:

```
+12
-10
-4
-8
+18 
```

Enter fullscreen mode Exit fullscreen mode

要将其解析为一系列数值，我们需要:

1.  在新行上拆分输入
2.  将形式为`<sign><value>`的字符串解析为有符号整数
    *   正则表达式`[\+|-]\d+`

文件 API:
直接支持在新行上拆分 Python [文件对象](https://docs.python.org/3/glossary.html#term-file-object)

```
with open("input.txt") as puzzle_input:
    lines = puzzle_input.readlines() 
```

Enter fullscreen mode Exit fullscreen mode

直接使用 Python 的内置`int`函数可以将每个频率解析成一个带符号的整数。当给定一个字符串(比如我们的频率)时，它将*强制*将该值转换为一个整数，同时考虑一个符号(如果存在的话):

```
>>> int("+15)
15
>>> int("-15")
-15 
```

Enter fullscreen mode Exit fullscreen mode

将它封装在一个函数中，我们得到:

```
def parse_frequencies(puzzle_input):
    return [int(f) for f in puzzle_input.readlines()] 
```

Enter fullscreen mode Exit fullscreen mode

### 求和频率

由于我们的频率是一个整数列表，Python 通过调用`sum` :
简化了这一部分

```
def part_1(frequencies):
    return sum(frequencies) 
```

Enter fullscreen mode Exit fullscreen mode

## 第二部分

接下来，我们被要求找出在连续应用谜题输入中的频率变化期间出现两次的**第一频率。谜题描述给出了一个重要提示:**

> 请注意，在找到重复频率之前，您的设备可能需要多次重复其频率变化列表，并且在处理列表的过程中可能会找到重复频率。

有了这个我们就可以分解问题了:

1.  以`0`的频率开始
2.  *重复*迭代频率变化序列，将每次变化应用于当前频率值。
3.  跟踪每次迭代的频率值。
4.  如果遇到以前见过的频率，返回它。

```
def part_2(frequencies):
    frequency_memo = {0}
    current_frequency = 0
    while True:
        for f in frequencies:
            current_frequency += f
            if current_frequency in frequency_memo:
                return current_frequency
            frequency_memo.add(current_frequency) 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

《代码的出现》用一个温和的介绍吸引了我们，展示了一般的谜题结构:

1.  从文本文件中读取一些输入。
2.  用它来解决 2 个(通常是相关的)谜题。

### 资源

*   [文件对象](https://docs.python.org/3/glossary.html#term-file-object)
*   [列出理解](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions)
*   [设置](https://docs.python.org/3/tutorial/datastructures.html#sets)