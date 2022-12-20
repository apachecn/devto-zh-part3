# JAVASCRIPT:数据类型

> 原文：<https://dev.to/5anthosh/javascript-data-types-3h36>

这篇文章最初发表在我的博客上，在这里找到原文

每个变量都有一个数据类型来决定它存储的值的类型，number 类型的变量可以存储整数或浮点数。

JavaScript 是动态类型语言，这意味着一个变量可以是数字类型，也可以是字符串类型。

## 数字

`number` type 表示整数和小数(浮点数)。

```
let num = 79; // integer
let num = 65.45; // floating-point 
```

我们可以进行类似于`+`、`-`、`*`、`/`的算术运算。

```
let num = 45 + 2 * 6; // 57 
```

对于非常大的数或者非常小的数，我们可以使用`*e*`(指数)

```
let speedOfLight = 2.998e8; 
```

`Infinity`代表数学上的无穷大，这是一个特殊的数值。

```
let infinity = 1 / 0; 
console.log(infinity); // Infinity 
```

`NaN`也是一个特殊的数值，用来表示不正确或无效的数学运算。

```
let nan = "str" / 79; 
```

> 我们可以使用`typeof`获得变量的数据类型。
> 
> ```
> console.log(typeof "peace"); //string 
> ```

## 字符串

`string`用来表示文字。它们是用引号括起它们的内容来写的。

```
let hello = "Hello World";
let sq = 'sinle quote'; 
```

单引号和双引号都可以使用。

反斜杠代表字符串模板，我们可以通过将变量和表达式包含在`${ variable or expression }`
中来将它们嵌入到字符串中，表达式在`${....}`中被求值并成为字符串的一部分。

```
let name = "Kevin";
console.log(`My name is ${ name } `); // My name is kevin
console.log(`Age is ${17 + 1} `); // Age is 18 
```

## 布尔

布尔类型只有两个值:`true`和`false`。

它用于存储是/否值。

```
let isGreater = true; 
```

## null

为了表示虚无、空虚，我们可以用`null`

```
let answer = null; 
```

## 未定义

如果变量没有赋值或者变量没有声明，那么它将有`undefined`。

`undefined`的含义是“未赋值”。

```
let a;
console.log(a) //undefined 
```