# Javascript 的词法变量作用域

> 原文：<https://dev.to/wangonya/til-lexical-variable-scoping-with-javascript-48kk>

在 Javascript 中，代码块是用花括号({})创建的。例如:

```
someFunction() {
    // some code here
}

anotherFunction() {
    // some more code here
} 
```

Enter fullscreen mode Exit fullscreen mode

`someFunction`和`anotherFunction`是两个不同的码块。这两个不同的代码块也可以被认为是两个不同的范围。这意味着在`someFunction`中声明的变量只影响那段代码，而在`anotherFunction`中声明的变量只影响那段代码。在这个意义上，他们是*【范围】*。
举例说明

```
var name = "Kenny"

someFunction() {
    var name = "Kyle"
    console.log("someFunction block:", name) // someFunction block: Kyle
}

anotherFunction() {
    var name = "Timmy"
    console.log("anotherFunction block:", name) // anotherFunction block: Timmy
}

console.log("global", name) // global Kenny 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，同一个变量`name`保留了它的全局值，尽管它在`someFunction`和`anotherFunction`中被重新定义。

这就是事情变得有点棘手的地方。有了这些知识，我们就很容易假设，每当我们有代码块时，情况总是如此。除此之外，它与`if/else`语句和`for`循环的工作方式不同。

```
var name = "Kenny";

if (name) {
  var name = "Kyle";
  console.log("if block:", name); // if block: Kyle
}

console.log("global", name); // global Kyle 
```

Enter fullscreen mode Exit fullscreen mode

`if`模块中的`name`变量重置`name`的值。同样的事情发生在`for`循环中:

```
var name = "Kenny";

for (var i = 0; i < 1; i++) {
  var name = "Kyle";
  console.log("for loop block:", name); // for loop block: Kyle
}

console.log("global", name); // global Kyle 
```

Enter fullscreen mode Exit fullscreen mode

解决方法是用`let`关键字代替`var`。

```
var name = "Kenny";

if (name) {
  let name = "Kyle";
  console.log("if block:", name); // if block: Kyle
}

console.log("global", name); // global Kenny 
```

Enter fullscreen mode Exit fullscreen mode

```
var name = "Kenny";

for (var i = 0; i < 1; i++) {
  let name = "Kyle";
  console.log("for loop block:", name); // for loop block: Kyle
}

console.log("global", name); // global Kenny 
```

Enter fullscreen mode Exit fullscreen mode