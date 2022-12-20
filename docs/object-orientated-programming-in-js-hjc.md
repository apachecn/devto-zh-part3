# JS 中的面向对象编程

> 原文：<https://dev.to/kartik2406/object-orientated-programming-in-js-hjc>

在面向对象编程中，你将数据和方法组合成一个名为类的实体，并创建名为对象的类的不同实例。这些对象将具有不同的数据。继承是另一个 OOP 特性，它允许我们通过创建父类和子类来重用代码。父类有所有子类共有的代码。孩子是父母的特化。

### Javascript 的继承模型

JavaScript 遵循原型基础继承模型。原型是 JS 引擎将添加到函数中的属性。默认情况下，此原型对象 intern 具有构造函数属性。要检查原型上可用的属性，您可以使用 object . getownpropertymanames(function name . prototype)
来检查原型上存在哪些属性。

### 创建一个类及其实例

让我们先来看看如何在 JS 中创建一个特定的类对象。要创建一个对象，你需要利用一个构造函数。您可以使用构造函数来获取特定类型的对象。您一定已经看过 new Array()中使用的 new 关键字，new Date()。
在下面的例子中，我们为传输类型创建一个构造函数。约定是像命名类一样命名构造函数。

```
function Transport(mode, travelSpeed, ticketCost) {
  this.mode = mode
  this.travelSpeed = travelSpeed
  this.ticketCost = ticketCost
}

let bus = new Transport('Road', 'Slow', 'Cheap')
console.log(bus)
// Output: { mode: "Road", travelSpeed: "Slow", ticketCost: "Cheap" } 
```

这里我们创建了一个构造函数，它创建了一个 Transport 类型的对象。

使用“instance of”运算符检查对象是否是类的实例。

```
bus instanceof Transport
// Output: true 
```

您还可以检查 protype 对象的属性。

```
console.log(Object.getOwnPropertyNames(Transport.prototype))
// Output: Array [ "constructor" ] 
```

### 向类中添加方法

当使用类时，我们应该在原型上使用方法，因为这样我们可以改变原型上的方法，并在所有实例中反映出来。

```
Transport.prototype.showInfo = function() {
  console.log(this.mode, this.travelSpeed, this.ticketCost)
}
bus.showInfo()
// Output: Road Slow Cheap 
```

如果您现在检查 transport 的 prototype 属性，您将会看到我们刚刚添加的方法。

```
console.log(Object.getOwnPropertyNames(Transport.prototype))
// Output: Array [ "constructor", "showInfo" ] 
```

### 创建子类

现在让我们为 Bus 创建一个单独类型的类，因为这个对象将具有不需要的、不为 Transport 类所共有的属性。

```
function Bus(mode, travelSpeed, ticketCost, busType) {
  Transport.call(this, mode, travelSpeed, ticketCost)
  this.busType = busType
}

let interCityBus = new Bus('Road', 'Slow', 'Cheap', 'Sleeper')
console.log(interCityBus)
// Output: { mode: "Road", travelSpeed: "Slow", ticketCost: "Cheap", busType: "Sleeper" } 
```

在上面的例子中，我们通过从总线构造函数中调用传输构造函数创建了一个父子关系。Transport.call()类似于 super()运算符调用。

虽然这里没有完全实现继承，但是让我们检查一下 bus 类的 prototpe 属性

```
console.log(Object.getOwnPropertyNames(Bus.prototype))
// Output:  Array [ "constructor" ] 
```

这没有在父级上定义的方法。为了获得父原型的属性，你需要合并子原型和父原型，这在[这个 MDN 帖子](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Inheritance)和[数字海洋社区](https://www.digitalocean.com/community/tutorials/understanding-prototypes-and-inheritance-in-javascript)的这个帖子中有很好的解释。要了解更多关于 JavaScript 中的原型，请阅读这些帖子([用普通语言编写的 JavaScript 原型](http://javascriptissexy.com/javascript-prototype-in-plain-detailed-language/)，[用 JavaScript 编写的原型](https://hackernoon.com/prototypes-in-javascript-5bba2990e04b))。你也可以通过在子类的原型中给方法赋值来覆盖子类中的方法。

### 更简单的工作方式

使用原型可能会变得有点乏味和混乱。还有另一种方法来处理继承，ES 2105 引入了新的语法来创建一个类。但是内部继承机制保持不变。让我们将上面的例子转换成基于类的解决方案。

```
class Transport {
  constructor(mode, travelSpeed, ticketCost) {
    this.mode = mode
    this.travelSpeed = travelSpeed
    this.ticketCost = ticketCost
  }

  showInfo() {
    console.log(this.mode, this.travelSpeed, this.ticketCost)
  }
}

class Bus extends Transport {
  constructor(mode, travelSpeed, ticketCost, busType) {
    super(mode, travelSpeed, ticketCost)
    this.busType = busType
  }
}

let cityBus = new Bus('Road', 'Slow', 'Cheap', 'Seating')
cityBus.showInfo() 
```

好了，它看起来整洁干净吧？我们使用 super()操作符将值传递给父类的构造函数。此外，这样我们就不需要合并父类和基类的原型，因为它是由 JavaScript 为我们处理的。如果我们想改变 showInfo()方法的行为，我们可以简单地在子类中重新定义它。

```
class Bus extends Transport {
  constructor(mode, travelSpeed, ticketCost, busType) {
    super(mode, travelSpeed, ticketCost)
    this.busType = busType
  }

  showInfo() {
    console.log(this.mode, this.travelSpeed, this.ticketCost, this.busType)
  }
}

let cityBus = new Bus('Road', 'Slow', 'Cheap', 'Seating')
cityBus.showInfo() 
```

你不必总是在构造函数中设置所有的属性，有时你可能只想设置和读取特定的值，你可以在这里阅读 getters 和 setter。

如果你喜欢这篇文章，请分享它，关注我来更新我的文章:)。

*哈尔·盖特伍德在 Unsplash 上拍摄的封面照片*