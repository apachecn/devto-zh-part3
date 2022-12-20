# JavaScript (ES5)对象

> 原文：<https://dev.to/martyhimmel/javascript-es5-objects-3cce>

*此文最初于 2017 年 1 月 31 日发布在我的网站 [https://martyhimmel.me](https://martyhimmel.me) 。像 dev.to 上的许多其他人一样，我决定将我的技术博客帖子转移到这个网站。*

在讨论数据类型时，我们将对象比作字典——一组术语(属性或键)及其定义(值)。另一种看待对象的方式是把它看作是某种东西的容器。这里有一个例子:

```
var person = {
  firstName: 'John',
  lastName: 'Smith',
  age: 24,
  isMarried: true,
  children: ['Jack', 'Judy'],
  pet: {
    type: 'dog',
    name: 'Fido'
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`person`对象包含几种不同的数据类型，包括另一个对象。访问这些属性都是通过点符号来完成的。

```
console.log(person.firstName); // John
console.log(person.age); // 24
console.log(person.children[0]); // Jack
console.log(person.pet.name); // Fido 
```

Enter fullscreen mode Exit fullscreen mode

对象也可以包含函数。下面是一个例子，继续讨论`person`对象:

```
var person = {
  firstName: 'John',
  lastName: 'Smith',
  ... // The other properties
  getFullName: function() {
    return person.firstName + '  ' + person.lastName;
  }
};
console.log(person.getFullName()); // John Smith 
```

Enter fullscreen mode Exit fullscreen mode

就像对象的其他属性一样，声明属性名并赋予它一个值。在这种情况下，值是一个函数。

## `this`

可以用稍微不同的方式编写`getFullName`函数，使用`this`关键字。

```
var person = {
  ...
  getFullName: function() {
    return this.firstName + '  ' + this.lastName;
  }
}
console.log(person.getFullName()); // John Smith 
```

Enter fullscreen mode Exit fullscreen mode

结果是一样的。`this`是对当前对象的引用。我将把`this`如何工作的细节留到一个单独的教程中，因为在你理解它的所有规则之前，它可能会有点混乱。对于本教程，我们将保持简单，而`this`将总是指当前/包含对象。

## 用构造函数创建对象

到目前为止，我们一直手动创建每个对象。这很好，但是会产生大量重复的代码。如果我们想要 10 个`person`对象，我们必须创建 10 个独立的对象。

```
var person1 = {
  firstName: 'John',
  lastName: 'Smith'
};
var person2 = {
  firstName: 'Jane',
  lastName: 'Doe'
};
// and so on 
```

Enter fullscreen mode Exit fullscreen mode

编程有个原则叫“干”——不要重复自己。如果您可以避免重复代码(有时，您不能或不一定想这样做)，这将使代码更容易维护。那么，我们如何在这里使用这个原则呢？

你会注意到我们的每个`person`对象都有相同的属性- `firstName`和`lastName`。如果我们愿意，他们可以拥有第一个例子中的所有属性，但是我们在这里保持简单。在任何情况下，在创建每个对象时都重复相同的代码。

这就是构造函数派上用场的地方。构造函数是产生对象的函数。常见的命名约定是将构造函数的第一个字母大写。这使它有别于其他功能。否则，构造函数的创建方式与任何其他函数完全相同。现在，让我们转换上面的例子。

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}
var person1 = new Person('John', 'Smith');
var person2 = new Person('Jane', 'Doe');
// and so on
console.log(person1.firstName + '  ' + person1.lastName); // John Smith
console.log(person2.firstName + '  ' + person2.lastName); // Jane Doe 
```

Enter fullscreen mode Exit fullscreen mode

使用`Person`构造函数消除了为每个对象分配名和姓的重复代码。想象一下，如果每个人有 10 或 15 处房产。那会有很多重复的代码。现在假设你有 500 个人，然后发现了一个 bug 或者需要改变对象的一些属性或方法。你必须做出 500 个改变。使用构造函数，你只需要在一个地方改变它。这就是为什么干燥原则很重要。

`Person`构造函数有两个参数- `firstName`和`lastName`。在函数中使用`this`是用来分配传递给给定属性名的值。换句话说，传递给构造函数的`firstName`参数被分配给所创建对象的`firstName` ( `this.firstName`)属性。

调用`new`是创建实际对象的过程。如果我们看一下被创建的`person1`对象，它看起来像这样:

```
console.log(person1); // {firstName: 'John', lastName: 'Doe'} 
```

Enter fullscreen mode Exit fullscreen mode

## 物体原型

在本节中，我们将继续使用上面的`Person`构造函数作为我们的基础。在第一部分中有一个类似于`getFullName`的方法会很方便。这就是原型出现的地方。

JavaScript 是一种基于原型的语言。你可以在 Mozilla 的开发者文档中深入了解它。

JavaScript 中的每个对象都有一个原型。将一个对象记录到控制台可以提供比对象本身更多的信息。所以，`console.log(person1);`的真实视图会给我们这个(使用 Chrome):

```
Person
  firstName: "John"
  lastName: "Smith"
  __proto__: Object
    constructor: Person(firstName, lastName)
      // a handful of other properties
      prototype: Object
        // more properties
    __proto__: Object
      // a bunch of properties inherited from the prototype chain 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，除了我们在构造函数中创建的两个属性之外，还有更多的内容。这是 JavaScript 对象原型结构的一部分。原型允许对象从其他对象继承属性。这也意味着我们可以通过利用构造函数的`prototype`属性，用新方法改造对象。

通过原型链添加方法的格式为:

```
[constructor name].prototype.[method name] = function() {
    // do something
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们添加`getFullName`方法。

```
Person.prototype.getFullName = function() {
  return this.firstName + '  ' + this.lastName;
};
console.log(person1.getFullName()); // John Smith 
```

Enter fullscreen mode Exit fullscreen mode

既然`Person`构造函数有了一个`getFullName`方法，那么该方法对每个实例都是可用的- `person1`、`person2`以及任何其他可能已经用构造函数创建的对象。由于原型的工作方式，即使一个`Person`对象是在原型方法被添加到构造函数之前创建的(就像这个例子中的情况——编写构造函数，创建两个对象，添加原型方法),这个方法仍然对所有用给定构造函数创建的*对象可用。*