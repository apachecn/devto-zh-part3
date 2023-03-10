# JavaScript (ES5) -这个

> 原文：<https://dev.to/martyhimmel/javascript-es5---this-j9i>

*此文最初于 2017 年 1 月 16 日发布在我的网站 [https://martyhimmel.me](https://martyhimmel.me) 。像 dev.to 上的许多其他人一样，我决定将我的技术博客帖子转移到这个网站。*

在 JavaScript 中，`this`关键字可能有点难以理解。在我们深入讨论之前，我应该提一下严格模式，因为根据是否在严格模式下使用，`this`的行为会有所不同。

严格模式的核心是在 JavaScript 中实施更好的编码实践。浏览器解释 JavaScript 代码的方式有一些改变。因为严格模式的范围很容易成为它自己的教程或文章(它已经出现在许多网站上！)，我就不在这里赘述了。相反，我鼓励你阅读关于严格模式的 Mozilla 开发者文档，尤其是在继续本教程之前。

## `this`在全局范围内

`this`是对一个对象的引用。什么对象取决于调用`this`的上下文。

在全局范围内，无论是严格模式还是非严格模式，`this`都是对`window`对象的引用。每当提到全局范围时，它实际上是在谈论`window`对象。考虑这个例子:

```
var foo = 42;
console.log(foo); // 42
console.log(window.foo); // 42
console.log(this.foo); // 42 
```

Enter fullscreen mode Exit fullscreen mode

您在全局作用域中定义的任何变量或函数实际上都将其附加到了`window`对象上。所以，当你在全球范围内工作时，`this`则指的是`window`。如果你想看另一个例子，打开控制台，输入`console.log(window);`，然后输入`console.log(this);`，你会看到同样的输出。如果您创建任何变量或函数，然后运行这些语句中的任何一个，您将在记录的对象中看到这些变量/函数。

## `this`在一个物体中

这对于严格和非严格模式都是一样的。从上一节可以看出，全局范围实际上是一个顶级对象——`window`对象。也就是说，任何时候在一个对象内部调用`this`时，它通过引用它被调用的对象来完全相同地工作。

```
var person = {
  firstName: 'John',
  lastName: 'Smith',
  fullName: function() {
    return this.firstName + '  ' + this.lastName;
  }
};
console.log(person.fullName()); // John Smith 
```

Enter fullscreen mode Exit fullscreen mode

在`fullName`函数中，`this`是对容器对象- `person`的引用。`this.firstName`可以写成`person.firstName`。那为什么用`this`？假设在脚本中的某个地方有另一个同名的变量(`person`)。那么`person.firstName`指的是什么呢？根据代码的结构，它可能引用了错误的`person`对象。这就是`this`变得必不可少的地方——它只引用被调用的对象。

## 功能中的`this`

在上一节中，您已经看到了函数内部的`this`，但是该函数被包装在`person`对象中。但是当你有一个全局函数并使用`this`时会发生什么呢？这就是严格模式真正重要的地方。先看代码:

```
var fullName = 'Jane Doe';
function getName() {
    return this.fullName;
} 
```

Enter fullscreen mode Exit fullscreen mode

先来涵盖非严格模式。在非严格模式下，`this`是对上下文中最近的对象*的引用。在前一节中，`person`是函数上下文中最近的对象。*

如果你记得全局作用域实际上是`window`对象，那么全局函数中的`this`就变得更容易理解了。在`fullName`的例子中，函数在全局范围内，这意味着它是`window`对象的一部分。反过来，离函数最近的对象是`window`对象，所以`this`指的是`window`。而且由于`fullName`和`window.fullName`是一样的(因为在全局范围内)，全局函数内部的`this.fullName`引用的是全局变量。

现在我们来看看严格模式。在严格模式下，`this`是对它在执行上下文中绑定到的任何对象的引用。这意味着`fullName`和`window.fullName`之间有很大的不同。前者的执行上下文是函数，后者的执行上下文是`window`。

由于严格模式查看执行上下文而不是对象上下文，当调用`getName()`时，该函数抛出一个`Uncaught TypeError`。原因是`this`是执行上下文中的`undefined`。如果您在函数中添加一个`console.log(this);`语句，就可以看到这一点。

另一方面，如果调用`window.getName()`，函数在执行时被绑定到`window`对象。在这种情况下，函数正常工作，如果你在函数中记录`this`，它会记录`window`对象。

让我们进一步看看`this`在函数中是如何工作的。

对于构造函数类型的函数，`this`就像在对象中一样工作。我们将使用这个函数作为我们的基础:

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
  this.getName = function() {
    return this.firstName + '  ' + this.lastName;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

每当您使用`Person`函数创建一个新对象时，`this`就会被绑定到该对象的实例。它在严格和非严格模式下的工作方式是相同的。

```
var person = new Person('John', 'Smith');
console.log(person.firstName); // John
console.log(person.lastName); // Smith
console.log(person.getName()); // John Smith

var anotherPerson = new Person('Jane', 'Doe');
console.log(anotherPerson.firstName); // Jane
console.log(anotherPerson.lastName); // Doe
console.log(anotherPerson.getName()); // Jane Doe 
```

Enter fullscreen mode Exit fullscreen mode

由于`this`被绑定到单个实例，`person`有自己的`this`引用，而`anotherPerson`有自己的引用。

## `this`在自变量函数中

当您将一个函数作为参数传递给另一个函数时，事情变得有点棘手，比如在事件侦听器中。考虑一个按钮点击监听器:

```
// Using an anonymous function
document.getElementById('myButton').addEventListener('click', function() {
  console.log(this); // logs the button element (HTML)
});

// Using a declared function
document.getElementById('myButton').addEventListener('click', myClickListener);
function myClickListener() {
  console.log(this); // logs the button element (HTML)
} 
```

Enter fullscreen mode Exit fullscreen mode

无论是内联创建匿名函数还是传递声明的函数，无论是使用严格模式还是非严格模式，结果都是一样的。在上面的按钮 click listener 示例中，`this`引用了调用函数的对象——在本例中是按钮。

看起来不算太糟，对吧？让我们把它复杂化一点。如果你传递一个已经有自己的`this`引用的函数，会发生什么？例如，不是在按钮被按下时将`this`记录到控制台，而是我们想要记录`person`的全名(来自前面的例子)。

```
document.getElementById('myButton').addEventListener('click', function() {
  console.log(person.getName()); // John Smith
}); 
```

Enter fullscreen mode Exit fullscreen mode

在匿名函数版本中，它按照我们期望的方式工作。这是有意义的，因为我们是在对象上调用函数，而不是将它作为参数传递。让我们用方法作为一个参数。

```
document.getElementById('myButton').addEventListener('click', person.getName);
// undefined undefined 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，即使`getName`是`person`对象的一个方法，我们也不是直接在对象上调用函数，而是将它作为一个参数传递。不是`this`引用`person`对象，而是引用按钮元素。该按钮没有附加`firstName`或`lastName`属性，所以它返回`undefined`。

不过，有一个办法可以解决这个问题。JavaScript 有内置的`bind`函数来处理它。最简单的形式是，`bind`函数将`this`绑定到你传入的任何对象。

```
document.getElementById('myButton').addEventListener('click', person.getName.bind(person));
// John Smith 
```

Enter fullscreen mode Exit fullscreen mode

这意味着当在按钮的事件监听器的上下文中调用`person.getName`时，将`this`绑定到 person 对象。

## `this`在封

当涉及到`this`时，闭包有一个独特的行为。通常，内部函数可以访问外部函数的变量。而`this`就不是这样了。每个功能都有自己的`this`版本。考虑这个代码:

```
var person = {
  scores: [1, 2, 3, 4],
  getScores: function() {
    console.log(this);
    this.scores.forEach(function(score) {
      console.log(this);
      // do something
    });
  }
};
person.getScores(); 
```

Enter fullscreen mode Exit fullscreen mode

在`getScores`方法中，`this`具有可预测的行为——它引用了`person`对象(在严格和非严格模式下)。一旦我们进入`forEach`循环的内部函数，事情就会发生变化。

内部函数不能访问对象本身——只能访问包装/外部函数的变量和全局范围内的任何东西(`window`对象)。由于这种行为，您可以将该函数视为一个独立的函数(来自“函数”一节中的“`this`”)。在非严格模式下，`this`指的是`window`对象。在严格模式下，`this`是`undefined`。

那么我们该如何解决这个问题呢？在设置为`this`的外部函数中创建一个变量，以便内部函数可以使用该变量。

```
var person = {
  scores: [1, 2, 3, 4],
  getScores: function() {
    console.log(this);
    var that = this;
    this.scores.forEach(function(score) {
      console.log(that);
      // do something
    });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，`that`变量被赋给了外部函数中`this`的值——换句话说，就是`person`对象。在内部函数中的任何地方使用`that`都会产生与外部函数中的`this`相同的行为。

使用`var that = this;`或`var self = this;`是处理这种情况的常见做法。虽然这两者都很常见，但如果使用更简洁的变量名，可能会更容易理解。在这个例子中，`var personObject = this;`清楚地说明了你指的是什么。