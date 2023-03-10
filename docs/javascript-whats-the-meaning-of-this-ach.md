# JavaScript“这”是什么意思？

> 原文：<https://dev.to/moresaltmorelemon/javascript-whats-the-meaning-of-this-ach>

[![wtf mate](img/5686326577866eb4ebb050db36a6ec13.png)](https://i.giphy.com/media/yDBjbceDHdt6M/giphy.gif) 
好吧 JavaScript，什么鬼。我们很酷。现在，我只是不知道了。在过去的几个月里，我一直和 Ruby 在一起，我已经习惯了`self`的一贯和明智。现在我回来了，一切都好了...奇怪。

我最纠结的是 JavaScript 对`this`的实现是否是一流函数语言的必要之恶，或者只是我们坚持的另一个坏主意。

我将为你浓缩一些从 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) 、[凯尔·辛普森](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md)和 [ECMA 2020 规格](https://tc39.github.io/ecma262/)中收集到的想法。(只是为了好玩，在 Spec 中搜索“this”😂)

对于那些还没有对`this`感到沮丧的人，让我们带你登上“wtf，JavaScript”的列车。`this` *是否应该*允许我们编写引用拥有对象的函数，而不必硬编码对象名。

```
function Person(name) {
    this.name = name;
    this.printName = function() { console.log(this.name); };
}

const joe = new Person("Joe");
const sal = new Person("Sal");

joe.printName();    //> "Joe"
sal.printName();    //> "Sal" 
```

所以这非常有用。我们刚刚使用了`this`将一个属性和一个方法附加到一个对象上，并使用该方法返回属性。

然而，JavaScript 中的方法只是函数，函数是可以绑定到新变量的值，可以作为其他函数的参数，也可以从函数返回。这意味着我们可以这样做:

```
const printName = joe.printName;
printName;    //> ƒ () { console.log(this.name); }

printName();  //> (nothing prints) 
```

既然函数`printName`是从找到`this.name`的值的对象上下文之外被调用的，它不知道`this.name`应该解析成什么。但它确实知道`this`指的是什么:

```
const name = "Mike";    // declared in Global Scope

printName();   //> "Mike" 
```

原来全局对象(以及模块)有自己的`this`属性。如果您正在执行的函数没有由其执行上下文定义的`this`，它将默认为全局`this`。

```
console.log(this);    //> Window { ... }

console.log(window.globalThis);    //> Window { ... } 
```

这仍然没有那么令人困惑。我们再深入一点。

```
function anExample() {
    console.log(this);
    function aNestedExample() {
        console.log(this);
    }
    aNestedExample();
}
anExample();
//> Window { ... }
//> Window { ... }

const anObj = {};
anObj.aMethod = anExample;

anObj.aMethod();
//> { aMethod: f }
//> Window { ... } 
```

这就是问题的核心。即使我们将`anExample`函数作为方法附加到一个对象上，它内部的函数仍然引用全局对象。

[![isn't that weird?](img/d5087be05d99e3cce09f354b891d5bf0.png)](https://i.giphy.com/media/w6yT4hb1bODOcYS5i3/giphy.gif)

为什么？

事实证明有两个重要因素:

1)是否启用了`"use strict"`模式？
2)函数是如何调用的？

# `"use strict"`是否启用？

还有一个额外的因素！让我们稍微修改一下上面的例子，使用`"use strict"`

```
"use strict";

function anExample() {
    console.log(this);
    function aNestedExample() {
        console.log(this);
    }
    aNestedExample();
}
anExample();
//> undefined
//> undefined

const anObj = {};
anObj.aMethod = anExample;

anObj.aMethod();
//> { aMethod: f }
//> undefined 
```

如果强制执行`"use strict"`，JavaScript 将不会在函数的执行上下文之外搜索`this`值，因此如果函数没有为自己定义一个值，`this`将解析为`undefined`。

# 函数是如何调用的？

如果该函数作为一个函数被调用，而没有提供任何对象作为上下文供`this`引用，那么`this`将引用`global`对象。

```
function example() {
    console.log(this);
}
example();    //> Window { ... }

const anObj = {
    property: 5,
    method: function() {
        console.log(this);
        function noThis() {
            console.log(this);
        }
        hasThis();
    }
}
anObj.method();
//> { property: 5, method: f }
//> Window { ... } 
```

`this`*不是*的词汇范围。即使它包含在一个有`this`值的函数中，并且在词汇上位于一个对象中，它仍然引用`global`对象。

真正重要的是函数是否作为方法被调用:

```
const anObj = {
    property: 5,
    method: function() {
        console.log(this);
        return function noThis() {
            console.log(this);
        }
    }
}
const anotherObj = {};

anObj.method()();
//> { property: 5, method: f }
//> Window { ... }

anotherObj.asAMethod = anObj.method();    
//> { property: 5, method: f }

anotherObj.asAMethod();
//> { asAMethod: f } 
```

在这个例子中，即使函数`noThis`在词汇上位于另一个对象的方法中，而*在被调用时*有一个`this`，它也没有自己的`this` *，除非*被作为一个新对象的方法调用。否则指的是`global`对象。

所以您用来调用方法的实际符号很重要。

```
anObject.method;    // Dot Notation: this == true
anObject["method"]; // Computed Access: this == true
asAFunction();      // Function invocation: this == false 
```

每次调用函数时都会生成`this`的值。任何不作为对象方法调用的函数都不会生成一个`this`值。

但是还有最后一个转折。对象生成器函数和类*在被调用时*有一个`this`值，即使它们没有被作为方法调用:

```
class Person {
    constructor(name) {
        console.log(this);
        this.name = name;
    }
}
const joe = new Person("Joe");
//> Person {}

function Person(name) {
    console.log(this);
    this.name = name;
}
const joe = new Person("Joe");
//> Person {} 
```

这是有意义的，他们正在生成一个对象，并且需要能够在创建该对象时引用它，以便向它添加属性和方法。

如果我们在`prototype`上定义了一个方法，它*仍然*正确地引用了创建的对象，因为我们使用的是方法调用！

```
Person.prototype.newMethod = function () {
    console.log(this);
}
joe.newMethod();    //> Person { name: "Joe" } 
```

好吧，这实际上是*种*明智的做法，但是它设置了一个嵌套函数的陷阱，在这里你不能将嵌套函数作为方法调用。

这个问题有两种常见的解决方案:

1)将`this`的值保存为变量，如`that`、`_this`、`self`或`_self`。

