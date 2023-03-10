# 我从哪里开始？第 4 部分:JavaScript OOP、上下文和类

> 原文：<https://dev.to/kevindsteeleii/where-do-i-start-part-4-javascript-oop-context-and-class-36lc>

## 你上周不是刚写了点这个吗？

不，我为 JavaScript 列出了一个资源列表，不包括它实现**面向对象编程**的技巧。一些参考资料涵盖了它，但并不广泛，除了我相信的自由代码营。

## 这次我们要学习什么？

### 面向对象编程

是的，我知道我一直在说，但让我们先了解一些词汇。上一次，我提到了作为一种数据类型的**对象**,但是我并没有真正说明对象和其他数据类型或者原语之间的区别。在 JavaScript 中，一切都是对象，这导致了一些有趣的问题。即绑定。

### 原语

原语是不可变或不可更改的数据类型，它没有改变原语本身的方法。今年 JavaScript 有 6 种原语:

*   用线串
    *   “你好”
*   数字
    *   123.456, 1, -12
*   布尔运算
    *   真，假
*   空
    *   空指针引用，一个不指向任何东西的引用
*   不明确的
    *   未定义变量时，赋给变量的默认原始值
*   标志
    *   我可以写一篇关于这些的博客，我可能会的。目前，它们被用作动态生成的独特的匿名值。如果你不明白也没关系。

还有像整数之类的子类，但目前这并不重要。

#### 如果原语是不可变的，我怎么可以这样做？

```
 let greeting = "hello";
     greeting += " and goodbye"; // greeting equals "hello and goodbye" now 
```

Enter fullscreen mode Exit fullscreen mode

在这个代码片段中，您实际上并没有将字符串原语“hello”更改为“hello and goodbye ”,而是更改了变量 *greeting* 的**赋值**值。

### 是什么让物体如此特别？

对象通常用于将相关信息组合在一起。无论是价值、功能还是其他。这是一种数据结构。对象通常被用来创建现实世界事物和概念的抽象，比如鞋子或日期。你可能还会听到像字典或散列这样的词。这些都是物件。重要的主线是它们都有键值对。

#### 键值对

是的，键值对。对象具有不同值的属性，而键用于访问与对象相关的任何值。因此名为键-值对。例如:

```
 // initializing a shoe object
     const shoe = {};
     // it's looking lonely let's give it a type or something
     shoe.type = "sneakers";
     // what's it made of ?
     shoe.materials = ["nubuck", "rubber", "EVA"];     
     // and you can nest objects inside of objects indefinitely, you might not want to nest them too deeply
     shoe.company = {
          name: "Anon shoe Corp",
          address: "123 None of your business Ave."
     }
     // and who makes them
     shoe['getCompany'] = function() {
          return this.company.name;
     }

     console.log(shoe.type); // prints out "sneakers"
     console.log(shoe['materials']); //prints out ["nubuck", "rubber", "EVA"]; 
```

Enter fullscreen mode Exit fullscreen mode

我有意在 object.key 和 object["key"]符号之间进行交换。它们的意思相同，但有不同的用途。如果你用 for-in 循环遍历一个对象，你最好在循环中使用方括号对象['key']符号，但是你不需要引号，因为 for-in 以字符串的形式给你提供键。括号符号使用文字键作为字符串来访问值。另一方面，如果您知道键的名称，并且它不像“我有空格”，那么您可以用点符号来分配或访问它的值。

### 你说的是关于语境的事情吧？

是的，但是首先我会给你看一些代码，然后边看边解释。

```
 function greetings (greeting) {
          // the this keyword refers to the object that the function resides inside or its context
          return greeting +"  "+ this.name;
     }
     console.log(greetings("Hello")); // "Hello undefined"

     const James = {
          name: "James Walker"
     }

     console.log(greetings.call(James, "Hello")); // "Hello James Walker" 
```

Enter fullscreen mode Exit fullscreen mode

**这个**关键字指的是调用函数的上下文。就上下文而言，我指的是函数驻留在哪里，或者它在被调用/执行时绑定到什么。在第一次调用问候语时，我们得到“Hello undefined ”,因为第一次调用的上下文是全局的。这个名称属性没有全局赋值，所以默认情况下是未定义的。在我们创建了另一个可以提供上下文的对象之后，我们将函数绑定到那个对象，为它提供一个定义好的执行上下文。 **Call** 是将 greetings 函数显式绑定到 James 对象的三种特殊方法之一，该对象为 name 属性提供上下文。因此，当在该上下文中调用它时，它返回“你好，詹姆斯·沃克”。第一个参数总是调用上下文，后面跟着需要传递的其余参数。换句话说，该执行上下文中的 **this** 是詹姆斯对象，因此第二次调用时的 *this.name* 基本上是*詹姆斯. name* 。

