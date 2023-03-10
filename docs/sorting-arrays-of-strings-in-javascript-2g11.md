# JavaScript 排序数组对 JavaScript 中的字符串数组进行排序

> 原文：<https://dev.to/banesag/sorting-arrays-of-strings-in-javascript-2g11>

[![alt text](img/09e3513697006e645a6b6cf0593c2e05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EbBBF847--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.techagilist.com/wp-content/uploads/2018/06/Sort.png)

### 对字符串数组排序

在 JavaScript 中，数组有一个 **sort( )** 方法，将数组项按字母顺序排序。

下面说明了 **sort( )** 方法的语法:

```
Array.sort([comparer]) 
```

**sort( )** 方法接受一个可选参数，该参数是一个比较数组中两个元素的函数。

如果省略了 compare 函数，那么 **sort( )** 方法将根据元素值对元素进行排序。

元素值规则:

1.如果 **compare (a，b)** 小于零，则 **sort( )** 方法将 **a** 排序到比 **b** 更低的索引。换句话说， **a** 会先来。
2。如果 **compare (a，b)** 大于零，则 **sort( )** 方法将 **b** 排序到比 **a** 更低的索引，即 **b** 将首先出现。
3。如果 **compare (a，b)** 返回零，则 **sort ( )** 方法认为 a 等于 b，并保持它们的位置不变。

另外，请注意， **sort( )** 方法接受两个参数，并将返回一个确定排序顺序的值，下面是语法:

```
function compare (a, b) {
   / …
} 
```

***注意:记住一个函数可以随意命名，但要尽量使名字对你想通过那个函数说的话有意义，以便于参考。*T3】**

**字符串数组排序**
让我们用一个名为**动物**的字符串数组来练习，如下:

```
var animals = [
    'cat', 'dog', 'elephant', 'bee', 'ant'
]; 
```

要按字母顺序对 **animals** 数组的元素进行升序排序，我们需要使用 **sort( )** 方法，而不像示例中那样传递比较函数:

```
animals.sort();
console.log(animals);
// ["ant", "bee", "cat", "dog", "elephant"] 
```

要按降序对 **animals** 数组进行排序，您需要更改比较函数的逻辑，并将其传递给 **sort( )** 方法，如下例所示。

```
// descending order
animals.sort(function (a, b) {
    if (a > b) {
        return -1;
    }
    if (b > a) {
        return 1;
    }
    return 0;
});
console.log(animals);
// ["elephant", "dog", "cat", "bee", "ant"] 
```

当然，这只是排序数组元素的一小部分，因为我们还可以使用 **sort( )** 方法按照大小写、数字和对象的属性进行排序。