```
const anObj = {
    property: 5,
    method: function() {
        const that = this
        function noThis() {
            console.log(this);
            console.log(that);
        }
        noThis();
    }
}
anObj.method();
//> Window { ... }
//> { property: 5, method: f } 
```

保存`this`的值允许它与正常的词法范围规则一起使用。

2)使用箭头功能

```
const anObj = {
    property: 5,
    method: function() {
        const that = this
        const noThis = () => {
            console.log(this);
            console.log(that);
        }
        noThis();
    }
}
anObj.method();
//> { property: 5, method: f }
//> { property: 5, method: f } 
```

箭头函数不定义它们自己的`this`值，相反，它们在词法范围链中寻找`this`值，就像你希望的`this`那样。

*然而*，因为它们没有定义自己的`this`如果你不将它们嵌套在用`function`关键字定义的函数中，并从一个对象调用*的方法*，你的箭头函数也不会知道`this`的值是什么:

```
const anObj = {
    method: () => console.log(this)
}
anObj.method();    //> Window { ... } 
```

所以如果你想用`this`:

1)调用函数时使用点`.`或括号`[ ]`符号。使用`function`来定义你的方法，而不是箭头函数。
3)要么绑定`this`的值，要么在你的方法中为词法范围使用箭头函数*。
4)不要使用`this` *，除非*你希望将函数作为方法使用，或者已经准备好让它引用`global`对象。*

# 好吧，但是为什么呢？

JavaScript 对`this`的解决方案是否是一个好主意应该根据它所面临的问题来判断。像 Ruby 这样的语言，对象拥有自己的方法，这些方法通常定义在`self`将要引用的对象内部或之上，与此不同，JavaScript 没有正式的方法。

JavaScript 中的方法实际上是对象上的键:值对，它存储对函数对象的引用值。用点或括号符号调用方法时，表达式`object.method()`实际上是两部分:`object.method`和函数调用。`object.method`评估为`myMethodFunction`，然后调用`myMethodFunction`。

与 Ruby 不同，在 Ruby 中，对象接收消息并根据消息上定义的方法确定是否可以响应消息，所有 JavaScript 对象都知道它们定义了哪些键，以及哪些值对应于这些键。由于用作方法的函数可以在任何地方定义，并且可能被许多不同的对象引用，所以任何函数都没有明确的所有者，JavaScript 被迫根据调用它的上下文而不是如何定义它来确定`this`。

所以这是有道理的。这很复杂，很棘手，让`this`引用`global`而不是词法范围是一个坏主意，但是现在我们明白了这一点，我们预料到了这一点，并使用`that`或`=>`来代替！