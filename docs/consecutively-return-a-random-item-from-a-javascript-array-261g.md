# 从 JavaScript 数组中连续返回一个随机项

> 原文：<https://dev.to/hybrid_alex/consecutively-return-a-random-item-from-a-javascript-array-261g>

*在[Alex carpenter . me](https://alexcarpenter.me/posts/2019/03/random-item-array/)T3 上查看此贴*

另一周，我在做一个[附带项目](https://github.com/alexcarpenter/standup-starter)，我需要通过点击按钮从数组中连续返回一个随机项。

为此，我首先创建了一个从数组中返回随机项的函数。

```
function randomItem(arr) {
  return arr[Math.floor(Math.random() * arr.length)];
} 
```

这里只使用`randomItem`函数的问题是，连续两次返回相同的项目并不罕见。

为了解决这个问题，我使用了一个`do...while`循环来调用`randomItem`函数，直到它返回一个不等于先前选择的项目的结果。

所以当`prevItem`等于`currentItem`时，运行`randomItem`函数。

```
var fruit = ['Apples', 'Oranges', 'Pears'];

var currentItem = randomItem(fruit);

btn.addEventListener('click', function () {
  var prevItem = currentItem;
  do {
    currentItem = randomItem(fruit);
  } while (prevItem === currentItem);
}, false); 
```

现在，当我们单击按钮时，我们将不会连续返回相同的项目。

检查[演示](https://codepen.io/alexcarpenter/pen/QoMKNv?editors=1011)并打开控制台查看它的运行。