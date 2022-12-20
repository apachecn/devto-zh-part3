# 对象与数组

> 原文：<https://dev.to/zac_heisey/objects-vs-arrays-2g0e>

<figure>[![](img/f1c1cb01aec938fa830b6d850223e9c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5y0kKHnj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANACus9WrUGcxW2cgxAxBTA.jpeg) 

<figcaption>照片由[布莱克·康纳利](https://unsplash.com/photos/IKUYGCFmfw4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[下](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

在 JavaScript 中，对象和数组都被认为是“特殊的”。对象表示一种特殊的数据类型，它是可变的，可以用来存储一组数据(而不仅仅是一个值)。数组是一种特殊类型的变量，它是*也是*可变的，并且*也是*可以用来存储一列值。那么，对象和数组之间到底有什么区别，什么时候使用它们，以及如何使用它们呢？

### 物体

**什么时候使用对象**

对象是用来代表你代码中的一个“东西”。这可能是一个人、一辆车、一栋建筑、一本书、一个游戏中的角色——基本上是由一组特征组成或可以由一组特征定义的任何东西。在对象中，这些特征被称为由一个键和值组成的**属性**。

```
// Basic object syntax
var object = {
  key: 'value'
};

// Example 'person' object
var person = {
  name: 'Zac',
  age: 33,
  likesCoding: true
}; 
```

**访问、添加和删除对象中的项目**

对象中的属性可以通过使用**点**或**括号**符号来访问、添加、更改和删除。要用点和括号符号获得 personobject 中年龄键的值，我们应该写:

```
// Dot notation
person.age // returns 33

// Bracket notation
person['age'] // returns 33 
```

假设我们想要将 likesCoding 的值更改为 **false** 。我们可以这样用点符号:

```
person.likesCoding = false; 
```

如果我们想给 person 对象添加一个新的属性，我们也可以用点符号来完成:

```
person.hobbies = ['hiking', 'travel', 'reading']; 
```

最后，为了从对象中删除一个属性，我们使用 delete 关键字，比如:

```
delete person.age; 
```

[看看这篇文章](https://codeburst.io/javascript-quickie-dot-notation-vs-bracket-notation-333641c0f781)关于点和括号符号的区别，以及何时使用它们。

**遍历对象**

遍历对象中的属性最常见的方式是在循环中使用**for…循环:** 

```
for (var key in myObject) {
  console.log(key); // logs keys in myObject
  console.log(myObject[key]); // logs values in myObject
} 
```

另一种遍历对象属性的方法是将 **forEach()** 方法附加到 **Object.keys()** :

```
Object.keys(myObject).forEach(function(key) {
  console.log(key); // logs keys in myObject
  console.log(myObject[key]); // logs values in myObject
}); 
```

### 数组

**何时使用数组**

每当我们想要在单个变量中创建和存储多个项目的列表时，我们都会使用数组。数组在创建**有序集合**时特别有用，集合中的项目可以通过它们在列表中的数字位置来访问。正如对象属性可以存储任何[原始数据类型](https://javascript.info/types)的值(以及一个数组或另一个对象)，数组也可以由字符串、数字、布尔值、对象甚至其他数组组成。

**从数组**

中访问、添加和移除项目数组使用[从零开始的索引](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#Accessing_array_elements)，因此数组中的第一个项目的索引为 0，第二个项目的索引为 1，依此类推。例如，假设我们想要访问以下数组中的第三项(“墨西哥城”):

```
var vacationSpots = ['Tokyo', 'Bali', 'Mexico City', 'Vancouver']; 
```

为此，我们应该写:

```
vacationSpots[2]; // returns 'Mexico City' 
```

可以使用 push()、pop()、unshift()和 shift()方法从数组的开头或结尾添加和移除项目:

```
// push() - Adds item(s) to the end of an array
vacationSpots.push('Miami');

// pop() - Removes the last item from an array
vacationSpots.pop();

// unshift() - Adds item(s) to the beginning of an array
vacationSpots.unshift('Cape Town', 'Moscow');

// shift() - Removes the first item from an array
vacationSpots.shift(); 
```

**遍历数组**

我们可以用几种方法遍历数组中的条目。首先是循环的标准**:** 

```
for (var i = 0; i < myArray.length; i++) {
  console.log(myArray[i]); // logs items in myArray
} 
```

还有循环的**:** 

```
for (var item of myArray) {
  console.log(item); // logs items in myArray
} 
```

或者我们可以利用 **forEach()** 方法:

```
myArray.forEach(item) {
  console.log(item); // logs items in myArray
}); 
```

同样，对象和数组的迭代技术之间的相似性会使事情变得混乱。这里有一篇[有用的文章](https://bitsofco.de/for-in-vs-for-of/)来澄清事情。

### 包装完毕

对象代表具有特征(也称为属性)的“事物”，而数组在单个变量中创建和存储数据列表。点和括号符号都允许我们访问、添加、更改和删除对象中的项，而从零开始的索引和各种内置方法允许我们访问和更改数组中的项。最后，我们可以使用各种循环(例如 for、for…in、for…of、forEach())迭代对象属性和数组项。

*感谢阅读！如果你有兴趣学习更多关于 HTML、CSS 和 JavaScript 的基础知识，请跟随 Medium 上的* [*Web Dev 基础知识*](https://medium.com/web-dev-basics) *。准备好写一些代码了吗？太棒了。* [*报名参加*](https://www.web-dev-basics.com/) *课程，学习 web 开发基础知识。*

* * *