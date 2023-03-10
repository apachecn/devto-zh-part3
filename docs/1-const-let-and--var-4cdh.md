# 1.let，const 和...定义变量

> 原文：<https://dev.to/blueturtle/1-const-let-and--var-4cdh>

ES6 引入了一些新的语法特性。其中一个是关键字 [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const) ，[让](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)来声明变量。先说说为什么他们比 [var](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var) 更受青睐。

### **范围**🔭

`var`有一个功能[范围](https://developer.mozilla.org/en-US/docs/Glossary/Scope)。这意味着在函数中的任何地方都可以访问它。请看这个例子:

```
function buyHerChocolate(onDiet) {
    if (onDiet) {
        var decision = "Don't do it!";
    } else {
        var decision = "Go buy it!"
    }
    console.log(decision);
    }
buyHerChocolate(true);//"Don't do it!"
console.log(decision);//Uncaught ReferenceError: decision is not defined 
```

如你所见，用`var`定义的*决策*变量在函数范围内是可用的，但是当我们试图在函数外记录它时，控制台抛出一个错误`decision is not defined`，就好像它从来不存在一样。

相反，`let`和`const`有阻挡`{}`的作用域。

```
function buyHerChocolate(onDiet) {
    if (onDiet) {
        let decision = "Don't do it!";
        console.log(decision);
    } else {
        let decision = "Go buy it!"
        console.log(decision);
    }
    console.log(decision);
    }
buyHerChocolate(true);
//"Don't do it!"
//Uncaught ReferenceError: decision is not defined
console.log(decision);//Uncaught ReferenceError: decision is not defined 
```

惊喜！在表达式块中记录 decision 的值会产生预测的字符串，而在块抛出错误和函数范围之外进行同样的操作也会抛出错误。

同样的情况也发生在`const`身上。

* * *

### **吊装**⏫

用`var`声明的变量被[提升](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)到它们作用域的顶部。重要的是要注意变量声明是被托管的而不是赋值。

```
console.log(x);
var x = 5;//undefined 
```

发生了什么事？！在提升任何一个`var`变量之前，不会执行`console.log()`功能。因此，`var x`；会上升到全局范围的顶端。然后执行 console.log(x)并未定义日志，因为 x 在那时没有值。x 被赋值为 5。会是这样的:

```
var x;
console.log(x);
x = 5; 
```

因此，如果我们在那之后`console.log(x)`，它会将 5 记录到控制台。这种古怪的行为会在更大的程序中引入错误。

`let`和`const`不吊。

```
console.log(x);
const x = 5;//Uncaught ReferenceError: x is not defined 
```

* * *

### **申报&赋值** ✒️

在同一个范围内，变量可以被多次重新声明和重新分配不同的值。
`let`变量不能重声明，但可以在相同的作用域内重新赋值。
`const`变量不能在同一个作用域内重新声明或重新赋值。除此之外，还必须同时声明它们并给它们赋值。所以我们不能这么做:

```
const y;//Uncaught SyntaxError: Missing initializer in const declaration 
```

但是我们必须这么做:

```
const y = 5; 
```

所以如果你的变量改变了值，使用`let`来声明它，如果不总是使用`const`。

它们和`var`之间的这些差异将防止命名冲突。

* * *

### **结论:**

由于上述原因，除了变量被重新分配新值时，你应该在所有情况下使用`const`。在这种情况下，使用`let`代替。大多数文章建议开发者避免使用`var`。为什么还会有人用`var`呢？！

关于那件事，你同意我的看法吗？请留言告诉我你的看法。

* * *

***进一步阅读:***

📌[JavaScript 中函数和块作用域的区别](https://medium.com/@josephcardillo/the-difference-between-function-and-block-scope-in-javascript-4296b2322abe)

📌[揭秘 JavaScript 可变范围提升](https://www.sitepoint.com/demystifying-javascript-variable-scope-hoisting/)

📌[JavaScript 中的提升是什么？](https://medium.com/javascript-in-plain-english/https-medium-com-javascript-in-plain-english-what-is-hoisting-in-javascript-a63c1b2267a1)