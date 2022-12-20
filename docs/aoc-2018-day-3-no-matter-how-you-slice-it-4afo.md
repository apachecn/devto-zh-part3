# 《代码降临 2018》第三天:不管你怎么切片

> 原文：<https://dev.to/steadbytes/aoc-2018-day-3-no-matter-how-you-slice-it-4afo>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/advent-of-code-2018/03/)
> 
> 完整的解决方案可以在 [GitHub](https://github.com/SteadBytes/advent-of-code-2018/blob/2c9fe3ab312bdd15b23e1657edd98d3bd1fef8e5/03/solution.py) 上找到

## 第一部分

给定一个**索赔**的[字谜输入](https://github.com/SteadBytes/advent-of-code-2018/blob/2c9fe3ab312bdd15b23e1657edd98d3bd1fef8e5/03/input.txt)，我们被要求找出两个或更多索赔内的**织物平方英寸数。**

1.  将输入解析为一系列结构声明
2.  对于每平方英寸的织物，找出包含它的索赔数量
3.  当索赔数大于 1 时，计算织物的总平方英寸数

### 解析输入

每行输入代表以下格式的索赔:

```
#<ID> @ <left>,<top>: <width>x<height> 
```

Enter fullscreen mode Exit fullscreen mode

例如，`#123 @ 3,2: 5x4`对应于一个矩形，ID 为`123`，`3`，距离左边缘，`2`英寸，宽度为`5`，高度为`4`英寸。

1.  在新行上拆分输入
2.  对于每一行，提取索赔数据:
    *   身份证明
    *   距左边缘的距离
    *   距顶边的距离
    *   宽度
    *   高度

提取索赔数据可以使用一个[正则表达式](https://en.wikipedia.org/wiki/Regular_expression) :
来实现

```
import re

claim_re = re.compile(
    r"#(?P<_id>[0-9]+) @ "
    r"(?P<left>[0-9]+),(?P<top>[0-9]+): "
    r"(?P<width>[0-9]+)x(?P<height>[0-9]+)"
)

m = claim_re.match("#123 @ 3,2: 5x4")
# retrieve all cptured values m.groups() # -> ('123', '3', '2', '5', '4')
# retreieve a specific captured value m.groups('_id') # -> '123' 
```

Enter fullscreen mode Exit fullscreen mode

*   Python [命名的捕获组](https://www.regular-expressions.info/named.html)用于使提取的数据更加清晰。
*   [该正则表达式的交互演示和解释](https://regex101.com/r/MKLnmx/1)

从正则表达式中提取的值是字符串，但是对于这个谜题，它们表示整数。此外，一旦提取了值，就不再需要正则表达式匹配对象，声明可以由它自己的对象来表示。因为这是 Python，所以用一个`dict`就可以了！

给定来自难题输入的声明:

1.  使用`claim_re`正则表达式匹配每个值
2.  创建一个`dict`,其中键是组的名称，值是解析为整数的提取字符串:

```
def parse_claim(claim_string):
    m = claim_re.match(claim_string)
    return {k: int(v) for k, v in m.groupdict().items()}

parse_claim("#123 @ 3,2: 5x4")
# {'_id': 123, 'left': 3, 'top': 2, 'width': 5, 'height': 4} 
```

Enter fullscreen mode Exit fullscreen mode

### 代表织物

对于这个难题，织物可以被认为是网格，其中每个坐标代表一平方英寸的织物，并包含一个整数，表示对它的要求数量。给定上例中的单个声明:

```
00000000000
00000000000
00011111000
00011111000
00011111000
00011111000
00000000000
00000000000
00000000000 
```

Enter fullscreen mode Exit fullscreen mode

对于每项索赔:

1.  找到它将覆盖的“物理”区域(网格中的坐标)
2.  增加结构中相应的索赔计数

使用从难题输入:
中提取的位置(左、上)和尺寸(宽、高)可以找到由权利要求表示的区域

```
def claim_area(claim):
    return (
        (i, j)
        for i in range(claim["left"], claim["left"] + claim["width"])
        for j in range(claim["top"], claim["top"] + claim["height"])
    ) 
```

Enter fullscreen mode Exit fullscreen mode

该函数获取由之前的`parse_claim`函数生成的声明`dict`，并返回一个[生成器](https://wiki.python.org/moin/Generators)，该生成器生成包含在声明中的结构网格中的`(x, y)`坐标对。

表示织物网格的最直观的方式是使用 2D 数组结构:

```
fabric = []
for y in range(height_of_fabric):
    row = []
    for x in range(width_of_fabric):
        row.append(0)
    fabric.append(row)

# to update the fabric counters for a claim for x, y in claim_area("#123 @ 3,2: 5x4"):
    fabric[y][x] += 1 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们实际上并不需要整个织物的结构所代表的信息——只需要每平方英寸上存在的索赔数量。此外，我们实际上并不知道用作阵列结构限制的结构的确切大小。这个问题只给出了一个下限:

> 他们正在制作的整块布料是一个非常大的正方形——每边至少有`1000`英寸。

为了避免构建和迭代大型 2D 数组的不必要的开销，我们可以使用一个字典，其中的键是坐标，值是该位置上声明的数量:

```
fabric =  {}

# to add a claim for coord in claim_area("#123 @ 3,2: 5x4"):
    if coord in fabric:
        fabric[coord] += 1
    else:
        fabric[coord] = 1

fabric[(3, 2)] # -> 1 
```

Enter fullscreen mode Exit fullscreen mode

通过这一改进，我们不再存储结构中没有声明的位置值。在这样的字典中计算值是一个常见的操作，因此，Python 在[集合](https://docs.python.org/3/library/collections.html#collections.Counter)模块中包含了一个内置的`Counter`数据结构。给定一个包含从难题输入中解析的每一个声明的列表`claims`，整个 fabric 可以表示为:

```
fabric = Counter(coord for claim in claims for coord in claim_area(claim))

fabric[(3, 2)] # -> number of claims at position (3, 2) 
```

Enter fullscreen mode Exit fullscreen mode

### 寻找重叠的权利要求

用我们的`fabric`找到两个或更多索赔中的平方英寸数需要计算`fabric`中大于 1 的值的总数:

```
def part_1(fabric):
    return sum([1 for claim_count in fabric.values() if claim_count > 1]) 
```

Enter fullscreen mode Exit fullscreen mode

对于我的谜题输入，结果是 **115348** 。

## 第二部分

接下来，我们被告知有一个**单个**索赔与任何其他索赔都没有重叠，并被要求找到所述索赔的 **ID** 。

对于`fabric`中包含的每个坐标，该声明将有一个计数器值`1`。

1.  重复每个声明
2.  从声明中每个坐标的`fabric`中检索计数器值
3.  如果每个值都等于`1`，则返回索赔的 ID

```
def part_2(claims, fabric):
    for claim in claims:
        # all returns True if and only if each value is True
        if all(fabric[coord] == 1 for coord in claim_area(claim)):
            return claim["_id"] 
```

Enter fullscreen mode Exit fullscreen mode

对于我的谜题输入，结果是 **ID 188** 。

### 资源

*   [命名为捕获组的正则表达式](https://www.regular-expressions.info/named.html)
*   [regex101](https://regex101.com/)
*   [收藏。计数器](https://docs.python.org/3/library/collections.html#collections.Counter)