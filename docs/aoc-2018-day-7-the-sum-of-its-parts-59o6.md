# 代码 2018 第 7 天的来临:其部分的总和

> 原文：<https://dev.to/steadbytes/aoc-2018-day-7-the-sum-of-its-parts-59o6>

> 本帖原载于[steadbytes.com](https://steadbytes.com/blog/advent-of-code-2018/07/)T2【完整解决方案见 [GitHub](https://github.com/SteadBytes/advent-of-code-2018/blob/3ce928637a401a5180a5773c8087c1e884bb6fbf/07/solution.py)

## 第一部分

给定一个包含一组建造圣诞老人雪橇的指令的[谜题输入](https://github.com/SteadBytes/advent-of-code-2018/blob/4f27d44f7003f04dcccd04b5471b96f19751b1f0/07/input.txt)，我们被要求确定指令中的步骤应该完成的**顺序**。

每个*指令*定义了一对*步骤* :
之间的顺序依赖关系

```
Step C must be finished before step A can begin. 
```

Enter fullscreen mode Exit fullscreen mode

重要规则:

*   如果一个步骤的所有先决步骤都已完成，则该步骤*可用*
*   如果有多个*可用的*步骤，则按照**字母顺序**完成

整体算法:

1.  从难题输入中提取步骤对，以获得步骤依赖性列表
2.  虽然还有未完成的步骤:
    *   从步骤相关性列表中查找下一个可用步骤
    *   从未完成的集合中删除步骤
    *   将步骤添加到已完成步骤的列表中
    *   移除需要完成步骤的所有步骤依赖项
3.  返回已完成步骤的列表

### 解析输入

为了从一行字谜输入中提取成对的步骤，我将使用一个可靠的正则表达式:

```
>>> import re
>>> line = "Step C must be finished before step A can begin."
>>> re.findall(r"step (\w)", line, re.IGNORECASE)
['C', 'A'] 
```

Enter fullscreen mode Exit fullscreen mode

为了匹配`Step X`和`step Y`，标志`re.IGNORECASE`很重要。

*   [这个正则表达式的演示](https://regex101.com/r/hNe6Qn/1/)

将整个难题输入解析成步骤依赖列表，其中每对中的第一步是第二步的先决条件:

```
import re

with open("input.txt") as f:
    prereq_step_pairs = [re.findall(r"step (\w) ", l, re.IGNORECASE) for l in f] 
```

Enter fullscreen mode Exit fullscreen mode

这可用于获得所有步骤的集合:

```
steps = {s for steps in prereq_step_pairs for s in steps} 
```

Enter fullscreen mode Exit fullscreen mode

### 寻找下一个可用的步骤

如果一个步骤的所有先决步骤都已完成，则该步骤*可用*。给定一个集合(`steps`)和一个剩余步骤依赖列表(`prereq_step_pairs`)，可以通过将步骤集合过滤为那些在`prereq_step_pairs`中没有任何先决条件的步骤来找到可用的步骤。这些必须按字母顺序完成，所以下一步是返回列表中的第一个元素*:* 

```
def find_next_steps(steps, prereq_step_pairs):
    next_steps = sorted(
        [step for step in steps if all(s != step for prereq, s in prereq_step_pairs)]
    )
    return next_steps 
```

Enter fullscreen mode Exit fullscreen mode

### 主算法

现在可以实现算法的其余部分:

```
step_order = []
while steps:
    next_step = find_next_steps(steps, prereq_step_pairs)[0]
    step_order.append(next_step)
    steps.remove(next_step)
    # remove dependencies fulfilled by completing the step
    prereq_step_pairs = [
        (prereq, s) for (prereq, s) in prereq_step_pairs if prereq != next_step
    ]

answer = "".join(step_order) 
```

Enter fullscreen mode Exit fullscreen mode

对于我的字谜输入，答案是**GRTAHKLQVYWXMUBCZPIJFEDNSO**。

## 第二部分

正如经常发生的情况一样，我们必须面对现实，即[时间](https://en.wikipedia.org/wiki/Time)。此外，精灵[多处理](https://en.wikipedia.org/wiki/Multiprocessing)被扔进了 mix！我们被要求找出完成所有步骤需要多长时间，给定 **5** 个精灵同时工作并且每一步持续时间。

在代码时尚的典型出现中，这个问题很好地呈现给了一个*模拟*:

1.  从一组 5 个 elf 工人开始(不分配任何任务)
2.  当未完成的步骤还剩下**和**时，一些精灵还在工作:
    *   对于每个工人:
        *   减少他们当前任务的剩余时间
        *   如果任务现在已完成:
        *   移除需要完成步骤的所有步骤依赖项
        *   将工作线程设置为空闲
        *   查找下一个可用步骤
        *   计算下一可用步骤的持续时间
        *   将步骤分配给员工
        *   从要完成的步骤集中删除步骤
    *   增量时间步长

### 计算步长持续时间

该谜题陈述了每一步花费 **60 秒加上对应于其字母**(字母表中的位置)的量。为了确定字母在字母表中的**零索引**位置，从字母“A”的值中减去其 [unicode](https://en.wikipedia.org/wiki/Unicode) 码位的值。这是因为字母表中每个字母的代码点增加 1:

```
>>> ord("A")
65
>>> ord("B")
66
...
>>> ord("B") - ord("A")
1 
```

Enter fullscreen mode Exit fullscreen mode

然后可以计算一个步骤的持续时间:

```
def calculate_step_time(s):
    return (ord(s) - ord("A") + 1) + 60 
```

Enter fullscreen mode Exit fullscreen mode

*   注意`+ 1`，因为问题中的字母值是 1 索引的

### 工人

每个工人都是他们当前步骤和该步骤剩余时间的字典:

```
workers = [{"step": None, "time": 0} for _ in range(5)] 
```

Enter fullscreen mode Exit fullscreen mode

### 主算法

获取步骤依赖关系和所有步骤的集合与第 1 部分相同。然后，模拟在时间`-1`开始，答案是模拟(`while`循环)完成后的时间值:

```
time = -1
workers = [{"step": None, "time": 0} for _ in range(5)]
while steps or any(w["time"] > 0 for w in workers):
    for w in workers:
        # decrement remaining time of current task
        w["time"] = max(w["time"] - 1, 0)

        # current task complete
        if w["time"] == 0:
            # task is assigned
            if w["step"] is not None:
                # remove all step dependencies which require the completed step
                prereq_step_pairs = [
                    (prereq, s)
                    for (prereq, s) in prereq_step_pairs
                    if prereq != w["step"]
                ]
                # set worker to idle
                w["step"] = None
            next_steps = find_next_steps(steps, prereq_step_pairs)
            if next_steps:
                # get next step and assign to worker
                step = next_steps.pop()
                w["time"] = calculate_step_time(step)
                w["step"] = step
                steps.remove(step)
    # move along the wheel of time
    time += 1 
```

Enter fullscreen mode Exit fullscreen mode

对于我的谜题输入，结果是 **1115** 。

### 资源

*   [Unicode](https://en.wikipedia.org/wiki/Unicode)
*   [Python `ord`函数](https://docs.python.org/3.6/library/functions.html#ord)