### 这很好，但是如果我想写一段代码来创建对象，而不是手工编写每个对象的代码呢？

我们过去对所有事情都使用类，现在仍有这样做的情况，但我将向您展示这一点，然后告诉您为什么要少用它。有一个著名的例子叫做大猩猩香蕉问题。

```
 // old-school ES5
     // creates an object with a constructor function
     function Vehicle(make, model, year) {
          this.make = make;
          this.model = model;
          this.year = year;
     }

     // The prototype refers to an object inside of vehicle, all objects have a prototype object inside and it's used to pass down attributes or make new ones
     // we then create another attribute with the key of getInfo and its value is a function that prints out information about the Object.
     Vehicle.prototype.getInfo = function() {
          console.log("This is a ", this.year, "  ", this.make, "  ", this.model);
     }

     // Bicycle subclass, Vehicle being the superclass or parent
     function Bicycle(make, model, year, speed) {
          // using call redefines the execution context to the Bicycle object, not the original Vehicle constructor
          Vehicle.call(this, make, model, year);
          this.speed = speed;
     }

     // es6 style
     // much closer to traditional OOP languages in the "new" es6 syntax
     class Vehicle {
          constructor(make, model, year) {
               this.make = make;
               this.model = model;
               this.year = year;
          }

          getInfo() {
               console.log("This is a ", this.year, "  ", this.make, "  ", this.model);
          }
     }

     // let's make a subclass
     class Bicycle extends Vehicle {
          constructor(make, model, year, speed) { 
               // super is used to pass arguments to the class Bicycle is derived from
               super(make, model, year);
               this.speed = speed;
          }
     }

     const bike = new Bicycle("BMX", "Stormbringer", 1999, 5);
     bike.getInfo(); // logs "This is a 1999 BMX Stormbringer" 
```

Enter fullscreen mode Exit fullscreen mode

原型是作为一种内核存在于所有其他对象内部的对象。这个内核是你所有的属性和函数所在的地方。原型也可以包含另一个原型，它无限地继承功能和属性。派生或子类的访问被称为**继承**，由**原型链**提供。原型链只是指所有的类如何从它们先前祖先的所有原型继承，形成一个链或原型继承。

第二，ES6 版本是以任何有 OOP 语言经验的人都能识别的方式编写的。这是实现这种语法的部分原因。它使 JavaScript 人员更容易掌握 OOP 编码，如果他们已经知道一两种 OOP 语言，它也给不太了解它的人一个立足点。此外，解释什么是原型以及这个关键字的意思可能会令人困惑。ES6 在整体简化 JavaScript 语法方面取得了巨大进步。

长话短说，原型继承是一种不可避免的邪恶，即使有了新的约定，它仍然存在于表面之下。话虽如此，OOP 仍然容易出现某些问题，而且绝不是万能的。我将在下一次讨论这些问题以及一些解决方案和更多资源。

### 临别赠言

下一次，我会给你带来更多的资源，和往常一样，我不是绝对正确的，请评论，批评，并联系。我刚刚开始我的职业生涯，我希望变得更好，为我的键盘战友们提供更多的价值。

### 资源链接

*   **视频**
    *   旅行媒体
        *   [JavaScript OOP 速成班- 40 分钟](https://www.youtube.com/watch?v=vDJpGenyHaA)
    *   用 Mosh 编程
        *   [JavaScript-1hr 中的面向对象编程](https://www.youtube.com/watch?v=PFmuCDHHpwk)
    *   阿卡德曼德
        *   [参考与原始值/类型- 21 分钟](https://www.youtube.com/watch?v=9ooYYRLdg_g)
*   **网站/阅读材料**

    *   MDN
        *   [对象基础知识](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Basics)
        *   [对象原型](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object_prototypes)
        *   [继承和原型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
    *   w3 学校
        *   [JavaScript 对象](https://www.w3schools.com/js/js_object_definition.asp)
        *   [这个关键字](https://www.w3schools.com/js/js_this.asp)
        *   [对象原型](https://www.w3schools.com/js/js_object_prototypes.asp)
            *   泰勒·麦金尼斯
        *   [这个关键字，调用，应用，绑定](https://tylermcginnis.com/this-keyword-call-apply-bind-javascript/)