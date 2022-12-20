# ES6 变量声明:const，let

> 原文：<https://dev.to/epicosity/es6-variable-declarations-const-let-2j24>

### TLDR

尽可能使用 const。否则，使用 let，但不要使用 var。

### ES6 是什么，它有什么特点？

ECMAScript 6 通常被称为 ES6 或 ES2015，是 2015 年发布的 JavaScript 规范。

ES6 的发布带来了许多新的强大的概念，比如类、模板标签、箭头函数等等。ES6 的所有特性都是大框架的标准，如 [React](https://reactjs.org/) 和 [Angular](https://angular.io/) ，所有的[现代浏览器](https://caniuse.com/#search=es6)也都采用了这些特性。对于其他浏览器，有像 [Babel](https://babeljs.io/) 这样的流行工具，它将把 ES6 代码转换成 ES5，以便在传统浏览器上使用。

ES6 正在迅速成为标准，现在正是学习的最佳时机。

### 变量声明

### 有一个“t0”

如果你曾经用过 JavaScript，那么你可能用过 var。自从语言最初被创建以来，var 就被用来声明变量。

这里有一些关于 var 的知识，包括使用 var 关键字定义的变量可以被重新赋值:

```
var x = 10;
console.log(x); // 10
x = 50;
console.log(x) // 50 
```

使用 var 声明的变量的作用域是当前的执行上下文，这意味着变量要么是函数作用域的，要么只在初始化它们的函数中可用。如果变量没有在函数中定义，那么它的作用域是全局的，并被添加到窗口对象中。

```
// Function scoped
function counter(){
 for (var i = 0; i \< 5; i++) {
 console.log('Hello World');
 }
 console.log(i)
}
counter() 
// Prints 'Hello World' five times
// 5

console.log(i) // Uncaught ReferenceError: i is not defined 
```

发生这种情况是因为 I 的作用域是函数。

```
var age = 10;
function getOlder(){
 for(var i = 0; i \< 5; i++){
 age+=i
 }
 console.log(age)
}

getOlder() // 20

console.log(age) // 20
age = 15;
console.log(age) // 15 
```

一般来说，可以随意重新分配的全局范围的变量被认为是不好的做法，会导致很多令人头疼的问题。

### 让

与 var 一样，关键字 let 用于声明其值预计会随时间变化的变量。和 var 一样，let 在初始化时不需要值，可以重新赋值。在很大程度上，关键字 let 和关键字 var 的作用是一样的，但是有两个很大的区别。首先，不能重新声明已经用 let 初始化的变量，但是可以用 var。

```
var dog = "Marley"
console.log(dog) // Marley

var dog = "Spot"
console.log(dog) // Spot

let cat = "Harlow"
let cat = "Diana" // Uncaught SyntaxError: Identifier 'cat' has already been declared 
```

试图重新声明已经声明的变量会返回语法错误。应该避免重新声明变量，因为这会降低代码的可读性，并可能导致不必要的副作用。

let 和 var 的另一个区别是，let 关键字是块范围的，var 是函数范围的。

下面是上面 counter 函数的重复，但是这次 forloop 使用 let 来声明 I 变量。

```
// Block-scoped
function counter(){
 for (let i = 0; i \< 5; i++) {
 console.log('Hello World'); 
 }
 console.log(i)
}
counter() 
// Prints 'Hello World' five times
// undefined 
```

for 循环后的 console.log(i)返回 undefined，而不是数字五。这是因为变量 I 现在的作用域是 for 循环的块，而不是整个计数器函数。

### 常数

与 let 不同，const 应该用于声明变量，我们希望变量的值永远不会改变或保持不变。像 let 一样，const 是块范围的。用 const 声明的变量不能被重新赋值，但这并不意味着它们是不可变的。

```
const pets = ['Marley']

pets = ['Marley', 'Harlow'] // Uncaught TypeError: Assignment to constant variable.

pets.push('Harlow')
pets // ["Marley", "Harlow"]

const person = {};
person.firstName = 'Tony';

console.log(person); // {firstName: 'Tony'} 
```

### 重构

```
// ES5
var make = 'Honda'
var model = 'Civic'
var owner = 'Tony'
var mileage = '50000' 
```

上面，我们有汽车的四个属性——品牌、型号、所有者和里程。让我们对此进行重构，以使用 ES6 变量声明。

我喜欢如何处理它首先是将 var 的所有用途改为 const。我这样做是因为 const 有更强的绑定规则，这将帮助您编写更好的代码，并且它可以防止将来数小时的调试。

```
const make = 'Honda'
const model = 'Civic'
const owner = 'Tony'
const mileage = '50000' 
```

现在我看着它，想，“这些中的哪一个将来可能会改变？”汽车的品牌和型号永远不会变，但车主和里程数很可能会变。所以我们将把这些声明改为使用 let。

```
// ES6
const make = 'Honda'
const model = 'Civic'
let owner = 'Tony'
let mileage = '50000' 
```

### 可变吊装

使用 var 的 JavaScript 声明被挂起。提升是 JavaScript 中的一种行为，其中声明被提升或带到当前范围的顶部。这意味着您可以在声明变量和函数之前使用它们。下面的例子将更好地解释这一点。

```
age = 26;
console.log(age);
var age;

// 26 
```

即使直到调用 console.log()之后才声明年龄，这也正确地记录了 26。这实际上是这样的。

```
var age = 26;
console.log(age) 
```

上面的代码，展示了 JavaScript 在幕后所做的事情。

然而，当使用 ES6 变量声明 let 和 const 时，这种情况不会发生；

```
age = 26;
console.log(age)
let age;

// Uncaught ReferenceError: age is not defined 
```

虽然 JavaScript 仍然会提升所有声明，包括 let 和 const，但是当使用 var 进行声明时，JavaScript 会用 undefined 初始化它。如果您试图在 let 和 const 被赋值之前调用它们，它们将抛出一个错误。您应该始终在各自范围的顶部声明变量，并在声明变量时初始化变量。let 和 const 的行为将帮助您编写更干净的代码。

### 为什么？

所以你仍然不相信为什么你应该从使用 varto const and let？最有说服力的原因是未来代码的可读性。在一个有许多变量声明的大型程序中，哪些变量应该改变，哪些不应该改变，这可能并不明显。使用 ES6 变量声明将提高代码的可读性，并帮助您编写不容易出错的代码。

* * *