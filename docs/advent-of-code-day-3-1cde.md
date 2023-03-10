# 代码日 3 的到来

> 原文：<https://dev.to/captainsafia/advent-of-code-day-3-1cde>

又是一天，代码挑战的又一次来临！像往常一样，你可以在这里阅读今天挑战的问题陈述。

我正在用不同的编程语言解决这些问题。我已经用过 Python 和 JavaScript，想知道下一步可以用什么语言。时间有点晚了，因为我正在做这个，我已经工作了一整天，没有精神去尝试一门新的语言。所以对于这个挑战，我打破常规，使用我在这些挑战中已经使用过的编程语言:Python。

因此，在这个挑战中，我们想要确定给定位置和维度列表的重叠任务的区域。在这里，我想做的第一件事是编写一个函数，确定一个矩形范围内的所有子坐标。

```
def increments(claim):
    return [
        (x, y)
        for x in range(claim["x"], claim["x"] + claim["width"])
        for y in range(claim["y"], claim["y"] + claim["height"])
    ] 
```

我需要做的下一件事是解析输入文件。虽然我通常远离它，但我决定使用正则表达式来解析输入。我将一些数据复制粘贴到 [Regexr](https://regexr.com) 中，并处理这些值，直到我最终得到一个正则表达式来解析这些数据。最后，我设置了一个迭代器来遍历矩形内的每一英寸增量，并将重叠区域的值增加 1。一英寸一英寸地遍历这些区域使我们能够只对涉及多个矩形的重叠进行一次计数。

对于难题的第二部分，我需要确定不与任何其他声明共享一个区域的声明的 ID。我这样做是通过搜索每一个索赔的每一英寸，寻找没有共享的区域。这是最终的结果。

```
import re

def increments(claim):
    return [
        (x, y)
        for x in range(claim["x"], claim["x"] + claim["width"])
        for y in range(claim["y"], claim["y"] + claim["height"])
    ]

with open("areas.txt") as claims:
    regex = "^#(\d+)\s@\s(\d+),(\d+):\s(\d+)x(\d+)"
    parsed_claims = []
    overlap = 0
    for claim in claims.readlines():
        m = re.search(regex, claim)
        parsed_claims.append(
            {
                "id": m.group(1),
                "x": int(m.group(2)),
                "y": int(m.group(3)),
                "width": int(m.group(4)),
                "height": int(m.group(5)),
            }
        )
    overlaps = {}
    for claim in parsed_claims:
        for increment in increments(claim):
            if increment in overlaps:
                overlaps[increment] += 1
            else:
                overlaps[increment] = 1
    overlapped_area = 0
    for overlap in overlaps.values():
        if overlap > 1:
            overlapped_area += 1
    print(overlapped_area)

    for claim in parsed_claims:
        if all(overlaps[increment] == 1 for increment in increments(claim)):
            print(claim) 
```