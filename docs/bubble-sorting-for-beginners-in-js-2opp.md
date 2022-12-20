# 冒泡排序 Javascript 冒泡排序，适合 JS 初学者

> 原文：<https://dev.to/ryan_dunton/bubble-sorting-for-beginners-in-js-2opp>

作为一个整天使用 Javascript 的人，每天为了工作，我意识到我把许多基本的算法任务视为理所当然，所以我决定在接下来的几周内在博客帖子中深入研究基础知识，从今天的冒泡排序开始。

## 什么是冒泡排序？

冒泡排序是一种通过将每个数组元素与其后面的元素进行比较来对数组进行排序的方法。因此，如果你有一个带有`[3,5,4, 2]`的数组，冒泡排序函数将比较“3”和“5 ”,然后比较“5”和“4 ”,依此类推，直到数组被排序。

## 冒泡排序的最佳方式

我发现的最好的冒泡排序方法如下:

```
const bubbleSort = arr => {
  let swapped;
  do {
    swapped = false;
    for (let i = 0; i < arr.length; i++) {
      if (arr[i] > arr[i + 1]) {
        let tmp = arr[i];
        arr[i] = arr[i + 1];
        arr[i + 1] = tmp;
        swapped = true;
      }
    }
  } while (swapped);
  return arr;
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 代码分解

现在让我们一段一段地分解这段代码。

```
const bubbleSort = arr => {
  let swapped;
  do {} while (swapped);
  return arr;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们初始化一个`swapped`变量，并设置一个 do/while 循环，当`swapped`等于 true 时运行，然后返回数组。

接下来:

```
const bubbleSort = arr => {
  let swapped;
  do {
    swapped = false;
    for (let i = 0; i < arr.length; i++) {
      // do stuff here 
    }
  } while (swapped);
  return arr;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经设置了函数来循环数组的每个元素。

接下来:

```
const bubbleSort = arr => {
  let swapped;
  do {
    swapped = false;
    for (let i = 0; i < arr.length; i++) {
      if (arr[i] > arr[i + 1]) {
        // if element > the next element
      } 
    }
  } while (swapped);
  return arr;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果数组中的元素比它后面的元素大，我们想对它做点什么(交换)。

接下来:

```
const bubbleSort = arr => {
  let swapped;
  do {
    swapped = false;
    for (let i = 0; i < arr.length; i++) {
      if (arr[i] > arr[i + 1]) {
        let tmp = arr[i];
        arr[i] = arr[i + 1];
        arr[i + 1] = tmp;
        swapped = true;
      }
    }
  } while (swapped);
  return arr;
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们需要将`arr[i]`元素保存到`tmp`变量中，因为我们要用后面元素的值(`arr[i+1]`)覆盖它。然后，我们将元素后面的值(`arr[i+1]`)重新赋值为等于`tmp`。

这是我在一个基本的冒泡排序函数上的最好尝试，如果你找到一个更好的解决方案，请告诉我！