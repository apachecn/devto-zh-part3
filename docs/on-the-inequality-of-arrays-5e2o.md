# 关于数组的不等式

> 原文：<https://dev.to/samelawrence/on-the-inequality-of-arrays-5e2o>

我最近发现两个相同的数组本质上是不相等的，并且在比较时永远不会返回“真”。我的挑战是解决以下问题:

> 写一个名为 moveAllZeros 的函数。它将接受一组数字作为输入。将数组中的所有 0 移动到其末尾，同时保持非零元素的相对顺序。你必须改变数组。你不能复制。

我使用了下面的代码:

```
const moveAllZeros = function (arr) {
  let i = 0;
  let zeroCounter = 0;
  while (i+zeroCounter < arr.length) {
    if ( arr[i] === 0 ) {
      arr.splice(i, 1);
      arr.push(0);
      zeroCounter ++;
    } else {
      i++;
    }
  }
  return arr;
};

const testArr = [1, 0, 3, 0, 0, 4, 9, 22, 18, 100, 20];
const resultArr = [1, 3, 4, 9, 22, 18, 100, 20, 0, 0, 0];

console.log(moveAllZeros(testArr)); 
```

Enter fullscreen mode Exit fullscreen mode

我曾假设我能够简单地将我的输出与解数组的已知值进行比较，但这不是真的。我花了很长时间试图用`==`和`===`来解决这个问题，但是没有用，直到一个同事提醒我，在 JavaScript 中，没有一个数组永远不会等于另一个数组，除非它们是同一个数组。

这是因为，在 JavaScript 中，所有的对象类型(包括数组)都是通过引用而不是值进行比较的。根据 reddit 用户 [/u/mursfZOR](https://www.reddit.com/user/mursfZOR) 对[这个线程](https://www.reddit.com/r/javascript/comments/539wea/why_are_my_two_arrays_not_equal_when_they_appear/)的评论，“只有原始值可以进行相等性比较——即布尔、字符串、数字。特殊值 null 和 undefined 也是如此。

吸取教训。