# JavaScript 是数字检查变量在 JavaScript 中是否是数字

> 原文：<https://dev.to/skptricks/check-if-variable-is-a-number-in-javascript-1f10>

Post 链接:[检查 JavaScript 中的变量是否为数字](https://www.skptricks.com/2018/11/check-if-variable-is-number-in-js.html)

本教程解释了如何在 javascript 中检查变量是否为数字。让我们看看下面的例子，我们使用了 typeof 运算符和 isNaN()函数来验证变量是否为数字。
type of–如果变量是一个数字，它将返回一个名为“数字”的字符串。
isNaN()–代表“不是数字”，如果变量不是数字，则返回 true，否则返回 false。

使用 typeof 运算符:
在本例中，我们使用了 typeof 运算符，并在 javscript 中验证变量是否为数字。
//在这个变量中我们存储的是数字...
var num 1 = 758；

//使用 typeof 运算符
验证 if(type of num 1 = = ' number '){
console . log(num 1+"为数字，");
}else{
console.log(num1 +"不是数字。");
}

## 输出:

> “758 是一个数字。”

[![](img/42c5bd1e8e7d514b67915a8401241303.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O_783St_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://4.bp.blogspot.com/-xW2C8rFYXro/XADFQT2KHtI/AAAAAAAACL4/xJuhnvzLu2kISQKmNCUsyrQLLj31wzcIQCLcBGAs/s400/variable.png)