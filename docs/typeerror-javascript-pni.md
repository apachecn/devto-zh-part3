# 类型错误:JavaScript

> 原文：<https://dev.to/lambdatest/typeerror-javascript-pni>

你能添加一个数字和一个字母表吗？

我说，如果我让你给我一个`1`和`H`相加的结果，你能给我答案吗？

答案显然是否定的。

JavaScript 也是如此！如果在 JavaScript 中将 1 和 H 相加或者试图对两个不匹配类型的操作数执行运算时，JavaScript 会抛出一个`TypeError`。

因此，你可以用技术术语说，当传递给函数的操作数或参数与该操作符或函数所期望的类型不兼容时，就会抛出“ **TypeError”。**

因此，在执行任何操作之前，有必要确保变量必须具有相同的数据类型。类型不匹配会在执行整个程序时产生错误。

因此，在执行任何操作之前，有必要确保变量必须具有相同的数据类型。类型不匹配会在执行整个程序时产生错误。

### 类型错误的类型

例如，如果您试图将一个数字转换成大写字符，您将得到`Uncaught TypeError`。As `toUpperCase()`是一个将字符串转换成大写字符的函数。它将为下面的代码结构给出一个错误。

**代码结构**

```
var num=1;
i.toUpperCase();

```

**错误**

[![Error](img/e4b6f933854446e6d0cfcc54e2b94c98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O4C5cbo8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i9r2b87dsie3oqak0avq.PNG)

### 如何摆脱这种未捕获的类型错误:无法设置属性

有许多方法可以克服这个误差。

**1。使用 toString()函数**
你可以先使用 toString()函数将数字转换成字符串，然后再使用 to upper case()函数将该字符串转换成大写字符。

```
var num = 1;
try {

    num.toString().toUpperCase();   // Convert number into string first 
}
catch(err) {
    document.getElementById("demo").innerHTML = err.name;
}
```

**输出** : 1

**2。使用预定义类**的构造函数 new String()

```
var num = 1;
num=new String(num);
try {

    num.toUpperCase();   // You cannot convert a number to upper case
}
catch(err) {
    console.log(err.name);
}
```

**输出** : 1

以下是 JavaScript 在不同浏览器中可能抛出的更多类型错误。

**键入与 console.log()相关的错误**

```
TypeError: Property 'log' of object # is not a function (Chrome) 
TypeError: console.log is not a function (Firefox)
TypeError: 'your string' is not a function (evaluating 'console.log("your string")') (Safari)
TypeError: Function expected (IE)
```

**键入与提示()相关的错误**

```
TypeError: Property 'prompt' of object [object Object] is not a function (Chrome)
TypeError: prompt is not a function (Firefox)
TypeError: 'a string, this could vary' is not a function (evaluating 'prompt("your question")') (Safari)
TypeError: Function expected (IE)
```

**与确认()相关的类型错误**

```
TypeError: Property 'confirm' of object [object Object] is not a function (Chrome)
TypeError: confirm is not a function (Firefox)
TypeError: 'a string, this could vary' is not a function (evaluating 'confirm("your question")') (Safari)
TypeError: Function expected (IE)
```

原文出处:[lambdatest.com](https://goo.gl/Xn3QP1)