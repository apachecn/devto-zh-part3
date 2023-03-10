# 快速排序:JS 故障

> 原文：<https://dev.to/benweiser/quicksort-a-js-breakdown-lfg>

快速排序是一种递归排序算法，它使用分而治之的方法。从最简单的基础案例开始检查，我们继续将列表分解成更小的问题。平均而言，快速排序的执行速度为 O(n log n)。

这里是完整的算法，

```
const quickSort = values => {
  if (values.length <= 1) {
    return valuesToSort;
  }

  let lessThanPivot = [];
  let greaterThanPivot = [];
  const pivot = values.shift();

  for (let i = 0; i < values.length; i++) {
    const value = values[i];
    value <= pivot ? lessThanPivot.push(value) : greaterThanPivot.push(value);
  }

  return [...quickSort(lessThanPivot), pivot, ...quickSort(greaterThanPivot)];
}; 
```

让我们来分解一下，

```
 if (values.length <= 1) {
    return values;
  } 
```

这是我们的基本情况。当使用递归时，你必须有一个退出条件，这样你的函数就不会无限地运行，导致你耗尽内存。`valuesToSort`只是一个 JavaScript 整数数组。如果这个数组的长度小于等于 1，就没有什么可排序的了，所以我们通过返回值来退出函数。当这个函数运行时，我们的快速排序就完成了。

```
 let lessThanPivot = [];
  let greaterThanPivot = [];
  const pivot = values.shift(); 
```

这里我们创建了两个数组来保存小于或大于 pivot 值的值。可以从数组中的任何索引中选择一个透视。在我们的例子中，我们使用`shift()`方法来选择数组中的第一项。

```
 for (let i = 0; i < values.length; i++) {
    const value = values[i];
    value <= pivot ? lessThanPivot.push(value) : greaterThanPivot.push(value);
  } 
```

我们迭代数组的长度，并检查小于或等于主元的值。如果是 than，我们把它推到小于数组中(即使相等也是)。如果这个条件为假，那么我们将把它推到大于数组中，因为理性告诉我们它必须大于。

```
return [...quickSort(lessThanPivot), pivot, ...quickSort(greaterThanPivot)]; 
```

这是我们递归发生的地方。对于少于枢纽的数组，我们将再次调用 quicksort，直到不再有项目需要排序。对于大于枢纽的项目也是如此。通过每一次递归调用，我们返回一个新的数组，数组中的项目顺序正确。

用一组随机的数字试试看，

```
quickSort([50,34,21,2,3,5,7,9]) 
```

结果会是这样的，

```
[ 2, 3, 5, 7, 9, 21, 34, 50 ] 
```