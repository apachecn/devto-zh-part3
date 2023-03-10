# 针对 JS 初学者的 JavaScript 插入排序

> 原文：<https://dev.to/ryan_dunton/insertion-sorting-for-beginners-in-js------fkg>

本周早些时候，我写了一篇[文章](https://dev.to/ryan_dunton/bubble-sorting-for-beginners-in-js-2opp)概述了一个基本的冒泡排序算法。今天我要解决插入排序算法。这些帖子背后的动机是我理解它们是什么，但考虑到我是一个每天都在使用 JS 的专业开发人员，我需要加强对它们的实际理解。

## 什么是插入排序？

插入排序是一种通过将数组分为“已排序”部分和“未排序”部分来排序数组的方法。然后，我们比较未排序的项目，看它是否大于前一个元素，如果不是，我们插入新的项目。基本上，我们是从左向右看，边看边排序。

让我们开始构建我们的`insertionSort`函数。

## 第一步

```
const insertionSort = arr => {
  const len = arr.length;
  return arr;
}; 
```

Enter fullscreen mode Exit fullscreen mode

我总是发现将数组长度保存在变量中比不断引用 arr.length 更好。

## 第二步

```
const insertionSort = arr => {
  const len = arr.length;
  for (let i = 0; i < len; i++) {
    //
  }
  return arr;
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有一个 for 循环来循环数组的每个元素，我们将在数组内部进行排序。

## 第三步

```
const insertionSort = arr => {
  const len = arr.length;
  for (let i = 0; i < len; i++) {
    let el = arr[i];
    let j;
  }
  return arr;
}; 
```

Enter fullscreen mode Exit fullscreen mode

设置一个变量`el`来保存当前值，初始化另一个变量`j`，并在下一个 for 循环之外设置它，以保持适当的作用域。

## 第四步

```
const insertionSort = arr => {
  const len = arr.length;
  for (let i = 0; i < len; i++) {
    let el = arr[i];
    let j;

    for (j = i - 1; j >= 0 && arr[j] > el; j--) {
      arr[j + 1] = arr[j];
    }
  }
  return arr;
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在第一个 for 循环中设置一个 for 循环。我们将当前数组位置的值减 1 赋值给`j`,如果该值大于 0，并且当前元素小于开始的循环元素，就对其求值。

## 第五步

```
const insertionSort = arr => {
  const len = arr.length;
  for (let i = 0; i < len; i++) {
    let el = arr[i];
    let j;

    for (j = i - 1; j >= 0 && arr[j] > el; j--) {
      arr[j + 1] = arr[j];
    }
    arr[j + 1] = el;
  }
  return arr;
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将值`el`赋给数组中的当前索引位置。使用`j+1`是因为我们最初将`j`的值设置为`i-1`。

这是插入排序算法的基础！