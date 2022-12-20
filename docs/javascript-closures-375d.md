# Javascript 闭包

> 原文：<https://dev.to/10secondsofcode/javascript-closures-375d>

一个**闭包**是一个内部函数，它可以访问外部(封闭)函数的变量——作用域链。闭包有三个作用域链:
——它可以访问自己的作用域(在花括号中定义的变量)。
-它可以访问外部函数的变量。
-它可以访问全局变量。

闭包意味着内部函数总是可以访问外部函数的变量和参数，即使外部函数已经返回。

内部函数可以访问外部函数的变量和参数。它有助于隐藏 JavaScript 中的实现细节。

```
function showName (firstName, lastName) {
    var nameIntro = "Your name is ";
    // this inner function has access to the outer function's variables, including             the parameter
    function makeFullName () {       
        return nameIntro + firstName + " " + lastName;   
    }

    return makeFullName ();
}

showName ("Michael", "Jackson"); // Your name is Michael Jackson 
```

```
function OuterFunction() {

    var outerVariable = 100;

    function InnerFunction() {
        alert(outerVariable);
    }

    return InnerFunction;
}
var innerFunc = OuterFunction();

innerFunc(); // 100

function Counter() {
    var counter = 0;

    function IncreaseCounter() {
        return counter += 1;
    };

    return IncreaseCounter;
}

var counter = Counter();
alert(counter()); // 1
alert(counter()); // 2
alert(counter()); // 3
alert(counter()); // 4 
```

在上面的例子中，返回 InnerFunction 调用 OuterFunction()时，从 OuterFunction 返回 InnerFunction。变量 innerFunc 只引用 InnerFunction()，而不引用 OuterFunction()。所以现在，当你调用 innerFunc()时，它仍然可以访问在 OuterFunction()中声明的 outerVariable。这就叫完结。

**休息:**[https://github . com/10 second of ofcode/10 second of ofcode](https://10secondsofcode.github.io/10secondsofcode/)

# javascript，# 10 秒代码，#闭包