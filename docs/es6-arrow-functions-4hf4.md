# ES6 箭头功能

> 原文：<https://dev.to/hellomeghna/es6-arrow-functions-4hf4>

它们是用于创建函数的下一代 JavaScript 语法。

普通 JavaScript 函数语法:

```
function myFunc() {
    ...
} 
```

ES6 胖箭头函数语法:

```
const myFunc = () => {
    ...
} 
```

## 为什么我们更喜欢 ES6 的箭头功能？

他们可以缩短 JavaScript 函数语法，因为:

*   省略`function`关键字
*   解决了 JavaScript 中关键字`this`的许多问题。
    *   当我们给一个对象添加方法时，关键字很重要。
    *   箭头函数绑定了它们的上下文，在运行时不会改变，所以`this`实际上指的是原始上下文。

## 练习时间！

```
function printMyName(name) {
    console.log(name);
}
printMyName('Meggie'); //Meggie 
```

改写为:

```
const printMyName = (name) => {
    console.log(name);
}
printMyName('Meggie'); //Meggie 
```

### 我们还可以把语法再缩短一些！

#### 如何？

*   如果我们只传递一个参数，我们也可以省略括号来缩短语法。

```
const printMyName = name => {
  console.log(name);
}
printMyName('Meggie'); 
```

同样，看另一个函数-

```
const multiplyNum = num => {
    return num*2
}
console.log(multiplyNum(5)); //10 
```

*   如果只有一行代码返回某些东西，我们也可以省略花括号`{}`和`return`关键字，将整个代码放在一行中！

```
const multiplyNum = num => num*2;
console.log(multiplyNum(5)); //10 
```

## 结论

ES6 箭头函数是用更少的代码行编写 JavaScript 函数的一种很酷的新方法。
[![Happy Comic Face](img/cd258f9445af60103e9a84ed2d51ff47.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--sG7-DtRq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://adage.cimg/bin/image/spongebob_1.jpg)