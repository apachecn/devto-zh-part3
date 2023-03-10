# JavaScript 🧐中的数组操作

> 原文：<https://dev.to/thomasaudo/advanced-array-manipulation-in-javascript--fhi>

# 简介

当我们开始编程时，我们倾向于用自己的循环**创建**自己的函数**来操作数组**。
T5 现实中，几乎所有的 **OOP 语言**，包括 JavaScript，都为我们提供了**方法来实现**。

如果你从 JavaScript 开始，这篇文章应该真的**有用**，对于其他人，它将是一个**伟大的提醒**

## 每

every 方法允许我们测试数组**的所有元素是否都验证了一个条件。**
如果所有元素都验证了测试，该方法返回**真**，否则返回**假**。
条件是一个函数。

例如，以下示例将测试数组中的每个“人”是否都是成年人:

```
// The condition has to return a boolean
function condition (human) {
    return human.age >= 18
}

var arr = [{
    name: 'Thomas',
    age: 19},{
    name: 'Noé',
    age: 17},{
    name: 'Luc',
    age: 20}]

console.log(arr.every(condition))
// Should output false 
```

然而，如果至少有一个元素通过了测试，some()方法将返回 **true**

## 滤镜

filter 方法创建并返回一个新的**数组**，其中所有的元素**都验证了一个条件**。
条件是一个函数。

以下示例返回一个唯一由成人组成的数组:

```
function condition (human) {
    return human.age >= 18
}
var arr = [{
    name: 'Thomas',
    age: 19},{
    name: 'Noé',
    age: 17},{
    name: 'Luc',
    age: 20}]

console.log(arr.filter(condition))
// Returns Thomas and Luc 
```

## 找到

方法 find()返回数组中第一个元素的**，该元素由**验证条件**。
否则，如果没有元素验证，find()返回**‘undefined’**。
和往常一样，条件是一个函数。**

此示例返回第一个成人:

```
function condition (human) {
    return human.age >= 18
}

var arr = [{
    name: 'Thomas',
    age: 19},{
    name: 'Noé',
    age: 17},{
    name: 'Luc',
    age: 20}]

console.log(arr.find(condition))
// Should returns Thomas 
```

## 地图

map 方法用为调用数组的**的每个元素**执行的函数的**返回值**创建一个**新数组**

此示例递增每个人的年龄:

```
var arr = [{
    name: 'Thomas',
    age: 19},{
    name: 'Noé',
    age: 17},{
    name: 'Luc',
    age: 20}]

console.log(arr.map(function(element){
    element.age += 1    
    return element
})) 
```

## 减少

最后但同样重要的是，reduce()方法对我来说是最棘手的一个方法。

reduce 方法通过为数组的每个值的**执行提供的函数，将数组缩减为**单值**。
对于每个元素，返回值存储在一个**‘累加器’**中，可以在所有**迭代**中使用。
reduce()方法的**最终返回**值就是这个**累加器**。
累加器**应该在方法调用中被**初始化******

此外，reducer 函数可以接受两个其他参数:

*   当前索引
*   源数组

此示例返回年龄总和:

```
function reducer (accumulator,element) {
    return accumulator + element.age
}

var arr = [{
    name: 'Thomas',
    age: 19},{
    name: 'Noé',
    age: 17},{
    name: 'Luc',
    age: 20}]

console.log(arr.reduce(reducer,0)) 
```

前面的例子很简单，但是 reduce()方法确实**强大**，你可以用它实现很多事情。

# 结论

感谢您阅读我的文章，如果您有任何问题，**请提问**！🧐