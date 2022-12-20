# Javascript 算术备忘单

> 原文：<https://dev.to/wangonya/javascript-arithmetic-cheat-sheet-265h>

鉴于发明计算机的主要原因之一是为了快速解决数学问题，难怪所有现代编程语言都有如此丰富的面向算术的方法。最早的计算机基本上只是计算器。(*是的，我在看着你[算盘](https://en.wikipedia.org/wiki/Abacus)* )。如果你对 Javascript 有所涉猎(偶尔也会学一点数学)，我真的希望你会觉得这很有用。像简单的加法(+)和减法(-)这样非常明显的运算被省略了。所以有更高级的操作。

[![meme](img/95fe147ad44233de721e03f78e9f2745.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rAbmpNto--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.pinimg.com/736x/4f/9b/15/4f9b15222b111831de64397a02297128--funny-teacher-memes-math-memes.jpg)

## 处理常量

以对数为底*e*T2】

```
Math.E; // 2.718281828459045 
```

Enter fullscreen mode Exit fullscreen mode

以 10 为底的对数

```
Math.LN10; // 2.302585092994046 
```

Enter fullscreen mode Exit fullscreen mode

以 2 为底的对数

```
Math.LN2; // 0.6931471805599453 
```

Enter fullscreen mode Exit fullscreen mode

以 10 为底的对数 *e*

```
Math.LOG10E; // 0.4342944819032518 
```

Enter fullscreen mode Exit fullscreen mode

以 2 为底的对数 *e*

```
Math.LOG2E; // 1.4426950408889634 
```

Enter fullscreen mode Exit fullscreen mode

🥧

```
Math.PI; // 3.141592653589793 
```

Enter fullscreen mode Exit fullscreen mode

1/2 的平方根

```
Math.SQRT1_2; // 0.7071067811865476 
```

Enter fullscreen mode Exit fullscreen mode

2 的平方根

```
Math.SQRT2; // 1.4142135623730951 
```

Enter fullscreen mode Exit fullscreen mode

无穷大

```
Infinity; // Infinity 
```

Enter fullscreen mode Exit fullscreen mode

更新:如[@ oscheller](https://dev.to/oscherler)在评论中澄清，

> 关于无穷的使用，我认为它在你比较结果的时候很有用。如果你做了类似 if(1/x > 1/y)的操作，而 x 或 y 中的一个结果是 0，那么比较仍然有效。

## 四舍五入

`Math.round`返回舍入到最接近的整数的数值。

```
Math.round(4.2); // 4
Math.round(4.7); // 5
Math.round(4.5); // 5\. Half-way values are always rounded up
Math.round(-4.5); // -4 
```

Enter fullscreen mode Exit fullscreen mode

说到四舍五入，`Math.ceil()` :

```
Math.ceil(4.2); // 5
Math.ceil(4.7); // 5
Math.ceil(-4.7); // -4\. Ceiling a negative number will round towards zero 
```

Enter fullscreen mode Exit fullscreen mode

`Math.floor()`向下舍入:

```
Math.floor(4.2); // 4
Math.floor(4.7); // 4
Math.floor(-4.7); // -5\. Flooring a negative number will round away from zero 
```

Enter fullscreen mode Exit fullscreen mode

## 模数(%)

返回(整数)除后的余数。

```
42 % 10; // 2
-40 % 10; // -0 🤔 
```

Enter fullscreen mode Exit fullscreen mode

## 三角学

Sine

```
Math.sin(60); // -0.3048106211022167 
```

Enter fullscreen mode Exit fullscreen mode

余弦值

```
Math.cos(60); // -0.9524129804151563 
```

Enter fullscreen mode Exit fullscreen mode

切线

```
Math.tan(60); // 0.320040389379563 
```

Enter fullscreen mode Exit fullscreen mode

## 递增(++)

`++`将其操作数增加 1。

```
// postfix: returns the value before incrementing
let a = 4, // 4
  b = a++, // 4
  c = a; //5 
```

Enter fullscreen mode Exit fullscreen mode

```
// prefix: returns the value after incrementing
let a = 4, // 4
  b = ++a, // 5
  c = a; //5 
```

Enter fullscreen mode Exit fullscreen mode

## 递减(-)

将操作数减 1。

```
// postfix: returns the value before decrementing
let a = 4, // 4
  b = a--, // 4
  c = a; //3 
```

Enter fullscreen mode Exit fullscreen mode

```
// prefix: returns the value after decrementing
let a = 4, // 4
  b = --a, // 3
  c = a; //3 
```

Enter fullscreen mode Exit fullscreen mode

## 求幂运算(**)

```
// Math.pow() or ** can be used
let a = 4,
  b = 2,
  c = Math.pow(a, b), // 16
  d = a ** b; // 16 
```

Enter fullscreen mode Exit fullscreen mode

## 获取最大值和最小值

```
Math.max(4.2, 4.7); // 4.7
Math.min(4.2, 4.7); // 4.2 
```

Enter fullscreen mode Exit fullscreen mode

从数组中获取最大值和最小值:

```
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9],
  max = Math.max(...arr), // 9
  min = Math.min(...arr); // 1 
```

Enter fullscreen mode Exit fullscreen mode

## 求根√

平方根

```
Math.sqrt(16); // 4 
```

Enter fullscreen mode Exit fullscreen mode

立方根

```
Math.cbrt(27); // 3 
```

Enter fullscreen mode Exit fullscreen mode

要找到 n 次方根，使用 Math.pow()函数并传入一个分数指数。

```
// This finds the 6th root of 64
Math.pow(64, 1 / 6); // 4 
```

Enter fullscreen mode Exit fullscreen mode

通过组合这些操作中的一个或多个，可以完成复杂得多的计算。