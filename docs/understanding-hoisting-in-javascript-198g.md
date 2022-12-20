# 理解 JavaScript 中的提升

> 原文：<https://dev.to/smithmanny/understanding-hoisting-in-javascript-198g>

新的 JavaScript 开发人员面临的一个问题是理解变量和函数是“悬挂的”。虽然一些开发人员希望提升发生，但其他人面临着意想不到的结果，不明白为什么。

一、什么是吊装？

```
// Outputs: undefined
console.log(car);
var car = 'Audi'; 
```

根据定义，提升就是“举起或举起”，这正是 JavaScript 在运行我们的代码时所做的。例如，JavaScript 会将上面的代码片段读作:

```
var car;

// Outputs: undefined
console.log(car);

// JavaScript sets our car variable afterwards
car = 'Audi'; 
```

正如你所看到的，我们的变量 car 是在文件的顶部声明的，其值被设置为“undefined”。然后，一旦 JavaScript 到达我们的变量，它会将该变量重新分配给我们传递给它的对象。

你可能会问自己，函数呢？函数也被提升，但是有一个限制。与变量不同，函数实际上提升了整个函数定义，但前提是它是一个函数声明。对于函数表达式，变量本身会被提升，但实际的函数定义不会。

```
// Outputs: 'slyderz.co'
someFunc() 

function someFunc() {
    return 'slyderz.co'
}

// Outputs: site is undefined
site();

var site = function someFunc() {
    return 'slyderz.co'
} 
```

目前就这些了😄。
你可以在 Twitter/IG 上找到我:[@史密斯曼尼](https://dev.to/smithmanny)