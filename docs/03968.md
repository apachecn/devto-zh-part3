# ES6:箭头功能

> 原文：<https://dev.to/epicosity/es6-arrow-functions-299j>

箭头函数是用于编写 JavaScript 函数的较新语法。箭头函数语法是通过 ES6 声明引入 JavaScript 的。它们肯定是 ES6 最受欢迎的特性之一，而且随着时间的推移，只会越来越多。即使你不打算使用箭头函数，你也应该学习它们是什么以及如何阅读它们。

### 语法

箭头函数可以归结为这样的语法:

```
// ES5

function nameOfFunction(parameters) {
  statements
}

// ES6

(parameters) => { statements } 
```

要创建一个箭头函数，你首先需要写一个 ES5 风格的函数。我们将使用一个使数字加倍的函数作为例子。

```
const double = function(num){
  return num * 2;
}

double(5); // 10 
```

接下来，您将删除`function`关键字。

```
const double = (num){
  return num * 2;
} 
```

最后，您将在花括号前的参数列表后放置一个粗箭头= >

```
const double = (num) => {
  return num * 2;
} 
```

就这样，你创建了你的第一个箭头函数。现在我们可以开始清理了。当 arrow 函数只有一个 JavaScript 表达式时，你可以去掉函数体的花括号{}，去掉关键字`return`，把函数放在一行上。

```
const double = (num) => num * 2; 
```

我们可以省去回车和花括号，因为箭头函数有一个隐式的回车。这意味着将返回函数右侧的结果。

这还不是全部。我们可以进一步简化箭头函数。如果您的函数只有一个参数，那么您可以省略参数周围的括号和函数体后面的分号。

```
const double = num => num * 2

double(6); // 12 
```

如果函数没有参数，那么只有一对空括号。

```
const helloWorld = () => {
  console.log('Hello World!');
}

helloWorld() // Hello World! 
```

### 词法本

ES6 arrow 函数语法允许您在函数声明中省略函数、返回和花括号，从而简化了代码。这太棒了，但是使用 arrow 函数的主要好处是如何处理关键字`this`。

关于这一点的更多信息，请查看栈溢出的这篇文章。

在经典函数表达式中，关键字`this`在对象方法中是指对象本身。对于 arrow 函数，`this`关键字不是自己定义的，而是从封闭范围继承的。

下面的例子将说明我的意思。

```
let person = {
  firstName: 'Donald',
  lastName: 'Glover',
  titles: ['actor', 'comedian', 'writer', 'producer', 'director', 'rapper', 'singer, 'DJ'],
  fullName: function(){
    return `${this.firstName} ${this.lastName}`
  }
}

person.fullName(); // Donald Glover 
```

在 fullName 函数中，this.firstName 和 this.lastName 引用 person 对象。让我们用箭头函数做同样的事情，看看会发生什么。

```
let person = {
  firstName: 'Donald',
  lastName: 'Glover',
  titles: ['actor', 'comedian', 'writer', 'producer', 'director', 'rapper', 'singer, 'DJ'],
  fullName: () => `${this.firstName} ${this.lastName}`
}

person.fullName(); // undefined undefined 
```

当在对象方法中使用箭头函数时，这是不受限制的。因此，在这种情况下，在调用堆栈中查找这个值，然后调用堆栈在窗口对象中查找它。箭头函数不应用作对象方法。这就引出了我的下一个观点。

### 何时避免箭头功能？

正如你在上面的例子中看到的，箭头函数不适合对象方法。另一个不适合使用箭头函数的用例是带有动态上下文的回调函数，或者 onclick 侦听器。

```
const button = document.getElementById('myButton');

button.addEventListener('click', ()=> {
  this.classList.toggle('active');
}); 
```

我们将在点击按钮时切换一个类。eventListener 未绑定到按钮，但将绑定到窗口对象。

其他应该避免使用箭头函数的用例是构造函数、生成器和带有参数的对象。我将在以后的博客文章中讨论这些话题。

### 何时使用箭头功能？

包括我自己在内的许多人都喜欢将 arrow 函数与 reduce 和 map 之类的数组辅助方法一起使用，因为这样可以使代码更短，可读性更好。这个总的规则在这个[帖子](https://stackoverflow.com/questions/22939130/when-should-i-use-arrow-functions-in-ecmascript-6)中有所阐述。

> *在全局作用域和 Object.prototype 属性中使用函数。
> 
> 使用类作为对象构造函数。
> 
> 别处使用= >。*

### 结论

虽然箭头函数不能完全取代经典函数，但我们仍然应该学习和使用箭头函数。它们为我们提供了更短、更易读的语法；最重要的是，它们解决了绑定`this`的问题。

* * *