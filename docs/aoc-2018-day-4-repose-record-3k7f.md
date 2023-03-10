# 代码 2018 第四天来临:安息记录

> 原文：<https://dev.to/steadbytes/aoc-2018-day-4-repose-record-3k7f>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/advent-of-code-2018/04/)
> 
> 完整的解决方案可以在 [GitHub](https://github.com/SteadBytes/advent-of-code-2018/blob/47ca570c8ea9363042d6624c7b8d2164393726ab/04/solution.py) 上找到

## 第一部分

[谜题输入](https://github.com/SteadBytes/advent-of-code-2018/blob/47ca570c8ea9363042d6624c7b8d2164393726ab/04/input.txt)包含每个警卫在*午夜*轮班期间的睡眠模式记录。我们被要求找出睡得最多的警卫的 ID，警卫在一小时的哪一分钟(0- > 59)睡着，并提交这些值的乘积作为答案。

我们被告知，记录是按时间顺序排列的*而不是*。然而，在排序之后，对于每个守卫，输入具有记录块的重复结构:

```
[<timestamp>] Guard <ID> begins shift
<---------repeat------------>
[<timestamp>] falls asleep
[<timestamp>] wakes up
<--------------------------> 
```

Enter fullscreen mode Exit fullscreen mode

利用这一点，我们获得了解决方案的以下步骤:

1.  按时间顺序排列记录
2.  迭代每条记录
3.  每次遇到“开始轮班”记录时:
    *   提取警卫 ID
    *   对于每对后续的“入睡”、“醒来”记录:
        *   从时间戳中提取分钟值
        *   将当前防护的计数器增加两个值之间的总分钟数
        *   在两分钟值范围内，每一分钟的当前保护值增加一个计数器
4.  找到具有最大总睡眠分钟数计数器的警卫 ID
5.  找到那个守卫的最大分钟数
6.  返回 ID 和分钟的乘积

### 解析输入

我们上面的解决方案需要按时间排序的记录。由于几个关键属性，这听起来比实际更复杂:

*   每个记录*以时间戳开始*
*   时间戳*是唯一的*
*   时间戳遵循相同的`[YYYY-MM-DD HH:MM]`格式

这意味着在这种情况下，时间顺序与词典顺序相同。在 python 中，对字符串列表调用`list.sort()`将实现这一点。因此，首先解析我们的输入:

1.  将新行上的字谜输入拆分成一个字符串列表
2.  按字典顺序排列列表

```
with open("input.txt") as f:
    records = f.read().split("\n") # split into list of strings
    records.sort() # sort lexicographically 
```

Enter fullscreen mode Exit fullscreen mode

### 数着分钟入睡

首先，我们需要识别记录的每种“类型”:“开始轮班”、“入睡”和“醒来”:

```
if "begins shift" in record:
    # extract guard ID elif "falls asleep" in record:
    # extract timestamp etc elif "wakes up" in record:
    # extract timestamp etc 
```

Enter fullscreen mode Exit fullscreen mode

#### 提取警卫 ID

一旦识别出“开始轮班”记录，我们就可以使用正则表达式提取警卫的 ID:

```
import re

id_re = re.compile(r"#(\d+)")

def parse_id(record):
    m = id_re.search(record)
    guard_id = int(m.group(1))

parse_id("[1518-11-01 00:00] Guard #10 begins shift")
# 10 
```

Enter fullscreen mode Exit fullscreen mode

*   [该正则表达式的交互演示和解释](https://regex101.com/r/I9YpkR/1)

#### 从时间戳中提取分钟

每个时间戳都有一个`[YYYY-MM-DD HH:MM]`格式。我们只需要这个的`MM`(分钟)部分。很方便(我怀疑是故意的)，在分钟和时间戳的其余部分之间有一个单独的`:`分隔符，还有一个单独的`]`表示时间戳的结束。因此，为了提取会议记录，我们需要在一个`]`上拆分记录，以获得时间戳和动作(“睡着了”等)，然后在`:`字符上拆分时间戳:

```
def parse_minute(record):
    timestamp, action = record.split("] ")
    minute = int(timestamp.split(":")[1]

parse_minute("[1518-11-01 00:05] falls asleep")
# 5 
```

Enter fullscreen mode Exit fullscreen mode

#### 实现算法

现在我们可以从记录中检索所需的信息，是时候实现前面概述的算法了。第一部分涉及构建两种数据结构:

1.  *每个警卫睡眠的总分钟数*
2.  在特定的分钟值(0->59)内，每个警卫睡觉的次数

```
from collections import defaultdict

# {guard_id: {minute_value: count, ...}, ...} guard_minute_asleep_counts = defaultdict(lambda: defaultdict(int))
# {guard_id: total_minutes_asleep, ...} guards_asleep_totals = defaultdict(int)

current_guard = None
asleep_minute = None
for r in records:
    minute = parse_minute(r)
    if "begins shift" in r: # new guard begins shift
        current_guard = parse_id(r)
        asleep_minute = None
    elif "falls asleep" in r:
        asleep_minute = minute # start of a sleep cycle
    elif "wakes up" in r:
        for m in range(asleep_minute, minute): # end of a sleep cycle
            guard_minute_asleep_counts[current_guard][m] += 1
            guards_asleep_totals[current_guard] += 1 
```

Enter fullscreen mode Exit fullscreen mode

睡眠时间最长的守卫现在是与`guard_asleep_totals`字典中的最大值对应的守卫 ID:

```
most_asleep_guard = max(guards_asleep_totals, key=guards_asleep_totals.get) 
```

Enter fullscreen mode Exit fullscreen mode

该守卫最晚进入的分钟是与守卫
的`guard_asleep_minute_counts`的最大值相对应的分钟值

```
most_asleep_minute = max(
    guard_minute_asleep_counts[most_asleep_guard],
    key=guard_minute_asleep_counts[most_asleep_guard].get,
) 
```

Enter fullscreen mode Exit fullscreen mode

最后我们的答案简单来说就是这两个值的乘积:

```
answer = most_asleep_guard * most_asleep_minute 
```

Enter fullscreen mode Exit fullscreen mode

对于我的谜题输入，答案是 **151754** 。

## 第二部分

接下来，我们被要求找出在同一*分钟内最频繁睡眠的警卫，并再次提供警卫 ID 和分钟值的乘积。*

1.  按时间顺序排列记录
2.  迭代每条记录
3.  每次遇到“开始轮班”记录时:
    *   提取警卫 ID
    *   对于每对后续的“入睡”、“醒来”记录:
        *   从时间戳中提取分钟值
        *   在两分钟值范围内，每一分钟的当前保护值增加一个计数器
4.  找到具有最大分钟值计数器的防护 ID
5.  返回 ID 和分钟值的乘积

注意这个算法和第一部分有多相似？记录迭代和数据提取逻辑是完全相同的，本质上所有改变的是当发现“唤醒”记录时所采取的动作。你的[干燥的](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)感觉应该在这里刺痛，因为这看起来是一个提取一些重复逻辑的好机会。首先，我将展示算法实现，然后对其进行重构。

```
from collections import defaultdict

# {(guard_id, minute_value): count, ...} guard_minute_asleep_counts = defaultdict(int)

current_guard = None
asleep_minute = None
for r in records:
    minute = parse_minute(r)
    if "begins shift" in r: # new guard begins shift
        current_guard = parse_id(r)
        asleep_minute = None
    elif "falls asleep" in r:
        asleep_minute = minute # start of a sleep cycle
    elif "wakes up" in r:
        for m in range(asleep_minute, minute): # end of a sleep cycle
            guard_minute_asleep_counts[(current_guard, m)] += 1
guard_id, minute = max(
    guard_minute_asleep_counts, key=guard_minute_asleep_counts.get
)
answer = guard_id * minute 
```

Enter fullscreen mode Exit fullscreen mode

对于我的谜题输入，答案是 **819896** 。

## 干重构

在这两个部分之间，迭代记录和提取保护 id 和时间戳的逻辑是相同的。如前所述，唯一的*区别*是当发现“唤醒”记录时采取的动作:

```
# part 1 for m in range(asleep_minute, minute):
    guard_minute_asleep_counts[current_guard][m] += 1
    guards_asleep_totals[current_guard] += 1

# part 2 for m in range(asleep_minute, minute):
    guard_minute_asleep_counts[(current_guard, m)] += 1 
```

Enter fullscreen mode Exit fullscreen mode

两者都需要已经醒来的当前守卫的 ID 和守卫睡着的分钟范围，除此之外别无其他！为了重构这一点，我们可以使用一个[回调](https://en.wikipedia.org/wiki/Callback_(computer_programming))函数，当发现一个“唤醒”记录时执行该函数，该函数接收当前警卫 ID 和睡眠分钟范围作为参数:

```
def process_records(records, on_wake_up):
    current_guard = None
    asleep_minute = None
    for r in records:
        if not r:
            continue
        minute = parse_minute(r)
        if "begins shift" in r:
            current_guard = parse_id(r)
            asleep_minute = None
        elif "falls asleep" in r:
            asleep_minute = minute
        elif "wakes up" in r:
            on_wake_up(current_guard, range(asleep_minute, minute)) # callback 
```

Enter fullscreen mode Exit fullscreen mode

第一部分和第二部分都利用这个`process_records`函数，提供它们自己的回调来实现它们需要的逻辑:

```
def part_1(records):
    guard_minute_asleep_counts = defaultdict(lambda: defaultdict(int))
    guards_asleep_totals = defaultdict(int)

    # callback
    def on_wake_up(current_guard, asleep_minutes_range):
        for m in asleep_minutes_range:
            guard_minute_asleep_counts[current_guard][m] += 1
            guards_asleep_totals[current_guard] += 1

    process_records(records, on_wake_up)

    most_asleep_guard = max(guards_asleep_totals, key=guards_asleep_totals.get)
    most_asleep_minute = max(
        guard_minute_asleep_counts[most_asleep_guard],
        key=guard_minute_asleep_counts[most_asleep_guard].get,
    )
    return most_asleep_guard * most_asleep_minute

def part_2(records):
    guard_minute_asleep_counts = defaultdict(int)

    # callback
    def on_wake_up(current_guard, asleep_minutes_range):
        for m in asleep_minutes_range:
            guard_minute_asleep_counts[(current_guard, m)] += 1

    process_records(records, on_wake_up)

    guard_id, minute = max(
        guard_minute_asleep_counts, key=guard_minute_asleep_counts.get
    )
    return guard_id * minute 
```

Enter fullscreen mode Exit fullscreen mode

### 资源

*   [干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)
*   [回调](https://en.wikipedia.org/wiki/Callback_(computer_programming))
*   [Python 排序](https://wiki.python.org/moin/HowTo/Sorting)