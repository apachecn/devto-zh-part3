# 代码 2018 第五天来临:炼金术减少

> 原文：<https://dev.to/steadbytes/aoc-2018-day-5-alchemical-reduction-10g0>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/advent-of-code-2018/05/)
> 
> 完整的解决方案可以在 [GitHub](https://github.com/SteadBytes/advent-of-code-2018/blob/f1842d04abab5c3fc5d1cb966d9da8b0ffa0ac29/05/solution.py) 上找到

## 第一部分

给定一个包含圣诞老人服装中使用的聚合物的字符串表示的[谜题输入](https://github.com/SteadBytes/advent-of-code-2018/blob/f1842d04abab5c3fc5d1cb966d9da8b0ffa0ac29/05/input.txt)，我们被要求找出**在聚合物**完全反应后还剩多少个单元。我们被告知，反应发生在同类型但极性*相反*的*相邻*单元之间:

*   *类型* =字母
*   *极性* =大写/小写

```
aA -> reaction
ab -> no reaction
aB -> no reaction
aa -> no reaction 
```

Enter fullscreen mode Exit fullscreen mode

当一对相邻的单元反应时，它们被破坏，然后可能由现在相邻的一对单元产生另一个反应，例如:

```
abBAbA -> aAbA -> bA 
```

Enter fullscreen mode Exit fullscreen mode

### 解析输入

这是将整个输入作为单个字符串读入的一个简单例子:

```
with open("input.txt") as f:
    polymer = f.read().strip() # remove leading/trailing whitespace just in case 
```

Enter fullscreen mode Exit fullscreen mode

### 模拟一种反应

随着反应的进行，可以使用[堆栈](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))来保存聚合物单元，从而模拟完整的反应:

1.  初始化一个空堆栈
2.  对于聚合物中的每个单元:
    *   如果单位没有引起反应，把它推到堆栈上
    *   否则，从堆栈中弹出前一个单元(销毁该对)
3.  堆中剩余的单元代表完全反应的聚合物

一对单位如果同类型极性相反会有反应。这种关系可以用一个字典来表示，该字典从每个可能的单元映射到其对应的反应单元:

```
import string

unit_pairs_map = {
    **{ch: ch.upper() for ch in string.ascii_lowercase},
    **{ch: ch.lower() for ch in string.ascii_uppercase},
} 
```

Enter fullscreen mode Exit fullscreen mode

给定一个聚合物单元，通过在`unit_pairs_map` :
中查找，可以找到发生反应所需的相邻单元

```
# find required adjacent unit for 'a' unit_pairs_map['a']
# A 
# find required adjacent unit for 'A' unit_pairs_map['A']
# a 
```

Enter fullscreen mode Exit fullscreen mode

现在可以实现上面的算法:

```
def part_1(polymer):
    stack = []
    for unit in polymer:
        # there is a previous unit and it reacts with the current unit
        if stack and unit == unit_pairs_map[stack[-1]]:
            # reaction -> destroy the units
            stack.pop()
        else:
            # no reaction -> unit remains
            stack.append(unit)
    return len(stack) 
```

Enter fullscreen mode Exit fullscreen mode

对于我的谜题输入，结果是 **10496** 。

## 第二部分

接下来，我们被告知，如果去除某种聚合物类型，可以减少更完全的反应。我们被要求找出最短的可能的聚合物的**长度，方法是移除一种类型的所有单元，并对结果进行充分反应。**

1.  对于每个可能的单元类型
    *   从原始聚合物中移除该单元的所有出现
    *   使聚合物充分反应
    *   测量反应聚合物的长度
2.  返回最短长度

算法步骤 1 的子任务实际上与拼图的第 1 部分相同。为了让
保持干爽，这个逻辑可以从`part_1`函数中提取出来:

```
def react_polymer(polymer):
    # reaction logic from part 1
    stack = []
    for unit in polymer:
        if stack and unit == unit_pairs_map[stack[-1]]:
            stack.pop()
        else:
            stack.append(unit)
    return stack

def part_1(polymer):
    return len(react_polymer(polymer)) 
```

Enter fullscreen mode Exit fullscreen mode

第二部分的解决方案现在可以利用算法中步骤 1
的子任务的`react_polymer`函数:

```
def part_2(polymer):
    # original polymer length is the shortest so far
    best = len(polymer)
    for ch in string.ascii_lowercase: # unit types = letters of the alphabet
        reacted_polymer = react_polymer(
            # remove all occurrences (both polarities)
            [u for u in polymer if u != ch and u != ch.upper()]
        )
        # keep track of the shortest polymer found so far
        best = min(best, len(reacted_polymer))
    return best 
```

Enter fullscreen mode Exit fullscreen mode

对于我的谜题输入，结果是 **5774** 。

### 资源

*   [堆栈](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))

## - [使用 Python 列表作为堆栈](https://docs.python.org/3.1/tutorial/datastructures.html#using-lists-as-stacks)