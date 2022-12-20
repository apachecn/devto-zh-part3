# Javascript 中内置的数据结构

> 原文：<https://dev.to/kartik2406/built-in-data-structures-in-javascript-hhl>

[![Store](img/889bcdbad5395aefe8447d486f7d7e0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M93o10_E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bi827tz1qelwem6e8b0l.jpg)

在这篇文章中，我们将看看 JavaScript 中现成可用的不同数据结构(现成的，因为您不必自己编写这些数据结构)。我们还将了解一些有助于我们处理这些数据结构的方法。

JavaScript 内置了对以下数据结构的支持:

1.  排列
2.  一组
3.  地图

### 阵列

数组是项目的集合。在 JavaScript 中，可以有一个包含不同类型数据的数组。但是在实践中，您会遇到具有相同数据类型的数组。

创建数组有三种方法。

1.  定义一个变量并分配给它
2.  新数组([1....n])
3.  新数组(数组长度)

##### 阵列操作

*   从数组中访问元素:
    数组元素有一个索引，index 从 0 开始，到 array.length -1。若要访问任何特定索引处的元素，请使用以下语法 arrName[index]。

*   若要向现有数组中添加元素，请使用 array.push()。

*   若要查找数组中的元素数，请使用 array.length 属性。

*   若要检查元素是否存在，请使用 array.indexOf()或 array.includes()。

*   要从数组中删除一个元素，我们可以使用 array.splice()方法。如果你想删除最后一个元素你可以使用 array.pop()方法。
    Splice 修改现有数组，使用 array.splice(index，1)删除特定索引处的元素。

*   可以使用 array.concat()方法组合两个数组。

### 设定

和 Array 一样，set 也是一个项目的集合，不同之处在于 Set 中的值只出现一次。

您可以按如下方式创建器械包:

```
let set = new Set([1, 2, 3, 4, 5, 6, 6, 6]) 
```

集合将包含 1，2，3，4，5，6

##### 设定操作

*   若要获取集合中元素的数量，请使用集合的 size 属性(set.size)。
*   若要添加值，请使用 set.add()，返回集合。
*   若要移除元素，请使用 set.delete(value)，若要移除集合中的所有元素，请使用 set.clear()

当您需要一个唯一的项目集合时，Set 非常有用。如果您使用一个数组来完成相同的任务，您将需要额外的逻辑来消除重复项。

### 地图

是键值对的集合，那么它和对象一样吗？在最高层，它们看起来是一样的，但是差别很小。

*   一个对象的键只能是字符串，这不是地图的情况。

*   键按插入顺序存储在映射中。

*   您也可以使用 size 属性来获取地图的大小。

*   地图是可迭代的，所以你可以直接使用。地图上的每个人。对于对象，您必须首先获取键，以获取该键的值。

*   除此之外，Map prototype 还有一些辅助方法。
    例如，要检查一个键是否出现在地图中，你可以做
    map.has(key)。

您可以按如下方式创建地图:

```
let map = new Map([['name', 'John'], ['age', '21']]) 
```

##### 地图操作

*   若要获取地图中的元素数量，请使用 size 属性。
*   您不能像访问对象一样访问地图中的值。你需要使用。地图上的 get()方法。

要增加地图的价值，您需要使用。set()方法。

现在你已经知道了这些数据结构的基础，继续尝试，实现一些东西。

### 参考文献

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array/pop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Iteration _ protocols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)

如果你喜欢这篇文章，请分享它。