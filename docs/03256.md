# 代码 2018 第 6 天的来临:历史坐标

> 原文：<https://dev.to/steadbytes/aoc-2018-day-6-chronal-coordinates-288d>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/advent-of-code-2018/05/)
> 
> 完整的解决方案可以在 [GitHub](https://github.com/SteadBytes/advent-of-code-2018/blob/fa4c0cc437a942ad8cdeb727a4bc8264cd15e419/06/solution.py) 上找到

## 第一部分

我们被要求从[字谜输入](https://github.com/SteadBytes/advent-of-code-2018/blob/fa4c0cc437a942ad8cdeb727a4bc8264cd15e419/06/input.txt)中的坐标列表中找出一个坐标的**最大有限区域**周围区域。

### 解析输入

输入的每一行包含一对由`,` :
分隔的`X,Y`坐标

```
1, 1
1, 6
8, 3
3, 4
5, 5
8, 9 
```

Enter fullscreen mode Exit fullscreen mode

获取一列*整数的*坐标:

1.  初始化一个`list`来存储坐标
2.  对于每一行输入:
    *   分割`,`上的线
    *   将每个分割的项目投射到一个`int`
    *   将整数坐标的`tuple`追加到列表中

```
with open("input.txt") as f:
    coords = []
    for line in f.readlines():
        x, y = line.split(", ")
        coords.append((int(x), int(y))) 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但可以清理，并使用列表理解
制作更多的*python 式的*

```
with open("input.txt") as f:
    coords = [tuple(int(s) for s in line.split(", ")) for line in f.readlines()] 
```

Enter fullscreen mode Exit fullscreen mode

### 计算坐标面积

从这里开始，术语*坐标*将指从难题输入中提取的 X，Y 位置之一，而*位置*将指搜索网格中的任何 X，Y 位置。

这个难题包括一些重要的约束:

*   计算必须使用[曼哈顿距离](https://en.wikipedia.org/wiki/Taxicab_geometry)
*   坐标周围的*区域*是距离**最近**的**整数** X，Y 位置的数量
    *   x，Y 位置不能与另一个坐标的*等于*距离
*   期望的坐标必须有一个有限的面积
    *   即该坐标的所有边都被另一个坐标包围

1.  找出坐标的最小/最大 X，Y 极限
2.  在这些限制内，生成所有可能的整数 X，Y 位置
3.  计算从每个坐标到每个位置的曼哈顿距离
4.  对于每个位置，确定最近的坐标
5.  对于每个坐标，通过合计最接近的位置数来计算面积
6.  返回最大的区域

寻找坐标的极限是寻找难题输入的 X，Y 分量的最小值和最大值的情况:

```
def coords_limits(coords):
    min_x = min(coords, key=lambda c: c[0])[0]
    max_x = max(coords, key=lambda c: c[0])[0]
    min_y = min(coords, key=lambda c: c[1])[1]
    max_y = max(coords, key=lambda c: c[1])[1]

    return ((min_x, max_x), (min_y, max_y))

coords_limits(
    [(1, 1), (1, 6), (8, 3), (3, 4), (5, 5), (8, 9)]
)
# ((1, 8), (1, 9)) 
```

Enter fullscreen mode Exit fullscreen mode

现在，每组极限之间的嵌套循环可用于枚举所有可能的位置:

```
def gen_locations(x_lims, y_lims):
    for x in range(x_lims[0], x_lims[1] + 1):
        for y in range(y_lims[0], y_lims[1] + 1):
            yield (x, y) 
```

Enter fullscreen mode Exit fullscreen mode

注意，我在这里使用了一个[生成器](https://wiki.python.org/moin/Generators)进行惰性评估。这意味着将根据需要计算每个位置，而不是一次构建整个列表。这样做的好处将在接下来的步骤中变得显而易见。

为了计算坐标和位置之间的距离，我将首先为每个组合生成一个`(<location>, <coord>)`元组:

```
def gen_locations_coords(coords):
    x_lims, y_lims = coords_limits(coords)
    for x, y in gen_locations(x_lims, y_lims):
        for cx, cy in coords:
            yield ((x, y), (cx, cy)) 
```

Enter fullscreen mode Exit fullscreen mode

然后计算每一个的曼哈顿距离，生成`((<location>, <cood>), <distance>)` :
的元组

```
def manhattan_distance(x1, y1, x2, y2):
    return abs(x1 - x2) + abs(y1 - y2)

def gen_locations_coords_dists(coords):
    for (x, y), (cx, cy) in gen_locations_coords(coords):
        d = manhattan_distance(cx, cy, x, y)
        yield ((x, y), (cx, cy), d) 
```

Enter fullscreen mode Exit fullscreen mode

在这里使用生成器是非常强大的，它允许将迭代逻辑的“链”分解到多个函数上，而无需构建和传递大型数据结构(在本例中是一个位置列表)。

为了确定离每个位置最近的坐标，我迭代通过`gen_locations_coords_dists`更新具有最短距离的配对之间的映射而产生的位置、坐标和距离的组合:

```
# location: closest coordinate closest_coord_map = {}
# location: shortest distance closest_coord_dists = defaultdict(lambda: float("inf"))
for (x, y), (cx, cy), d in gen_locations_coords_dists(coords):
    # shortest distance seen so far
    closest = closest_coord_dists[(x, y)]
    if d < closest:
        # current pairing is shorter
        closest_coord_map[(x, y)] = (cx, cy)
        closest_coord_dists[(x, y)] = d
    elif d == closest and closest_coord_map[(x, y)] != (cx, cy):
        # same distance, not the same X,Y values
        # locations with multiple equal distance coords don't count towards area
        closest_coord_map[(x, y)] = None 
```

Enter fullscreen mode Exit fullscreen mode

使用这个`closest_coord_map`，通过计算每个坐标最近的位置的数量，可以计算每个坐标的面积:

```
x_lims, y_lims = coords_limits(coords)
coords_areas = defaultdict(int)
for xy, coord in closest_coord_map.items():
    if coord is None:
        # location was equal distance to coordinates
        continue
    if xy[0] in x_lims or xy[1] in y_lims:
        # location at edge of coordinate limits
        # must have an infinite area
        coords_areas[coord] = float("-inf")
    coords_areas[coord] += 1
largest_area = max(coords_areas.values()) 
```

Enter fullscreen mode Exit fullscreen mode

这最后两步可以总结成第 1 部分的一个函数:

```
def part_1(coords):
    closest_coord_map = {}
    closest_coord_dists = defaultdict(lambda: float("inf"))
    for (x, y), (cx, cy), d in gen_locations_coords_dists(coords):
        closest = closest_coord_dists[(x, y)]
        if d < closest:
            closest_coord_map[(x, y)] = (cx, cy)
            closest_coord_dists[(x, y)] = d
        elif d == closest and closest_coord_map[(x, y)] != (cx, cy):
            closest_coord_map[(x, y)] = None

    x_lims, y_lims = coords_limits(coords)
    coords_areas = defaultdict(int)
    for xy, coord in closest_coord_map.items():
        if coord is None:
            continue
        if xy[0] in x_lims or xy[1] in y_lims:
            coords_areas[coord] = float("-inf")
        coords_areas[coord] += 1
    return max(coords_areas.values()) 
```

Enter fullscreen mode Exit fullscreen mode

对于我的谜题输入，结果是 **3358** 。

## 第二部分

现在要求我们找出包含所有位置的区域的* *大小，这些位置到所有给定坐标的总距离小于 10000`。这个难题实际上为我们提供了一个寻找所需区域的算法(使用较小的 32 号):

> 对于每个位置，将到所有给定坐标的距离相加；如果这些距离的总和小于 32，则该位置在期望的区域内。

所有的艰苦工作都在第一部分完成了，我们已经可以生成所有位置和所有坐标之间的距离。最后一步是合计每个位置的距离，以找到该区域中所有坐标的总距离，过滤这些总距离小于 10000 的位置，并对它们进行计数！

``python
def part_2(coords):
total_dists_to_coords = defaultdict(int)
for (x, y), (cx, cy), d in gen_locations_coords_dists(coords):
# calculate region size for each location
total_dists_to_coords[(x, y)] += d
# filter and count
return len([l for l, d in total_dists_to_coords.items() if d < 10000])`` 

 ``对于我的难题输入，结果是 **45909**

## 关于懒评的一个注记

这个谜题非常适合生成器和懒惰评估。它提供了一个非常有趣的编程范例，对于大型数据集非常有用(当然也很有趣)。我强烈建议阅读[计算机程序的结构和解释](https://sarabander.github.io/sicp/html/3_002e5.xhtml)关于流的第 3.5 章，以获得关于这个主题的精彩介绍。

### 资源

*   [曼哈顿距离](https://en.wikipedia.org/wiki/Taxicab_geometry)
*   [发电机](https://wiki.python.org/moin/Generators)
*   [T2`defaultdict`](https://docs.python.org/3/library/collections.html#collections.defaultdict)
*   [计算机程序的结构和解释](https://sarabander.github.io/sicp/html/3_002e5.xhtml)关于流的第 3.5 章。``