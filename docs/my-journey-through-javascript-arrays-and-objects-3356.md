# 我的 Javascript 之旅:数组和对象

> 原文：<https://dev.to/reduncan/my-journey-through-javascript-arrays-and-objects-3356>

现在我们知道了基本语法，我们可以讨论更高级的变量类型、*数组和方法*。在我之前关于[基本语法](https://dev.to/reduncan/my-journey-through-javascript-basic-syntax-bj6)的文章中，我们了解到数字、字符串和布尔是 JavaScript 中的变量类型。然而，我们也有数组和对象。

**数组:**

```
const cars = ['Tesla', 'Ford', 'Honda', 'Jeep']; 
```

*   列出类似变量
*   数组中元素的长度和类型是不固定的
*   数组中的元素可以由我们已经讨论过的任何类型的变量组成
*   数组用左右方括号[]表示
*   数组内部的元素用逗号分隔
*   数组内部的元素可以使用括号符号来访问(cars[1])
*   数组中元素的编号从 0 开始

**对象:**

```
const car = {
    make: 'Tesla',
    model: 'Model X',
    price: 71,200,
    color: 'red'
}; 
```

*   键/值对列表
*   键/值对可以由任何元素组成，甚至是函数
*   对象用左花括号和右花括号{}来表示
*   键/值对通过陈述键，然后用冒号后跟值来表示(make:“Tesla”)
*   每个键/值对由逗号分隔
*   可以使用点符号(car.name)或括号符号(car[name])来访问对象中的键/值对，但点符号是标准符号

现在我们可以让它变得更复杂，拥有一个对象数组。它们通过创建一个数组来格式化，数组中的每一项都是由键/值对组成的对象。

```
const cars = [
    {
        make: 'Tesla',
        model: 'Model X',
        price: 71,200,
        color: 'red'
    },
    {
        make: 'Tesla',
        model: 'Model S',
        price: 65,000,
        color: 'silver'
    },
    {
        make: 'Tesla',
        model: 'Model 3',
        price: 34,200,
        color: 'black'
    }
]; 
```

上面的相同属性仍然适用于对象数组，但是我们访问键/值对的方式发生了变化。要访问键/值对，我们必须使用点和括号符号。如果我们想访问 Tesla Model S 的价格，我们应该键入 cars[1].model。我们必须使用括号符号来访问数组中的正确对象，使用点符号来访问第二个对象中的键/值对。

这些是数组和对象的基础！下一次我们将看数组和对象方法。

直到下次:)