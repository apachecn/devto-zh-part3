# Javascript 中的范围

> 原文：<https://dev.to/torianne02/scope-in-javascript-4ang>

最初，我写了一篇关于 Javascript 中变量提升的博文，但是很快，我意识到我首先需要解释 Javascript 中的作用域。为了充分理解吊装的主题，理解范围是至关重要的。所以，我在这里写了一篇关于 Javascript 范围的博文。我们开始吧！

在 ES6 之前，Javascript 中只有两种类型的范围:

*   本地/功能范围
*   全球范围

ES6 为 Javascript 引入了一种新的作用域，它是

*   块范围

在我们深入每种类型的作用域的区别之前，让我们讨论一下什么是最基本的作用域。作用域本质上决定了何时何地可以访问和不可以访问变量。换句话说，变量的“范围决定了可访问性”。

让我们一次一个地深入了解每种类型的作用域的复杂性。

### 全局范围

在函数外部声明的变量是全局变量，存在于全局范围内。这意味着“网页上的所有脚本和函数都可以访问它。”更简单地说，你可以在程序的任何地方使用这个变量。

```
var shape = “circle”;
console.log(shape); // “circle”

function changeShape() {
  shape = “triangle”;
}
changeShape();
console.log(shape); // “triangle” 
```

### 局部/功能范围

当一个函数被定义时，全局范围内的所有变量对它都是可用的。一个函数也可以访问它内部声明的变量，这是局部/函数范围。因此，在函数中声明的变量被称为局部变量，只存在于局部/函数范围内。

```
function exampleFunction() {
  var color = “red”;
  console.log(color); // “red”
}

exampleFunction();
console.log(color); // ReferenceError: color is not defined 
```

### 封锁范围

块范围是由 ES6 引入的，它使用`let`和`const`来声明变量。Block 特指花括号内的任何东西，`{}`。块的例子有 if 语句或函数。所以现在，函数不是唯一能够创建自己范围的东西。

说到功能，`let`和`const`的工作方式与`var`相似。

```
function myFunc() {
  let animal = “dog”;
  console.log(animal); // “dog”
}

myFunc();
console.log(animal); // ReferenceError: animal is not defined 
```

#### 块范围变得混乱的地方

如果同一个变量名被使用两次，那么块范围就会变得混乱。当使用`var`声明两个变量(这不是块范围)时，第一个声明被覆盖。

```
var food = “pizza”;

if (true) {
  var food = “ramen”;
  console.log(food); // “ramen”
}

console.log(food); // “ramen” 
```

当通过使用`let`使用块范围时，第一个声明保持不变，第二个声明在块外不可用。

```
let food = “pizza”;

if (true) {
  let food = “ramen”; // “ramen”
}

console.log(food); // “pizza” 
```

虽然不建议对两个变量使用相同的名字，但是如果你这样做的话，请确保使用`let`来声明你的变量，否则你会得到一些非常混乱的代码和结果。

我希望这篇博文对那些不熟悉 Javascript 范围的人有所帮助！快乐编码。

### 资源

[ES6: var、let 和 const——函数作用域和块作用域之战](https://www.deadcoderising.com/2017-04-11-es6-var-let-and-const-the-battle-between-function-scope-and-block-scope/)

[理解 Javascript 中的提升](https://codeburst.io/understanding-hoisting-in-javascript-c8d35d5db2c2)

[Javascript 范围](https://www.w3schools.com/js/js_scope.asp)

[Javascript 中函数和块作用域的区别](https://medium.com/@josephcardillo/the-difference-between-function-and-block-scope-in-javascript-4296b2322abe)