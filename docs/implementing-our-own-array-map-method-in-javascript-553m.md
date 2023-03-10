# 用 javascript 实现我们自己的 Array.map()方法

> 原文：<https://dev.to/dhilipkmr/implementing-our-own-array-map-method-in-javascript-553m>

[![](img/b2d198e8d8da986e1948b0826d0d1cde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qRzdjqhL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pv3pg99ngtl1u2bx7mml.png)

如果你知道`Array.map()`是如何工作的，你可以直接跳到[这里](#map_implementation)。

## JavaScript 中的 Array.map 是什么？

```
A map is a built-in function of Arrays in javascript which helps us iterate over each individual element of the array and returns a brand new array. 
```

Enter fullscreen mode Exit fullscreen mode

首先让我们了解地图是如何工作的，

例如:

```
const sample = [1,2,3];
const mapResult = sample.map(function(val, index, array) {
    console.log('val :', val, 'index :', index, 'array :', array);
    return (val * 2);
}); 
```

Enter fullscreen mode Exit fullscreen mode

上面代码片段的输出将是:
[![](img/ad02c9e263a1064508a1dd3e046b7dad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dDNtIBcb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rzeogrd568lcizcji3ip.png)

因此，我们可以得出结论，对于数组的每个值,函数都会被执行。该函数可以访问 3 个参数:

*   被处理的当前元素
*   当前元素的索引
*   整个阵列

我们在每次迭代中都返回`val*2`，并存储在`mapResult`中。
所以，`mapResult`里面有[2，4，6]，这不会修改原来的数组`sample`。

因此，无论 map 在每次迭代中返回什么，都将存储在一个全新的数组中，而原始数组保持不变。

> 注意:如果函数没有返回任何东西，那么`undefined`将存储在输出数组中。并且该数组的长度将与映射完成的数组的长度相同。

如果我们在前面的例子中没有返回任何东西，

[![](img/8591c0fbbd5b2e5290a362fc307251a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--94BGHt73--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7vncgvcjv659kt1itxoy.png)

`map`将总是返回一个**数组**。所以我们不需要从 Array.map 函数中写一个显式的返回，这就是为什么我们大部分时间使用 map 来遍历 React 中的列表。

## 让我们创建自己的地图方法[mymap]

### 第一步:

*   我们将创建一个新方法[mymap],它允许我们使用`Array.mymap()`
*   为了使用 Array.mymap()，我们必须在 Array.prototype 中有`mymap()`的定义。

```
Array.prototype.mymap = function(){

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将能够运行`[1,2,3].mymap();`，它将返回`undefined`。

### 第二步:

*   `map`是用函数作为内部参数调用的。(例如:`[1,2].map(function(val, index, arr){})`)。所以，我们的`mymap`函数应该接受一个函数作为参数。
*   应该为具有 3 个参数的数组中的每个值调用参数中的函数:
    *   当前元素
    *   当前元素的索引
    *   整个阵列
*   `this`是指完成`mymap`的**数组**。`this`是阵列本身。

```
Array.prototype.mymap = function(callback) {
    for (let index = 0; index < this.length; index++) {
        callback(this[index], index, this);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:

*   最后，我们将结果输出到一个新的数组并返回它们。

```
Array.prototype.mymap = function(callback) {
    const resultArray = [];
    for (let index = 0; index < this.length; index++) {
        resultArray.push(callback(this[index], index, this));
    }
    return resultArray;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 输出:

[![](img/e74712a165fde8189588a6765de6d136.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JFPsnnaD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7mh99hnc2tl3v586a26j.png)

就是这样:)我们实现了自己的 map 方法。

如果对你有帮助就分享一下:)

[![](img/647f63eb479273178d5301cc55e8b9b7.png)](https://i.giphy.com/media/2HMUYBYrhg4Gk/giphy.gif)

下一步:尝试使用类似的方法，为对象创建一个自定义的地图。