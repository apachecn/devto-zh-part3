# 快速排序算法

> 原文：<https://dev.to/askeroff/quicksort-algorithm-3hhe>

**快速介绍**。我自己正在学习算法和数据结构。我写这篇文章是为了让自己更好地理解它，并希望它能帮助其他人。如果有需要改进的地方，或者不正确的地方，请分享。

快速排序算法是我没有马上得到的算法，我不得不花更多的时间去理解每一个移动的部分。这可能发生在很多包含递归的算法中。所以，我们开始吧。

# 概述

这是使用“分而治之”技术的算法之一。这里的要点是，我们解决这个问题时不断地分割我们的输入，直到它被完全解决。

这也是一种“就地”算法，意味着我们移动数组中的元素，改变数组，并且我们不创建任何新的数组来保存排序的值，并且不为元素使用任何额外的空间。

整个想法围绕着枢轴元素。这是第一步。**选择一个支点**。可以是第一个元素，也可以是最后一个，也可以是中间的东西。没关系，但我会用例子中的第一个。

现在，支点已经选好了。下一步是将小于轴心的所有东西放在轴心的左边，将大于轴心的所有东西放在右边。这部分也被称为**分区**。

这意味着你首先选择的元素，你的轴心，四处移动，与比它大和比它小的元素交换位置，直到它在数组中找到它的位置。

然后你再次调用这个函数，对于数组中枢轴右边和左边的部分。出于这个原因，你也应该跟踪枢纽指数。

概括一下，让我们一步一步来看:

1.  声明一个函数，它接受 3 个参数:数组，起始索引，结束索引

2.  选择一个轴心点

3.  跟踪左和右索引。我们要么从左到右，要么从右到左。为此，我们需要知道我们的立场。首先，将 left 赋给起始索引，right 赋给结束索引(我指的是传递给函数的开始和结束参数)。

4.  现在，分区过程将继续进行，当左索引小于右索引时。

5.  如果你正在查看的值**右索引**小于 pivot，这意味着它在错误的位置。交换它们并更新**中枢指数**。

6.  否则，如果左侧索引**处的值大于中枢索引处的值，则交换它们。更新透视索引。**

7.  在此分区过程中，跟踪是将右指针进一步向左移动，还是将左指针向右移动。很简单，如果右索引比主索引多，就减少它。如果左边的那个小于枢轴，那么增加那个。这在我的实现中有效，其中 pivotIndex 是第一个元素。

8.  这样做，直到左索引小于右索引。

9.  现在是递归部分。对右索引右侧的数组部分和左索引左侧的数组部分调用该函数两次。

## 代码

```
function swap(arr, left, right) {
  let temp = arr[left];
  arr[left] = arr[right];
  arr[right] = temp;
}

function quickSort(arr, low = 0, high = arr.length - 1) {
  if (arr.length <= 1 || high < 0 || low >= high) {
    return arr;
  }
  let pivotIndex = low;
  let left = low;
  let right = high;
  while (left < right) {
    if (arr[right] < arr[pivotIndex]) {
      swap(arr, pivotIndex, right);
      pivotIndex = right;
    } else if (arr[left] > arr[pivotIndex]) {
      swap(arr, pivotIndex, left);
      pivotIndex = left;
    }
    if (right > pivotIndex) {
      right--;
    } else if (left < pivotIndex) {
      left++;
    }
  }
  if (low < high) {
    quickSort(arr, low, pivotIndex - 1);
    quickSort(arr, pivotIndex + 1, high);
  }
  return arr;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 其他资源

[匈牙利民间舞蹈快速排序](https://www.youtube.com/watch?v=ywWBy6J5gz8&t=75s)

[我还制作了一个视频，如果你喜欢这种格式的话](https://youtu.be/Lm1c80I6ppM)