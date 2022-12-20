# 意外声明

> 原文：<https://dev.to/dexygen/accidentally-declarative-g37>

#### 一种将输入字段值映射到 JSON 从 JSON 映射的方法

“编程范式是一种根据编程语言的特征对其进行分类的方法。维基百科称，语言可以分为多种范式。同一篇文章接着列出了显然被认为是两种最常见的范例:

1.  命令式(“程序员指示机器如何改变其状态”)
2.  声明性的(“程序员只声明期望结果的属性，而不声明如何计算它”)

从这篇文章的标题，你能猜到我会关注哪一个吗？)在任何情况下，在上面对声明式编程的描述中都隐含了一些东西:程序员可能没有指定如何计算结果，但它*仍然是*计算出来的。

用于与数据库交互的 SQL 是声明式编程语言的一个典型例子——例如，在这篇[维基百科文章](https://en.wikipedia.org/wiki/Declarative_programming)中，它被作为第一个例子提到。考虑以下 SQL“Select”语句:

```
SELECT foo FROM bar 
```

这声明了期望的结果是来自表“bar”的所有“foo”值，但是没有指定如何计算该结果。这是数据库引擎的工作，无论是 Oracle、SQL Server 还是 MySQL 等等。

2015 年末，我的工作是创建一个表单，包含大约 100 个输入字段，这些字段都对应于一个 JSON 对象中的节点。我知道我不想编写如下代码来管理它们。

从 JSON 填充:

```
field001.value = json.obj.field001;
field002.value = json.obj.field002;
field003.value = json.obj.field003;
// A hundred times over, ugh :( 
```

保存到 JSON:

```
json.obj.field001 = field001.value;
json.obj.field002 = field002.value;
json.obj.field003 = field003.value;
// Another hundred times over, double ugh 
```

不用说，上述内容不一定是 JSON 的特别准确的表示。更确切地说，这是一个图表包，下面是从他们的网页中摘录的一段:

```
{
    "chart":{
            "caption":"Average Monthly Temperature in Texas",
            "yAxisName":"Average Monthly Temperature"
    },
    "annotation":{
            "group":{
                "id":"anchor-highlight",
                "item":{
                        "id":"high-star",
                        "type":"circle"
                }
            }
    }
} 
```

如上所述，大约有一百个这样的节点。在意识到接下来的一两天里，我想到了一个“淋浴的想法”(如果你愿意的话)，这与托马斯·爱迪生的名言相反:

"天才是百分之一的灵感加上百分之九十九的汗水。"

我的方法不仅主要来自灵感，而且事实证明，如果我不得不多写 200 行左右的代码，我会少流汗很多。有趣的是，仅仅三年后，我才意识到我的方法是声明式的。

具体来说，我所做的是将 json 节点的路径作为现有的 100 行表单输入字段 HTML 代码的数据属性嵌入。以下是几个例子:

```
<input data-json-node="chart.caption">
<input data-json-node="annotation.group.id">
<input data-json-node="annotation.group.item.type"> 
```

上面所做的是，对于每个输入字段，*声明*它映射到哪个 JSON 节点，而不执行实际的计算。也许我没有意识到我的方法是声明式的，因为实际上我*不得不*编写计算代码。我担任的是全栈角色，这与我曾经担任的其他类似职位没有什么不同，在这些职位上，我不仅编写了用于提供 RESTful 服务的中间层/后台代码，还编写了用于消费这些服务的前端代码。

总之，我写了两个小函数，加起来可能有 25-30 行，通过将数据属性拆分成点来解析它们；一个函数用于从 JSON 填充输入字段值，另一个函数用于将这些值保存到 JSON。我本来可以用`eval`来代替，但我不想和几乎所有人的误解作斗争，即*总是*邪恶。

今天给我同样的任务，我可能会依赖两个“lodash”库的方法，`get`和`set`，但是我当时并不熟悉那个库(而是“下划线”)。这三种执行计算的方法(mine、`eval`、lodash 方法)是声明性方法的指示和对应——只要结果是正确的，实现就没有区别。“偶然声明”没问题，但不要偶然不正确。