# function . call . bind(object . prototype . hasownproperty)，等等什么？

> 原文：<https://dev.to/melkornemesis/functioncallbind-objectprototypehasownproperty--wait-what-3han>

前几天我在浏览一个`prop-types`库的源代码时，偶然发现了这段代码:

```
const has = Function.call.bind(Object.prototype.hasOwnProperty) 
```

我就想这到底是怎么回事。

通过查看代码，你可能会猜到`has`最终会成为一个**函数**，它将检查一个对象是否有自己的属性。自有属性是直接存在于对象上的属性**，所以检查是在没有参考原型链的情况下执行的。**

 **你可能会问——我就不能做`({ foo: 1 }).hasOwnProperty(prop)`吗？是的，你可以。但是有一个特定的场景，它会失败。考虑:

```
const myObj = Object.create(null); 
```

> Object.create()方法创建一个新对象，使用现有对象作为新创建对象的原型。

这将创建一个没有任何原型的对象。所以原型链中没有`hasOwnProperty`方法。

```
myObj.hasOwnProperty() // Uncaught TypeError: myObj.hasOwnProperty is not a function 
```

您可以通过以下方式解决这个问题:

```
Object.prototype.hasOwnProperty.call(myObj, prop) 
```

但是这非常冗长，你应该考虑一个对象，它可能没有原型。

那么`has`函数来了，你这样用:

```
const myObj = { foo: true }
has(myObj, 'foo') // true
has(myObj, 'bar') // false 
```

# 它是如何工作的

简单回顾一下`Function.call`和`Function.bind`做了什么。

## `Function.call`

> call()方法使用给定的 this 值和单独提供的参数调用函数。

## `Function.bind`

> bind()方法创建一个新函数，调用该函数时，其 this 关键字设置为提供的值，调用新函数时，在任何提供的参数之前有一个给定的参数序列。

## 解

好了，看看`has`函数和它的用法`has({ foo: true }, 'foo')`，我们可以说，我们需要创建一个函数，它有两个参数:

1.  要对其执行检查的对象
2.  要检查的属性

还记得`Object.prototype.hasOwnProperty.call(myObj, prop)`吗？难道`.call()`的函数签名看起来不像我们想要实现的吗？`has`函数有两个参数——上下文(对象)和属性。我们可以使用`call`函数来提供上下文和额外的参数。

好的，我们能让另一个上下文调用`.call`函数吗，特别是`Object.prototype.hasOwnProperty`的上下文？是的，我们可以，这就是`const has = Function.call.bind(Object.prototype.hasOwnProperty)`发挥作用的地方。

```
console.dir(has)
// output
ƒ bound call()
    name: "bound call"
    __proto__: ƒ ()
    [[TargetFunction]]: ƒ call()
    [[BoundThis]]: ƒ hasOwnProperty()
    [[BoundArgs]]: Array(0) 
```

刚刚发生了什么事？我们采用了存在于`Function.prototype`对象上的`.call`函数，并将其上下文更改为`ƒ hasOwnProperty()`。

如果你检查`console.dir`的输出，你可以看到我们取回了`call`函数(`[[TargetFunction]]`，但是它的`this`被绑定到了`ƒ hasOwnProperty()` `[[BoundThis]]`。

这意味着当您调用`has({ foo: true, 'foo' })`时，您仍然在执行`call`函数，该函数将上下文作为第一个参数，然后是调用该函数时跟随的任何其他参数。但是有一个很大的不同——`call`的上下文与`Object.prototype.hasOwnProperty`绑定在一起。

## 更简单的例子

当我试图理解这一点时，它帮助我在更容易理解代码的基础上进行测试。

```
function sayHi() {
    console.log('Hi ' + this.name) // NOTICE the usage of this.name
} 

const boundSayHi = Function.call.bind(sayHi)

boundSayHi({ name: 'Jane' }) // "Hi Jane"
boundSayHi({ name: 'Peter' }) // "Hi Peter"

// instead of

sayHi.call({ name: 'Jane' }) // "Hi Jane" 
```

这里正在发生完全相同的事情:

```
console.dir(boundSayHi)
// output
ƒ bound call()
    name: "bound call"
    __proto__: ƒ ()
    [[TargetFunction]]: ƒ call()
    [[BoundThis]]: ƒ sayHi()
    [[BoundArgs]]: Array(0) 
```

我们取回了存储在变量`boundSayHi`中的函数`call`，但是它的上下文被绑定到了`ƒ sayHi()`。

当您运行`boundSayHi({ name: 'Jane' })`时，您正在调用绑定到`sayHi`函数的`call`函数，并且您正在为那个`sayHi`函数传递一个上下文。

# 外卖

*   记住，在一个对象上调用`hasOwnPrototype`可能不起作用，因为它可能没有原型。
*   `Object.prototype.hasOwnProperty.call(obj, prop)`太长，尤其是当你在一个项目或一个文件中多次使用它的时候。创建助手。
*   如果有些事情没有意义，试着去理解它。往下挖。可能需要几个小时，甚至几天。但最终会成功的。在你复制粘贴和不理解的代码中很难找到 bug。**