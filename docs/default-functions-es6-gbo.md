# 默认功能参数(ES6)

> 原文：<https://dev.to/runosaduwa/default-functions-es6-gbo>

在 ES5 时代，要设置默认值，你可以这样写代码:

```
function getSum(a, b){
 a = (a !== undefined) ? a:1;
 b = (b !== undefined) ? b:41;
 return a + b;
}
 getSum() //42
 getSum(1,2) //3 
```

或者

```
function getSum(a, b){
 a = a || 1;
 b = b || 41;
 return a + b;
}
 getSum() //42
 getSum(1,2) //3 
```

这些都是很好的解决方案，但是嘿，现在是 2019 年，现代 JavaScript 来拯救我们了。现在，您可以为函数声明语句本身中的参数设置默认值，如下所示:

```
function getSum(a = 1, b = 41){
 return a + b;
}
 getSum() //42
 getSum(1,2) //3 
```

如果不指定参数，将使用参数的默认值。

与旧的设置默认值的方法相比，ES6 提供的这个新特性更容易、更简洁。

编码快乐！❤