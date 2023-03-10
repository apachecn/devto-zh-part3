# 封闭银行有限公司-JS 银行系统

> 原文：<https://dev.to/yashwanth2804/closure-bank-ltd---a-js-banking-system---5g8>

### 嗨 Devs，

当我学习 JavaScript 概念的时候，我很难理解 **clo `sure` s** ，当然一些初学者也可能面临这个问题。

> 什么是终结？

闭包是一个函数与对其周围状态(词法环境)的引用捆绑在一起(封闭)的组合。换句话说，闭包允许您从内部函数访问外部函数的范围。在 JavaScript 中，闭包是在每次创建函数时创建的。

*摘自[中篇](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36)*

[![](img/638a4ca9942a038e2f1f92cf35a5050f.png)](https://i.giphy.com/media/3lx7OMPseuokgfzCK2/giphy.gif)

所以随着我的理解和一些精力的投入，我做了一个例子

如果我说错了，请纠正我

### 关于例子

在这个场景中，我爷爷带来了 1000 BTC🤑并且存到了比特币银行，后来他花了一些 BTC (200)剩下的给了我爸，后来我爸把豪斯(300 BTC)也给了我。我捐了(499 BTC)给`DEV.to`😇

### JavaScript 中闭包的基本例子:

`// hidden setup JavaScript code goes in this preamble area` `function sum(a) { return function(b) { return a + b; // takes "a" from the outer lexical environment }; } // method 1 console.log( sum(1)(2) ); // 3 /// method 2 var call_1 = sum(1); var call_2 = call_1(4); console.log( call_2 ); // 5`

在上面的例子中，考虑方法 2，首先我们调用`sum`函数并在该函数范围内设置`a`值(通过作为参数传递)。

> var call _ 1 = sum(1)；

存储在变量`call_1`中的返回函数，如果我们记录 call_1，我们将看到这个

```
function(b) {
  return a + b; // takes "a" from the outer lexical environment
} 
```

现在把变量`call_1`看作一个典型的函数，现在我们可以通过调用 call_1(4)来调用它，这将给出两个数字的和，但是等一下我们知道当作用域/函数结束时，函数作用域变量死亡，对吧。那么为什么`call_1`设法得到直接父函数中定义的变量，即使在父函数已经返回之后，所以它应该已经死了。

[执行上下文](https://hackernoon.com/execution-context-in-javascript-319dd72e8e2c)，请参考本站。

根据 it EC，`Scope chain`持有自己的变量对象、外部函数的变量对象和全局执行上下文变量。

这就是当调用`call_1()`时，为什么`var a`可用的原因。确保它获取的是外部函数变量的引用而不是实际值。

### 这里是封行有限公司的代码

`// hidden setup JavaScript code goes in this preamble area` `var BTCbank = function(BTC_balanace) { return grandpa = (moneyspent) => { // grandpa BTC_balanace -= moneyspent; return dad = (moneyspent) => { // dad BTC_balanace -= moneyspent; return me = (moneyspent) => { // me return BTC_balanace -= moneyspent; } } } }; // step-1 // deposit 1000 to BTCbank var bankBalance = BTCbank(1000); // step-2 // now grandpa spending amount is 300 var afterGrandpaSpending = bankBalance(300); // step-3 // now grandpa spending amount is 200 var afterDadSpending = afterGrandpaSpending(200); // step-4 // now grandpa spending amount is 499 var aftermyDonation = afterDadSpending(499); console.log(aftermyDonation); /// simple way /// console.dir(BTCbank(1000)(400)(100)(499));`

### 让我们来分解代码

第一步:
当我们调用函数`BTCbank(BTC_balance)`时，我们只是将`BTC_balance`的 var 初始化为`1000`，并将函数`grandpa`返回给 var `bankBalance`。

`console.log(bankBalance)`