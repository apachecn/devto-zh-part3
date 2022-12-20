# 代码 2018 第二天的来临:库存管理系统

> 原文：<https://dev.to/steadbytes/aoc-2018-day-2-inventory-management-system-3km0>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/advent-of-code-2018/02/)
> 
> 完整的解决方案可以在 [GitHub](https://github.com/SteadBytes/advent-of-code-2018/blob/12ceab13ae7f89202d1590129f1b3eb2fe27bcd1/02/solution.py) 上找到

## 第一部分

我们被要求为[难题输入](https://github.com/SteadBytes/advent-of-code-2018/blob/12ceab13ae7f89202d1590129f1b3eb2fe27bcd1/02/input.txt)中的箱子 id 列表计算一个**校验和**。

校验和被定义为包含**恰好两个任意字母**的 id 数乘以 t 包含**恰好三个任意字母**的 id 数。

1.  解析输入以获得盒子 id 列表
2.  维护一个包含任意两个字母的 id 总数的计数器和一个包含任意三个字母的 id 总数的计数器
3.  对于每个箱子 ID:
    *   找出每个字母出现的频率
    *   如果任何一个字母恰好出现两/三次，则递增相应的计数器
4.  将两个计数器的最终值相乘以获得校验和

### 解析输入

难题输入的每一行代表一个盒子 ID:

```
pbopcmjeizuhxlqnwasfgtycdm
pjokrmjeizuhxlqnfasfguycdv
pbokrpjejkuhxlqnwasfgtycdv
sbokrmjeizuhxaqnwastgtycdv
pbokrmjeizuhxljnnasfgtycn 
```

Enter fullscreen mode Exit fullscreen mode

我们可以一次读一行，但是我要把它们全部读成一个列表，以便在第二部分中重用。

*   这是可行的，因为输入文件*小* (6750 字节)

```
with open("input.txt") as f:
    box_ids = [l.strip() for l in puzzle_input_f.readlines()] 
```

Enter fullscreen mode Exit fullscreen mode

使用`str.strip`来删除结尾/开头的空格并不是*严格意义上的*所必需的，因为 Eric Wastl 已经为我们提供了整洁干净的输入数据。然而，在处理不需要尾随/前导空格的外部数据时，这通常是一个好的做法。

### 框 ID 字母频率

一种简单的方法是遍历框 ID，并为每个字符搜索剩余的重复出现的字符:

```
frequencies = {}
for i, ch in enumerate(box_id):
    if ch not in frequencies:
        frequencies[ch]
    for j, ch2 in enumerate(box_id):
        if i != j and ch == ch2:
            frequencies[ch] += 1 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但我们可以做得更好！这种方法的一些问题:

*   低效的 **O(n <sup>2</sup> )** 运行时造成的嵌套循环。对于 box ID 中的每个字母，它“看”每隔一个字母。
*   这个问题不需要记录字母是什么，只需要记录它们的频率

为了改进这一点，我们可以首先将盒子 ID 减少到它的**唯一的**字母，然后统计每个字母在整个盒子 ID 中的出现次数。在 Python 中，可以通过从字符串创建一个`set`来取消字符串的唯一性(`set`将在本系列中多次出现*):* 

```
box_id = "pboxwmjeituhxlqnwasfgtycdv"
unique_letters = set(box_id) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以使用`str.count`来检索每个唯一字母的频率:

```
freqs = [box_id.count(l) for l in unique_letters] 
```

Enter fullscreen mode Exit fullscreen mode

通过 700000 次循环的平均执行时间比较两者的性能:

*   接近 1: 30.4 秒
*   方法 2: 3.54 秒

### 计算校验和

对于字母频率列表，计算校验和需要找出频率恰好为 2 的次数，频率恰好为 3 的次数，然后将这些计数相乘:

```
def part_1(box_ids):
    twos = 0
    threes = 0
    for _id in box_ids:
        unique_letters = set(_id)
        freqs = [_id.count(l) for l in unique_letters]
        twos += 1 if [f for f in freqs if f == 2] else 0
        threes += 1 if [f for f in freqs if f == 3] else 0
    return twos * threes 
```

Enter fullscreen mode Exit fullscreen mode

对于我的难题输入，这给出了一个校验和 **6000**

## 第二部分

我们被告知，列表中只有两个正确的框 id，并且它们是唯一两个**相差一个字符**的框 id。我们被要求找出这两个盒子 id 之间的**常用字母**。

1.  对于列表中的每个框 ID，将其与其他框 ID 进行比较，如下所示:
    *   比较两个框 id 中每个位置的字母
    *   如果字母不同，增加一个计数器
    *   如果在比较了所有的字母之后，计数器等于 1，则返回两个盒子 id
        *   我们可以在这里提前返回，以避免搜索剩余的框 id，因为我们在问题中被告知，只有一对框 id 匹配这个不变量。

为了比较每一对可能的盒子 id，我们可以使用双 for 循环:

```
for id_1 in box_ids:
    for id_2 in box_ids:
        # compare 
```

Enter fullscreen mode Exit fullscreen mode

不过，我准备用 [`itertools.product`](https://docs.python.org/3/library/itertools.html#itertools.product) 来更 Pythonic 一点:

```
import itertools

for id_1, id_2 in itertools.product(box_ids, box_ids):
    # compare 
```

Enter fullscreen mode Exit fullscreen mode

然后统计箱子 ID 对的字母之间的差异:

```
diffs = 0
for i, letters in enumerate(zip(id_1, id_2)):
    if letters[0] != letters[1]:
        diffs +=1 
```

Enter fullscreen mode Exit fullscreen mode

当`diffs`等于 1:
时，可以通过中断循环找到正确的盒子 id

```
for id_1, id_2 in product(box_ids, box_ids):
    diffs = 0
    for i, chars in enumerate(zip(id_1, id_2)):
        if chars[0] != chars[1]:
            # we know these aren't the correct box IDs -> stop searching early
            if diffs > 1:
                break
            else:
                diffs += 1
    if diffs == 1:
        # id_1 and id_2 are correct
        break 
```

Enter fullscreen mode Exit fullscreen mode

由于两个正确的盒子 ID 之间只有一个字母不同，所以通过在不同字母所在的位置`i`分割 ID，并将剩余部分连接在一起:
，可以找到字母**是相同的**

```
id_1[i:] + id_2[i:] 
```

Enter fullscreen mode Exit fullscreen mode

综上所述:

```
def part_2(box_ids):
    for id_1, id_2 in product(box_ids, box_ids):
        diffs = 0
        for i, chars in enumerate(zip(id_1, id_2)):
            if chars[0] != chars[1]:
                if diffs > 1:
                    break
                else:
                    diffs += 1
        if diffs == 1:
            return id_1[:i] + id_2[i:] 
```

Enter fullscreen mode Exit fullscreen mode

对于我的字谜输入，结果是**" pbykrmmizwhxlqnasfgtycdv "**。

### 资源

*   [计时时间](https://docs.python.org/3/library/timeit.html)
*   [itertools](https://docs.python.org/3/library/itertools.html#itertools-recipes)
*   [大 O](https://en.wikipedia.org/wiki/Big_O_notation)*