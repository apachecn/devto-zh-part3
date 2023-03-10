# 定义函数

> 原文：<https://dev.to/banesag/defining-functions-3dml>

[![alt text](img/488270b0166ea78d1b1cfd3c4615c3c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bi_X_DV9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rbd6k57twk4lh3pfsvix.jpg)

### 定义函数

**函数声明**

在 JavaScript 中定义函数有不同的方法，但我将介绍三种最常见的方法。

**函数文字**

要定义函数文字，可以使用函数声明:

```
function hello( ) {
console.log(‘Hello World!’);
} 
```

该函数的语法以 function 关键字开始，后面是分配给该函数的名称，在本例中是“hello ”,后面是括号，最后是该函数的代码块。

**函数表达式**

函数表达式是定义函数的另一种方式，这是通过给变量分配一个匿名函数来实现的:

```
const bye = function ( ) {
console.log(‘Bye World’);
}; 
```

它被称为匿名函数，因为它没有名字。它只是被创建并赋给一个变量。

但是，你也可以创建一个命名函数表达式:

```
const bye = function bye( ) {
console.log(‘Bye World’);
}; 
```

在这种情况下，函数的名称是“bye ”,它被赋给变量“goodbye”。

**函数( )构造函数**

我们也可以通过使用构造函数( )来创建一个函数。它的主体以字符串形式输入:

```
const hi = new function (‘console.log(“Hi World”);  ) ; 
```

虽然这是定义函数的另一种方法，但不推荐使用，因为在函数体中放置字符串会有一些问题，特别是因为 console.log 中引号的数量不同。

ES6 引入了一种使用箭头符号定义函数的新方法，名为‘箭头函数’,但我会写一篇关于它的新博客。