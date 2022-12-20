# 理解 javascript 中的闭包

> 原文：<https://dev.to/delph/understanding-closures-in-javascript-8fh>

## 关闭

闭包是在定义函数时创建的。闭包包含了函数可以访问的所有东西。

```
var g = "G";

function functionA() {
  var a = "A";

  function functionB() {
    var b = "B";
    console.log(a, b, g);
  }

  functionB(); // prints A, B, G

  a = "Batman";
  functionB(); // prints Batman, B, G
}

functionA(); 
```

在上面的例子中，当 functionB 被定义时，它创建的闭包赋予它对全局变量‘g’、变量‘a’和变量‘b’的访问权。

注意，闭包给出了对变量的引用，而不是变量的副本。因此，如果变量“a”的值被改变，并且再次调用 functionB，将打印“a”的新值而不是旧值。

闭包是函数式编程范式中一个非常重要的概念。它允许函数记住定义它的上下文。

当一个函数返回另一个函数时，闭包的概念变得更加相关。返回的函数可以访问不在全局范围内的变量，但只存在于它的闭包 ie 中。私有变量。

在上面的例子中，变量“a”在函数中被认为是私有的，只能由函数 b 访问。

在函数式编程中，闭包也经常用于局部应用和 currying。

[![alt text](img/68899a8389051519d6e3165b0bc63492.png "random curry photo")](https://res.cloudinary.com/practicaldev/image/fetch/s--iM4soKEJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ichef.bbci.co.uk/food/ic/food_16x9_832/recipes/fish_curry_09718_16x9.jpg)

> 部分应用:将一个函数应用于它的一些参数的过程。(即。接受多参数函数并返回少参数函数的函数。)
> 
> Currying:以一个有多个参数的函数作为输入，返回一个只有一个参数的函数。

下面是另一个例子:

```
function adder(a) {
  function add(b) {
    return a + b;
  }

  return add;
} 
```

```
var addOne = adder(1); // a is 1, addOne has a reference to add(b)
var addTen = adder(10); // a is 10

addOne(5); // 1 (a) + 5 (b) = 6
addTen(5); // 10 (a) + 5 (b) = 15 
```

在 C 和大多数其他通用语言中，函数返回后，局部变量不再可访问，因为堆栈框架被破坏了。

在 JavaScript 中，如果在另一个函数中声明一个函数，内部函数可以记住并访问外部函数的变量和参数，即使外部函数已经返回。

#### 作进一步阅读...

<sub>[https://medium . com/JavaScript-scene/master-the-JavaScript-interview-what-is-a-closure-b2f0d 2152 b 36](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36)</sub>
<sub>[https://medium . com/daily js/I-never-understand-JavaScript-closures-9663703368 E8 E8](https://medium.com/dailyjs/i-never-understood-javascript-closures-9663703368e8)</sub>
<sub>[https://medium . freecode camp . org/whats-a-a](https://medium.freecodecamp.org/whats-a-javascript-closure-in-plain-english-please-6a1fc1d2ff1c)</sub>