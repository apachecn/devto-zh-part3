# JavaScript 数组 Map()方法

> 原文：<https://dev.to/sarah_chima/the-javascript-array-map-method-44m0>

想知道 JavaScript 数组映射方法是什么吗？它能做什么？或者应该在什么时候使用？这篇文章是给你的。在我们进一步讨论之前，让我们得到 map 方法的正式定义。

## 符合方法

根据 MDN 文档:

> map()方法创建一个新数组，其结果是对调用数组中的每个元素调用一个提供的函数。

让我们简化一下。

JavaScript map 方法获取一个数组(数组 1)，并通过对给定数组(数组 1)中的每个元素调用一个函数来创建一个新数组(数组 2)。为了进一步解释这一点，我们将使用一个例子来看看 map 方法实现了什么。

让我们用常规的 for 循环来说明 map 函数确实如此。一个经典的例子是，如果我们想要一个给定数组中所有元素的平方的数组，我们可以使用 for 循环来实现，如下例所示:

```
 const array = [2, 5, 9];
    let squares = [];

    for (let i = 0; i < array.length; i++) {
        squares.push(array[i] * array[i]));
    }

    console.log(squares); // [4, 25, 81]
    console.log(array); // [2, 5, 9] 
```

Enter fullscreen mode Exit fullscreen mode

以上实现了什么？它遍历一个数组，找到数组中每个元素的平方，并将其推送到之前定义的 squares 数组中。

这类似于 map 函数所实现的，只是您不需要定义另一个结果被推送到的数组。地图功能会自动完成这项工作。所以让我们使用 map 函数来完成上面的工作。

页（page 的缩写）s:示例中使用了箭头函数。如果你不完全理解它的语法，请参考[这篇文章](https://dev.to/sarah_chima/arrow-functions-in-es6-24)上的内容。

```
 const array = [2, 5, 9];
    let squares = array.map((num) => num * num);

    console.log(squares); // [4, 25, 81]
    console.log(array); // [2, 5, 9] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，使用 map 方法并完成同样的事情要容易得多。另外，注意初始数组保持不变，这在函数式编程中特别有用。现在让我们更深入地研究一下 map 方法。

## 映射方法及其语法

map 函数的语法如下:

```
 let newArray = array.map((currentValue, index, array) => {
        // return element to new Array
    }); 
```

Enter fullscreen mode Exit fullscreen mode

*   newArray -返回的数组
*   array -在其上调用 map 方法的数组
*   currentValue -正在处理的值
*   index -正在处理的当前值的索引

##### **下面是一些关于 map 方法的注意事项:**

1.  它返回一个新的数组。
2.  它不会改变调用它的原始数组，即原始数组保持不变。
3.  map 函数处理的元素范围在第一次调用之前设置。如果元素是在映射开始后添加到数组中的，回调将不会处理它。

## 何时使用地图方法

假设还有其他类似的数组方法，如 ForEach 方法，您可能会想，“我应该在什么时候使用(或不使用)map 方法？”这里有一些问题可以帮助你做出决定:

1.  我需要从方法中返回一个数组吗？返回的数组会被使用吗？
2.  我是从回调函数中返回值吗？

如果你对这些问题的答案是肯定的，你应该使用地图功能。如果两种情况下你的答案都是否定的，你应该用`forEach`或者`for..of`来代替。

## 映射方法的示例

除了前面使用的例子，这里还有一些其他的例子，你可以用 map 方法做一些事情。

##### 示例 1:从对象数组中提取值

我们希望从一组对象中提取某些值。例如，在一个女孩数组中，我们想得到女孩的年龄。

```
 const girls = [
       {name: 'Sarah', age: 19},
       {name: 'Laura', age: 10},
       {name: 'Jessy', age: 29},
       {name: 'Amy', age: 23}];

    let girlsAges = girls.map((girl) => girl.age);

    console.log(girlsAges);  //[19, 10, 29, 23] 
```

Enter fullscreen mode Exit fullscreen mode

##### 示例 2:仅在某些元素上应用回调

如果我们希望回调只应用于数组中的某些元素，比如奇数，我们可以使用 If 语句来实现。

```
 const numbers = [4, 9, 36, 49];

    let oddSquareRoots = numbers.map((num) => {
       if(num % 2 != 0) {
           return Math.sqrt(num)     
       }
       return num;
    })

    console.log(oddSquareRoots); 
```

Enter fullscreen mode Exit fullscreen mode

或者使用三元运算符

```
 const numbers = [4, 9, 36, 49];

    let oddSquareRoots = numbers.map((num) => {
       return num % 2 !== 0 ? Math.sqrt(num) : num 
    })

    console.log(oddSquareRoots); 
```

Enter fullscreen mode Exit fullscreen mode

然而，更有效的方法是使用 JavaScript 数组过滤方法。这将在我的下一篇文章中讨论。

### 结论

如果使用正确，JavaScript 数组映射方法是一种可以用来简化代码的方法。如果你有其他的例子来展示你如何使用 map 方法，请在评论区分享。

有什么问题或补充吗？请留言评论。

感谢您的阅读:)

#### 无耻的插头🙈

如果你想了解我更多，这里有一个链接到我的网站。