# 理解 JavaScript 函数

> 原文：<https://dev.to/frugencefidel/understanding-javascript-functions-1f5b>

这篇文章最初发表在我的博客上。

## 是什么功能

函数是用于执行特定任务的一组语句。在执行重复任务时，函数非常有用。

例如，我们希望输出某些歌词。

```
// Declare functions
function verse1() {
  console.log('First verse goes here');
}

function verse2() {
  console.log('Second verse goes here');
}

function chorus() {
  console.log('Chorus goes here');
}

// Call them
// Use this pattern functionName()
verse1(); // First verse goes here
chorus(); // Chorus goes here
verse2(); // Second verse goes here
chorus(); // Chorus goes here 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，函数 chorus 可以重复任意多次。

## 定义功能

函数可以通过以下方式定义，即函数声明和函数表达式

```
// Function Declaration
function verse1() {
  console.log('First verse goes here');
}
verse1(); // First verse goes here

// Function Expression
let chorus = function() {
  console.log('Chorus goes here');
};
chorus(); // Chorus goes here 
```

Enter fullscreen mode Exit fullscreen mode

## 返回值

下面是如何从函数
输出结果

```
let chorus = function() {
  return 'Chorus goes here';
};
console.log(chorus()); // Chorus goes here

function sum() {
  return 1 + 1;
}
console.log(sum()); // 2 
```

Enter fullscreen mode Exit fullscreen mode

## 功能范围

如果在函数内部声明变量，它就不能泄漏到函数外部。但是函数可以访问外部变量(全局变量)。

```
const amOut = 'Coming from outside';

function testScope() {
  const amIn = 'Coming from inside';

  console.log(amOut);
  console.log(amIn);
}

console.log(testScope()); // Coming from outside, Coming from inside
console.log(amIn); // amIn is not defined 
```

Enter fullscreen mode Exit fullscreen mode

## 参数 vs 自变量

定义函数时使用参数，调用函数时使用自变量。我这边，实参是参数的值，而参数是实参的变量。两者都有助于接收输入。

```
// 'a' and 'b' are paremeters
function sum(a, b) {
  return a + b;
}

// 5 and 7 are arguments
console.log(sum(5, 7)); 
```

Enter fullscreen mode Exit fullscreen mode

## 默认函数参数

当参数未定义时，使用默认函数参数。

```
function sum(a = 5, b = 7) {
  return a + b;
}

// If all arguments are undefined, then pass nothing
console.log(sum()); // 12

// If all arguments are not undefined
console.log(sum(6, undefined)); // 13 
```

Enter fullscreen mode Exit fullscreen mode

## 休息功能参数

Rest 参数有助于传递任意数量的参数，不管函数是如何定义的。Rest 参数将参数收集到数组中。

```
// Without rest paremeters
function sum(a, b) {
  return a + b;
}

console.log(sum(5, 7, 6, 8)); // 12
console.log(sum()); // NaN

// With rest paremeters
function sum(...nums) {
  console.log(nums); // [5, 7, 6, 8]
  let total = 0;
  for (num of nums) {
    total += num;
  }
  return total;
}

console.log(sum(5, 7, 6, 8)); // 26
console.log(sum()); // 0
console.log(sum(5)); // 5
console.log(sum(5, 7)); // 12 
```

Enter fullscreen mode Exit fullscreen mode

## 高阶 vs 回调函数

高阶函数是将其他函数作为参数的函数，回调函数是作为参数传递给其他函数的函数。

```
function callback() {
  console.log('Coming from callback');
}

function highOrder(func) {
  console.log('Waiting for callback');
  func();
  console.log('Callback is called');
}

highOrder(callback);

// Waiting for callback
// Coming from callback
// Callback is called 
```

Enter fullscreen mode Exit fullscreen mode

## 匿名功能

匿名函数是没有名字的函数。

```
const anoyms = function() {
  console.log('I am Anonymous function');
};

setInterval(anoyms, 2000); 
```

Enter fullscreen mode Exit fullscreen mode

## 箭头功能

ES6 中引入了箭头函数，与表达式函数相比，它们的语法更短。箭头函数始终是匿名的和非绑定的' this '。

```
// Expression function
const sum = function(a, b) {
  return a + b;
};
console.log(sum(5, 7)); // 12

// In Arrow function
const sum1 = (a, b) => {
  return a + b;
};
console.log(sum1(5, 7)); // 12

// In Arrow function(Implicity return)
const sum2 = (a, b) => a + b;
console.log(sum2(5, 7)); // 12 
```

Enter fullscreen mode Exit fullscreen mode