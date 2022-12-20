# 在 JavaScript 中从数组中删除重复项

> 原文：<https://dev.to/yashints/removing-duplicates-from-arrays-in-javascript-231f>

有多种方法可以用 JavaScript 从数组中删除重复项。但是，了解它们效率的细节非常重要，尤其是在处理大型阵列时。

## 最简单的方法

最简单的方法是使用 ES6 的(ECMAScript 2015) `Set`对象。这使您可以存储任何类型的唯一值。它会自动为我们删除重复的内容，这不是很好吗？

```
const fruit = [
  'apple',
  'orange',
  'avo',
  'pear',
  'cherries',
  'strawberries',
  'avo',
  'avo',
];

let uniqueFruit = [...new Set(fruit)];

console.log(uniqueFruit); // ['apple', 'orange', 'avo', 'pear', 'cherries', 'strawberries'] 
```

Enter fullscreen mode Exit fullscreen mode

这里我们不得不使用`spread`操作符再次从`Set`中获取一个数组。

## 聪明但天真的方法

我们可以使用 ES5 的内置过滤方法来达到同样的效果:

```
const fruits = [
  'apple',
  'orange',
  'avo',
  'pear',
  'cherries',
  'strawberries',
  'avo',
  'avo',
];

const uniqueFruit = fruits.filter(
  (item, i, arr) => {
    return arr.indexOf(item) == i;
  }
);

console.log(uniqueFruit); // ['apple', 'orange', 'avo', 'pear', 'cherries', 'strawberries'] 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做的事情并不复杂。我们将遍历每个元素，检查该项在数组中的第一个位置是否等于当前位置。由于这两个位置对于重复项来说是不同的，所以它们将被过滤掉，我们最终得到一个唯一的数组🤩。

[[警告]]
| **警告:**这种方法对于大型数组(二次时间)不是那么有效。

## 哈希表营救

这种方法是将每个项目放在一个`hashtable`中，然后检查它是否存在。这给出了一个线性时间，但至少有两个缺陷:

*   因为散列键只能是字符串，所以这段代码不能区分数字和数值字符串。这意味着`['1', 1]`将返回`[1]`。
*   因为☝🏼，所有对象都将是平等的🤦‍♂️.

```
function unique(fruits) {
  var seen = {};
  return fruits.filter(item => {
    return seen.hasOwnProperty(item)
      ? false
      : (seen[item] = true);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

## 混合而最佳的方法

更好的方法是将两种方法结合起来。它对原始类型使用散列查找，对对象使用线性搜索:

```
function unique(fruits) {
  var prims = {
      boolean: {},
      number: {},
      string: {},
    },
    objs = [];

  return fruits.filter(item => {
    var type = typeof item;
    if (type in prims)
      return prims[type].hasOwnProperty(item)
        ? false
        : (prims[type][item] = true);
    else
      return objs.indexOf(item) >= 0
        ? false
        : objs.push(item);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

## 排序&独特

另一种选择是对数组进行排序，然后删除与紧接其后的项目相同的项目:

```
function unique(fruits) {
  return fruits
    .sort()
    .filter((item, pos, ary) => {
      return !pos || item != ary[pos - 1];
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

此项类似于哈希表方法，它不处理对象。更重要的是，我们必须改变原始数组，这是一个副作用，不是一个好办法。

## 使用减少

```
const uniqueFruits = fruits.reduce((a, b) => {
  if (a.indexOf(b) < 0) a.push(b);
  return a;
}, []);

console.log(uniqueFruits); // ['apple', 'orange', 'avo', 'pear', 'cherries', 'strawberries'] 
```

Enter fullscreen mode Exit fullscreen mode

这种方法使用一个累加器来查看该项之前是否被插入其中。与我们之前介绍的过滤方法非常相似，只是速度更快一些。

## 总结

这些只是完成相同操作的几种方法。但是正如你所看到的，在本质和浏览器支持上有很大的不同。精心挑选快乐编码。