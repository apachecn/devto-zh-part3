# 箭头功能

> 原文：<https://dev.to/banesag/arrow-functions-16fk>

[![alt](img/db2a97c5aadfbc345c936fbe3e23d71c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VQN3Eu1R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d4kx8m9n7g262k5juxb7.jpg)

### 箭头功能

箭头函数是在 ES6 中引入的，它使用了一种称为**箭头**语法的新语法来声明函数。这种新语法使用了更少的冗余语法。

箭头函数可以很容易地通过箭头符号“= >”来识别，这也是它得名的原因。作为编写箭头函数的语法和规则的一部分，参数在箭头元素之前，函数的主体在后面。

箭头函数总是匿名的，你需要把它们赋给一个变量，这样你就可以引用它们了。

**优势**

与其他函数声明方法相比，使用箭头函数有许多优点:

-它们不那么冗长
-不需要为单个参数
使用括号-如果函数体只有一行
，则不需要将函数体放在块内-如果返回语句是函数体中唯一的语句，则不需要返回关键字
-它们不将自己的值**这个**绑定到函数

示例:

```
const square = x => x*x; 
```

在此示例中,“x”不需要放在括号中，因为只有一个参数；多个参数需要放在括号内。

有两个参数的例子:

```
const add = (x,y) => x = y; 
```

但是如果函数不需要任何参数，可以在箭头符号前使用空参数:

```
const hello = ( ) => alert(‘Hello World!’); 
```

在所有这些例子中，函数都在一行中，所以没有必要把它们放在一个块中。但是对于更长的函数，你将需要使用花括号来定义函数体和末尾的返回键。

```
const tax = (salary) => {
const taxable = salary – 5000;
const lowerRate = 0.25 * taxable;
taxable = taxable -15000;
const higherRate = 0.4 * taxable;
return lowerRate + higherRate;
} 
```

正如你所看到的，当在一个更长的函数中使用箭头函数时，使用箭头函数的好处就失去了。箭头函数更适合简短的匿名函数。