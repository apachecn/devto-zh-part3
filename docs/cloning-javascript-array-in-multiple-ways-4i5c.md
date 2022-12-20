# 以多种方式克隆 JavaScript 数组

> 原文：<https://dev.to/tirthbodawala/cloning-javascript-array-in-multiple-ways-4i5c>

有很多不同的方法来克隆 JavaScript 数组。在本文中，我将列出克隆数组**最突出和最快的方法。**

## 克隆 JavaScript 数组的不同方式

*   ES6 扩展运算符
*   薄片
*   串联
*   数组.应用
*   数组. from
*   阵列地图
*   使用循环索引
*   使用循环推送
*   使用非移位
*   序列化然后去序列化

* * *

## ES6 传播算子

扩展操作符是 ES6 中引入的一个特性。该方法将当前数组扩展到一个新数组中，有效地克隆了当前数组。目前，如果您使用 ES6，此选项是在 Chrome 等浏览器上克隆阵列最快的方法。但是，对于不同的浏览器，这可能会有不同的表现。

```
var obj2 = [...testArray]; 
```

* * *

## 切片

长期以来，这是克隆阵列最常用也是最快的方法。如果您的目标用户可能使用各种浏览器，切片仍然应该是您克隆阵列的首选方式。

```
var obj2 = testArray.slice(); 
```

* * *

## 串联

用 JavaScript 实现克隆的另一种方法是创建一个空数组，然后将当前数组连接到该数组。就性能而言，这种克隆方式一般，会产生浅层拷贝。与其他方法相比，选择这种方法几乎没有任何优势。

```
var obj2 = [].concat(testArray); 
```

* * *

## 阵列应用

您还可以使用其他一些可用于数组的方法来克隆 JavaScript 数组。使用 apply 就是这样一种方式。仅将当前作为第二个参数传递将导致当前数组的浅层克隆。

```
var obj2 = Array.apply(undefined, testArray); 
```

* * *

## 阵从

Array.from，用于从 array like iterable object 返回一个新的浅复制数组实例，该对象也可用于克隆现有数组。根据您的浏览器，这可能不是最适合的，因为它会影响性能。

```
var obj2 = Array.from(testArray); 
```

* * *

## 阵列图

map 方法也可用于迭代当前数组，并逐个返回值。根据您的浏览器，在克隆性能方面，这也可能不是合适的方法。

```
var obj2 = Array.apply(undefined, testArray); 
```

* * *

## 使用循环索引

对于初学者来说，这是克隆数组最简单的方法。只需创建一个简单的循环，并使用索引来检索，然后将数组中的值插入到一个新数组中。

```
var obj2 = new Array(testArray.length);
for (var i = 0, l = testArray.length; i < l; i++) {
  obj2[i] = testArray[i];
} 
```

* * *

## 使用循环推送

这类似于使用循环索引，只是使用循环索引将从当前数组获得的数据推入一个新的空数组。

```
var obj2 = [];
for (var i = 0, l = testArray.length; i < l; i++) {
  obj2.push(testArray[i]);
} 
```

* * *

## 使用 unshift

就性能而言，使用 unshift 来克隆 JavaScript 数组是迄今为止最差的。这是因为与仅在数组末尾添加新值的 push 不同，unshift 在克隆数组的开头添加新值，并将所有现有值移动到下一个位置。

```
var obj2 = [];
for (var i = testArray.length; i--;) {
  obj2.unshift(testArray[i]);
} 
```

* * *

## 序列化然后反序列化

为了实现深度克隆，我们可以使用 JSON.stringify 将数组转换为字符串，然后使用 JSON.parse 解析它。正如所料，序列化和反序列化需要时间，因此这在性能方面是最差的。

```
var obj2 = JSON.parse(JSON.stringify(testArray)); 
```

* * *

## 结论

正如我们所见，有多种方法可以克隆 JavaScript 数组。每种方法都有自己的优点和缺点。因此，你应该选择一个最适合你的。如果你想自己检查性能，可以在这里做[。您还可以使用其他实用程序库(如 lodash)来实现克隆。根据您的浏览器，这些可能会由于额外的开销而影响性能。如果你想了解更多，请在下面的评论中告诉我。](http://jsben.ch/12R7I)

帖子[以多种方式克隆 JavaScript 数组](https://www.atyantik.com/cloning-javascript-array-in-multiple-ways/)最早出现在 [Atyantik Technologies](https://www.atyantik.com) 上。