# JavaScript 继承和原型链

> 原文：<https://dev.to/tylermcginnis/javascript-inheritance-and-the-prototype-chain-488e>

[https://www.youtube.com/embed/MiKdRJc4ooE](https://www.youtube.com/embed/MiKdRJc4ooE)

> 这篇文章被设计成在你阅读了 **[JavaScript 私有和公共类字段](https://tylermcginnis.com/javascript-private-and-public-class-fields/)** 之后阅读。

之前我们学习了如何在 ES5 和 ES6 中创建一个`Animal`类。我们还学习了如何使用 JavaScript 的原型在这些类之间共享方法。回顾一下，这是我们在之前的帖子中看到的代码。

* * *

```
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Animal.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

const leo = new Animal('Leo', 7) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
class Animal {
  constructor(name, energy) {
    this.name = name
    this.energy = energy
  }
  eat(amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  }
  sleep() {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  }
  play() {
    console.log(`${this.name} is playing.`)
    this.energy -= length
  }
}

const leo = new Animal('Leo', 7) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

现在，假设我们想开始为特定的动物创建单独的类。例如，如果我们想开始制作一堆狗的实例。这些狗会有什么属性和方法？嗯，类似于我们的`Animal`类，我们可以给每只狗一个`name`、`energy`等级，以及`eat`、`sleep`和`play`的能力。我们的`Dog`职业独有的，我们也可以给他们一个`breed`属性和`bark`能力。在 ES5 中，我们的`Dog`类可能看起来像这样

```
function Dog (name, energy, breed) {
  this.name = name
  this.energy = energy
  this.breed = breed
}

Dog.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Dog.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Dog.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

Dog.prototype.bark = function () {
  console.log('Woof-Woof!')
  this.energy -= .1
}

const charlie = new Dog('Charlie', 10, 'Goldendoodle') 
```

Enter fullscreen mode Exit fullscreen mode

好吧，那么...我们只是重新创建了`Animal`类，并添加了一些新的属性。如果我们想创建另一个动物，比如说一个`Cat`，此时我们必须再次创建一个`Cat`类，复制`Animal`类中的所有公共逻辑，然后添加`Cat`的特定属性，就像我们对`Dog`类所做的那样。事实上，我们必须为我们创造的每一种不同类型的动物都这样做。

```
function Dog (name, energy, breed) {}

function Cat (name, energy, declawed) {}

function Giraffe (name, energy, height) {}

function Monkey (name, energy, domesticated) {} 
```

Enter fullscreen mode Exit fullscreen mode

这项工作，但它似乎是浪费。`Animal`类是完美的基类。这意味着它拥有我们每一种动物共有的所有特性。不管我们是在创造一只狗、猫、长颈鹿还是猴子，它们都将有一个`name`、`energy`的等级，以及`eat`、`sleep`和`play`的能力。也就是说，当我们为每个不同的动物创建单独的类时，有没有一种方法可以利用`Animal`类？让我们试一试。下面我再贴一次`Animal`类，方便参考。

```
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Animal.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

function Dog (name, energy, breed) {

} 
```

Enter fullscreen mode Exit fullscreen mode

关于上面的`Dog`构造函数，我们知道些什么？

首先，我们知道它有三个参数，`name`、`energy`和`breed`。

其次，我们知道它将被用关键字`new`调用，所以我们将有一个`this`对象。

第三，我们知道我们需要利用`Animal`函数，这样狗的任何实例都将有一个`name`、`energy`等级，并且能够有`eat`、`sleep`和`play`。

第三个才是棘手的。你“利用”一个函数的方法是调用它。所以我们知道在`Dog`内部，我们要调用`Animal`。但是我们需要弄清楚的是，我们如何在`Dog`的上下文中调用`Animal`。这意味着我们想用来自`Dog`的`this`关键字来调用`Animal`。如果我们做得正确，那么`Dog`函数内部的`this`将拥有`Animal` ( `name`，`energy`)的所有属性。如果您还记得上一节[中的](https://tylermcginnis.com/this-keyword-call-apply-bind-javascript/)，JavaScript 中的每个函数都有一个`.call`方法。

> 。call()是每个函数上的一个方法，它允许您调用函数，并指定该函数将在什么上下文中被调用。

这听起来正是我们所需要的。我们希望在`Dog`的上下文中调用`Animal`。

```
function Dog (name, energy, breed) {
  Animal.call(this, name, energy)

  this.breed = breed
}

const charlie = new Dog('Charlie', 10, 'Goldendoodle')

charlie.name // Charlie
charlie.energy // 10
charlie.breed // Goldendoodle 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们已经走了一半了。您会注意到，在上面的代码中，由于这条线`Animal.call(this, name, energy)`，每个`Dog`实例现在都有一个`name`和`energy`属性。同样，这样做的原因是因为好像我们用从`Dog`生成的`this`关键字运行了`Animal`函数。然后在我们给`this`添加了一个`name`和`energy`属性之后，我们也像平常一样添加了一个`breed`属性。

请记住，这里的目标是让`Dog`的每个实例不仅拥有`Animal`的所有属性，还拥有所有方法。如果你运行上面的代码，你会注意到如果你试图运行`charlie.eat(10)`你会得到一个错误。目前`Dog`的每个实例都将具有`Animal` ( `name`和`energy`)的属性，但是我们还没有做任何事情来确保它们也具有方法(`play`、`eat`、`sleep`)。

让我们想想如何解决这个问题。我们知道所有`Animal`的方法都位于`Animal.prototype`上。这意味着我们想确保`Dog`的所有实例都可以访问`Animal.prototype`上的方法。如果我们用我们的好朋友`Object.create`会怎么样？如果您还记得，`Object.create`允许您创建一个对象，该对象将在查找失败时委托给另一个对象。所以在我们的例子中，我们想要创建的对象将是`Dog`的原型，而我们想要在失败的查找中委托给的对象是`Animal.prototype`。

```
function Dog (name, energy, breed) {
  Animal.call(this, name, energy)

  this.breed = breed
}

Dog.prototype = Object.create(Animal.prototype) 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当对`Dog`实例的查找失败时，JavaScript 就会将该查找委托给`Animal.prototype`。如果这仍然有点模糊，重新阅读[JavaScript 的原型](https://tylermcginnis.com/beginners-guide-to-javascript-prototype/)初学者指南，在那里我们谈论了所有关于`Object.create`和 JavaScript 的原型。

让我们一起看看完整的代码，然后我们将通过发生了什么。

```
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Animal.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

function Dog (name, energy, breed) {
  Animal.call(this, name, energy)

  this.breed = breed
}

Dog.prototype = Object.create(Animal.prototype) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经创建了我们的基类(`Animal`)和子类(`Dog`)，让我们看看当我们创建一个`Dog`的实例时它是什么样子的。

```
const charlie = new Dog('Charlie', 10, 'Goldendoodle')

charlie.name // Charlie
charlie.energy // 10
charlie.breed // Goldendoodle 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止还没有什么新奇的东西，但是让我们看看当我们调用位于`Animal`上的方法时会发生什么。

```
charlie.eat(10)

/*
1) JavaScript checks if charlie has an eat property - it doesn't.
2) JavaScript then checks if Dog.prototype has an eat property
    - it doesn't.
3) JavaScript then checks if Animal.prototype has an eat property
    - it does so it calls it.
*/ 
```

Enter fullscreen mode Exit fullscreen mode

检查`Dog.prototype`的原因是因为当我们创建一个新的`Dog`实例时，我们使用了`new`关键字。在引擎盖下，为美国创建的`this`对象委托给`Dog.prototype`(见下面的评论)。

```
function Dog (name, energy, breed) {
  // this = Object.create(Dog.prototype)
  Animal.call(this, name, energy)

  this.breed = breed
  // return this
} 
```

Enter fullscreen mode Exit fullscreen mode

检查`Animal.prototype`的原因是因为我们用这一行
重写了`Dog.prototype`以委托给`Animal.prototype`

```
Dog.prototype = Object.create(Animal.prototype) 
```

Enter fullscreen mode Exit fullscreen mode

现在有一件事我们还没有谈到，如果`Dog`有自己的方法呢？嗯，这是一个简单的解决办法。就像使用`Animal`一样，如果我们想在该类的所有实例中共享一个方法，我们就把它添加到函数的原型中。

```
...

function Dog (name, energy, breed) {
  Animal.call(this, name, energy)

  this.breed = breed
}

Dog.prototype = Object.create(Animal.prototype)

Dog.prototype.bark = function () {
  console.log('Woof Woof!')
  this.energy -= .1
} 
```

Enter fullscreen mode Exit fullscreen mode

👌非常好。我们只需要做一个小小的补充。如果你还记得 JavaScript 原型的[初学者指南](https://tylermcginnis.com/beginners-guide-to-javascript-prototype/)的帖子，我们能够通过使用`instance.constructor`访问实例的构造函数。

```
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

const leo = new Animal('Leo', 7)
console.log(leo.constructor) // Logs the constructor function 
```

Enter fullscreen mode Exit fullscreen mode

正如前一篇文章中所解释的，“这样做的原因是因为在查找失败时，`Animal`的任何实例都将委托给`Animal.prototype`。所以当你试图访问`leo.constructor`时，`leo`没有`constructor`属性，所以它会将查询委托给`Animal.prototype`，而后者确实有`constructor`属性。”

我提出这个问题的原因是因为在我们的实现中，我们用一个委托给`Animal.prototype`的对象重写了`Dog.prototype`。

```
function Dog (name, energy, breed) {
  Animal.call(this, name, energy)

  this.breed = breed
}

Dog.prototype = Object.create(Animal.prototype)

Dog.prototype.bark = function () {
  console.log('Woof Woof!')
  this.energy -= .1
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着现在，任何记录了`instance.constructor`的`Dog`实例都将获得`Animal`构造函数，而不是`Dog`构造函数。你可以通过运行这段代码来亲眼看看-

```
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Animal.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

function Dog (name, energy, breed) {
  Animal.call(this, name, energy)

  this.breed = breed
}

Dog.prototype = Object.create(Animal.prototype)

Dog.prototype.bark = function () {
  console.log('Woof Woof!')
  this.energy -= .1
}

const charlie = new Dog('Charlie', 10, 'Goldendoodle')
console.log(charlie.constructor) 
```

Enter fullscreen mode Exit fullscreen mode

注意它给了你`Animal`构造函数，即使`charlie`是`Dog`的直接实例。同样，我们可以像上面一样浏览这里发生的事情。

```
const charlie = new Dog('Charlie', 10, 'Goldendoodle')
console.log(charlie.constructor)

/*
1) JavaScript checks if charlie has a constructor property - it doesn't.
2) JavaScript then checks if Dog.prototype has a constructor property
    - it doesn't because it was deleted when we overwrote Dog.prototype.
3) JavaScript then checks if Animal.prototype has a constructor property
    - it does so it logs that.
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我们如何解决这个问题？嗯，这很简单。一旦我们覆盖了它，我们就可以将正确的`constructor`属性添加到`Dog.prototype`中。

```
function Dog (name, energy, breed) {
  Animal.call(this, name, energy)

  this.breed = breed
}

Dog.prototype = Object.create(Animal.prototype)

Dog.prototype.bark = function () {
  console.log('Woof Woof!')
  this.energy -= .1
}

Dog.prototype.constructor = Dog 
```

Enter fullscreen mode Exit fullscreen mode

* * *

此时，如果我们想创建另一个子类，比如说`Cat`，我们将遵循相同的模式。

```
function Cat (name, energy, declawed) {
  Animal.call(this, name, energy)

  this.declawed = declawed
}

Cat.prototype = Object.create(Animal.prototype)
Cat.prototype.constructor = Cat

Cat.prototype.meow = function () {
  console.log('Meow!')
  this.energy -= .1
} 
```

Enter fullscreen mode Exit fullscreen mode

拥有一个基类和委托给它的子类的概念被称为**继承**，它是**面向对象编程(OOP)** 的主要部分。如果你来自不同的编程语言，很可能你已经熟悉 OOP 和继承。在 ES6 类之前，在 JavaScript 中，正如你在上面看到的，继承是一项艰巨的任务。你现在只需要理解当使用继承时的**，但是也要理解`.call`、`Object.create`、`this`和`FN.prototype`的巧妙组合——所有这些都是非常高级的 JS 主题。让我们看看如何使用 ES6 类完成同样的事情。**

首先，让我们回顾一下使用我们的`Animal`类从 ES5“类”到 ES6 类是什么样子。

* * *

```
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Animal.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

const leo = new Animal('Leo', 7) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
class Animal {
  constructor(name, energy) {
    this.name = name
    this.energy = energy
  }
  eat(amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  }
  sleep() {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  }
  play() {
    console.log(`${this.name} is playing.`)
    this.energy -= length
  }
}

const leo = new Animal('Leo', 7) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

既然我们已经将`Animal`构造函数重构为一个 ES6 类，接下来我们需要做的就是弄清楚如何重构我们的基类(`Dog`)。好消息是它更加直观。作为参考，在 ES5 中，这里是我们所拥有的。

```
function Dog (name, energy, breed) {
  Animal.call(this, name, energy)

  this.breed = breed
}

Dog.prototype = Object.create(Animal.prototype)

Dog.prototype.bark = function () {
  console.log('Woof Woof!')
  this.energy -= .1
}

Dog.prototype.constructor = Dog 
```

Enter fullscreen mode Exit fullscreen mode

在我们进入继承之前，让我们重构`Dog`来使用一个 ES6 类，就像我们在上一篇文章中学到的那样。

```
class Dog {
  constructor(name, energy, breed) {
    this.breed = breed
  }
  bark() {
    console.log('Woof Woof!')
    this.energy -= .1
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来很棒。现在，让我们弄清楚如何确保`Dog`从`Animal`继承。我们需要迈出的第一步非常简单。对于 ES6 类，你可以用这个语法
来`extend`一个基类

```
class Subclass extends Baseclass {} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的例子中，这将使我们的`Dog`类看起来像这样

```
class Animal {
  constructor(name, energy) {
    this.name = name
    this.energy = energy
  }
  eat(amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  }
  sleep() {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  }
  play() {
    console.log(`${this.name} is playing.`)
    this.energy -= length
  }
}

class Dog extends Animal {
  constructor(name, energy, breed) {
    this.breed = breed
  }
  bark() {
    console.log('Woof Woof!')
    this.energy -= .1
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 ES5 中，为了确保`Dog`的每个实例都有一个`name`和一个`energy`属性，我们使用了`.call`，以便在`Dog`实例的上下文中调用`Animal`构造函数。对我们来说幸运的是，在 ES6 中，这要简单得多。每当您扩展一个基类并且需要调用该基类的构造函数时，您调用`super`并传递它需要的任何参数。所以在我们的例子中，我们的`Dog`构造函数被重构为类似于

```
class Animal {
  constructor(name, energy) {
    this.name = name
    this.energy = energy
  }
  eat(amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  }
  sleep() {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  }
  play() {
    console.log(`${this.name} is playing.`)
    this.energy -= length
  }
}

class Dog extends Animal {
  constructor(name, energy, breed) {
    super(name, energy) // calls Animal's constructor

    this.breed = breed
  }
  bark() {
    console.log('Woof Woof!')
    this.energy -= .1
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。不使用`.call`，不使用`Object.create`，不用担心在原型上重置`constructor`——只需要`extends`基类并确保调用`super`。

* * *

JavaScript 的有趣之处在于，你在最近几篇文章中学到的相同模式被直接嵌入到语言本身中。之前你已经了解到所有的`Array`实例都可以访问像`pop`、`slice`、`filter`等数组方法的原因是因为所有这些方法都存在于`Array.prototype`上。

```
console.log(Array.prototype)

/*
  concat: ƒn concat()
  constructor: ƒn Array()
  copyWithin: ƒn copyWithin()
  entries: ƒn entries()
  every: ƒn every()
  fill: ƒn fill()
  filter: ƒn filter()
  find: ƒn find()
  findIndex: ƒn findIndex()
  forEach: ƒn forEach()
  includes: ƒn includes()
  indexOf: ƒn indexOf()
  join: ƒn join()
  keys: ƒn keys()
  lastIndexOf: ƒn lastIndexOf()
  length: 0n
  map: ƒn map()
  pop: ƒn pop()
  push: ƒn push()
  reduce: ƒn reduce()
  reduceRight: ƒn reduceRight()
  reverse: ƒn reverse()
  shift: ƒn shift()
  slice: ƒn slice()
  some: ƒn some()
  sort: ƒn sort()
  splice: ƒn splice()
  toLocaleString: ƒn toLocaleString()
  toString: ƒn toString()
  unshift: ƒn unshift()
  values: ƒn values()
*/ 
```

Enter fullscreen mode Exit fullscreen mode

您还了解到所有的`Object`实例都可以访问像`hasOwnProperty`和`toString`这样的方法的原因是因为这些方法存在于`Object.prototype`上。

```
console.log(Object.prototype)

/*
  constructor: ƒn Object()
  hasOwnProperty: ƒn hasOwnProperty()
  isPrototypeOf: ƒn isPrototypeOf()
  propertyIsEnumerable: ƒn propertyIsEnumerable()
  toLocaleString: ƒn toLocaleString()
  toString: ƒn toString()
  valueOf: ƒn valueOf()
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这是对你的挑战。有了上面的数组方法和对象方法列表，下面的这段代码为什么能工作呢？

```
const friends = ['Mikenzi', 'Jake', 'Ean']

friends.hasOwnProperty('push') // false 
```

Enter fullscreen mode Exit fullscreen mode

如果你看一下`Array.prototype`，没有一个`hasOwnProperty`方法。如果`Array.prototype`上没有`hasOwnProperty`方法，那么上面例子中的`friends`数组是如何访问`hasOwnProperty`的呢？原因是因为`Array`类扩展了`Object`类。所以在上面的例子中，当 JavaScript 发现`friends`没有`hasOwnProperty`属性时，它会检查`Array.prototype`是否有。当`Array.prototype`不执行时，它检查`Object.prototype`是否执行，然后调用它。这和我们在这篇博文中看到的过程是一样的。

JavaScript 有两种类型——**原语**类型和**引用**类型。

原始类型有`boolean`、`number`、`string`、`null`和`undefined`，是不可变的。其他的都是引用类型，它们都扩展了`Object.prototype`。这就是为什么你可以给函数和数组添加属性，这就是为什么函数和数组都可以访问位于`Object.prototype`的方法。

```
function speak(){}
speak.woahFunctionsAreLikeObjects = true
console.log(speak.woahFunctionsAreLikeObjects) // true

const friends = ['Mikenzi', 'Jake', 'Ean']
friends.woahArraysAreLikeObjectsToo = true
console.log(friends.woahArraysAreLikeObjectsToo) // true 
```

Enter fullscreen mode Exit fullscreen mode

* * *

这篇文章最初发表在 TylerMcGinnis.com 大学，是他们在 T2 大学高级 JavaScript 课程的一部分。