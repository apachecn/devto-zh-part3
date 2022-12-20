# JavaScript 中的设计模式

> 原文：<https://dev.to/sameer_zure/design-patterns-in-javascript-2imk>

## 什么是图案？

一个模式可以被认为是一个模板，它可以在几种不同的情况下使用。模式为我们提供了一个解决策略，我们可以应用它来解决软件设计中经常出现的问题。基本上，设计模式让你写的代码更容易实现、构建和维护。

> 编程到接口，而不是实现。

## 设计模式类别

设计模式可以分为多个类别。但通常使用下列术语。

1)创造性设计模式
这些主要集中在类实例化或者你可以说处理对象创建机制。
因此，这些模式有助于解决创建对象时可能产生的问题。工厂、构造器、原型、抽象、单例、构造器是一些创造性的设计模式。

**2)结构设计模式**
结构设计模式都是关于对象组成的。这些主要确定实现对象之间关系的方法，并确保系统的一部分独立于另一部分。
Decorator、Bridge、Flyweight、Adapter、Composite 和 Proxy 就是其中的一些模式。

**3)行为设计模式**
行为设计模式关注的是系统中对象的交流。
其中一些模式是迭代器、状态、中介器和观察者。

## JavaScript 设计模式

我们可以使用 JavaScript 实现许多模式。但是我将只讨论其中的四种，即构造器模式、模块模式、单例模式和原型模式。

**构造函数模式**
我们都知道，一旦系统给对象分配了内存，就用构造函数来初始化对象。在 JavaScript 中，几乎所有东西都是对象，甚至是函数。
现在 JavaScript 支持构造函数，通过“ **new** 调用构造函数，暗示着我们希望那个函数的行为像一个构造函数，实例化一个对象。让我们通过一个例子来看看:

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}
Person.prototype.fullName = function() {
  console.log(this.firstName + ' ' + this.lastName);
}
var driver = new Person('John', 'Doe');
driver.fullName();    //Prints John Doe in console 
```

上面的例子演示了一个简单的构造器设计模式。这里需要注意的重要一点是，我们使用函数的**原型**对象来定义**全名()**。
这背后的原因是，这样一来，每当我们创建 **Person** 的新对象时， **fullName()** 就不会被重新定义。现在只有一个 **fullName()** 的实例将在不同的 **Person** 对象之间共享。

**模块模式**
模块是独立的可重用代码的小单元。它们有助于保持一段代码的分离和组织。模块有助于分别定义私有和公共成员，使之成为 JavaScript 范式中更受欢迎的设计模式之一。
使用模块模式，我们可以模仿类的概念，也就是说，我们在单个对象中包含私有和公共变量和方法，也可以防止片段泄漏到全局范围。所以基本上在这里我们在**闭包**的帮助下封装了所有东西，并且只暴露公共 API。看看下面的例子:

```
var counterApi = (function(){
  var counter = 0;

  var inc = function() {
    counter++;
  }

  var dec = function() {
    counter--;
  }
  return {
    increment: function() {
      inc();
    },
    decrement: function() {
      dec();
    },
    reset: function() {
      counter = 0;
    },
    getCounterValue: function() {
      return counter;
    }
  };
})();

counterApi.increment();
counterApi.increment();
counterApi.increment();
counterApi.decrement();
counterApi.reset();
console.log(counterApi.getCounterValue()); 
```

在上面的例子中，你可以看到变量**计数器**在全局范围内受到保护，因此表现得像一个私有成员。**递增**、**递减**、**复位**和**获取反值**功能对外公开，只有这些功能可以访问**计数器**。
在模块模式中，声明的变量或方法只在模块内部可用(因为封闭性)。然而，每个人都可以使用返回对象中定义的变量或方法。

单例模式
单例模式将一个类的实例化限制为一个对象。只有当不存在实例时，才会创建类的新实例。然而，如果一个类的实例存在，它将被返回。下面是实现此模式的一个简单示例:

```
var mySingleton = (function () {
  // Instance stores a reference to the Singleton
  var instance;
  function init() {
    // Singleton
    function printNum() {
      console.log(num);
    }
    var num = Math.random();
    return {
      printNumber: function() {
        printNum();
      },
      getNumber: function() {
        return num;
      }
    };
  };
  return {
    getInstance: function () {
      if (!instance) {
        instance = init();
      }
      return instance;
    }
  };
})();

var ins1 = mySingleton.getInstance();
var ins2 = mySingleton.getInstance();
console.log(ins1.getNumber() === ins2.getNumber()); // true 
```

**原型模式**
原型模式是指基于已有对象的模板创建对象。在 JavaScript 中，我们可以认为这是一种原型继承，其中创建的对象充当其他对象的原型。
原型模式的主要优势是性能提升。所有子对象都指向父对象中定义的同一个函数，而不是创建自己的副本。
我们可以使用 **Object.create()** (它创建一个新对象，使用一个现有对象作为新创建对象的原型)来实现这个模式。

```
var libraryPrototype = {
  books: ['Book1', 'Book2'],
  getAllBooks: function() {
    console.log(this.books);
  }
}

var myLibrary = Object.create(libraryPrototype, 
{ 
  name: {
    writable: true,
    configurable: true,
    value: 'My Library'
  }
});

console.log(myLibrary.name); // prints "My Library"
myLibrary.getAllBooks(); // prints ["Book1", "Book2"] 
```

所以在上面的例子中， **libraryPrototype** 是现有的对象，它充当了 **myLibrary** 对象的原型。 **myLibrary** 的新属性可以在 **Object.create** 的第二个参数上初始化。

设计模式可以通过提供经过测试和验证的开发范例来加速开发过程。在创建或维护解决方案时，它们也是让一个组织或团队中的所有开发人员保持一致的非常强大的方法。它们是提高效率的工具，但更重要的是让你作为开发者提高你的整体设计技能和项目质量，并给你更广泛的技能组合。