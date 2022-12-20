# 三个小小的创造模式——设计模式介绍

> 原文：<https://dev.to/maxwell_dev/the-three-little-creational-patterns-a-design-patterns-intro-1dmn>

*更新:在评论区反馈后，更新了几个代码示例。*

设计模式是程序员必须牢记的，至少对我来说是这样。这些是对常见问题的既定解决方案，有助于保持代码的可维护性和松散耦合。一个人越了解他们，就越容易解决我们面临的所有问题。

一旦你学会了它们，你会发现它们无处不在。我用适配器来连接 API，用单件来连接 Ember 服务，用状态和观察器来管理 ui，用外观来管理对象和在假日愚弄我的亲戚。

然而，作为一个没有计算机科学背景的人，学习它们是一场斗争。每种解释或每本书都充斥着术语，需要仔细筛选。其中一些我仍然不太明白。我几乎不知道轻量级模式是如何工作的，任何说他们知道的人都是骗子。

所以我的想法是用故事来解释设计模式。故事也很好地建立和解决共同的问题，但更容易理解。也就是，**童话故事！**

## 介绍童话般的设计模式

本系列旨在帮助人们学习设计模式的基本功能和用途。这些例子是最基本的，也是了解更多信息的起点。它们也是用 JavaScript 编写的，但是这些思想适用于任何面向对象的语言。

在这里，我将看看五种创造性的设计模式。这些是创建和管理更易维护、副作用更少的对象的模式。

而说到创造事物，我想到的是三只小猪！我们开始吧。

## 用工厂建造稻草屋

在这个版本的童话中，假设每只小猪都在闯入房地产市场。第一只猪，很自然地，通过建造稻草屋来做到这一点。他决定用 JavaScript 类来制作它们。

猪写这个基类是为了做一个稻草房子。它需要的唯一参数是所需稻草捆的数量。

```
class StrawHouse {
  constructor(straw) {
    this.straw = straw;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

随着订单的到来，猪意识到这个类对于建造大量的房子来说效率不高。顾客给他想要的房子高度，如果他们想要用更多的稻草加固房子。猪每次都要做数学运算，有时同样的运算不止一次。

以这种方式创建如此多的 house 实例是令人疲惫的，所以 pig 使用工厂模式来管理每个实例背后的工作。

```
class StrawHouseFactory {
  static create(height, specs) {
    const strawBase = (specs === 'reinforced') ? 350 : 150,
          amountOfStraw = height * strawBase;

    return new StrawHouse(amountOfStraw);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**这个稻草屋工厂让他可以从客户给他的信息中快速创建一个不同的稻草屋，进行计算并返回所需的`StrawHouse`类实例。**这是一个简单而强大的好处，这很有意义，因为工厂可以说是最简单的创造模式。

```
const smallStrawHouse = StrawHouseFactory.create(10),
      strongStrawHouse = StrawHouseFactory.create(25, 'reinforced'); 
```

Enter fullscreen mode Exit fullscreen mode

额外的好处是，工厂类和住宅类并没有紧密地结合在一起。他可以相当容易地改变任何一个类，而不会破坏另一个类。这是你将在其他创造模式中看到的一个普遍的好处，实际上是所有的设计模式。

## 用原型建造一个棍子房子

第二只猪正在做木头房子，但是面临一个不同的问题。他所有的客户都想建造外观相似的木屋。他可以尝试他哥哥建造稻草房子的工厂方法，但他会一遍又一遍地做同样类型的稻草房子。编写代码来生成相同的实例是重复的，他想要更高效的东西。

第二只猪使用原型模式。假设一群客户都想要一个小棒棒的房子。他将只制作一个(原型)并包含一个根据需要复制的方法，而不是为他们制作多个木棍房子的实例。

```
class StickHouse {
  constructor(height, sticks) {
    this.height = height;
    this.sticks = sticks;
  }
}

class StickHousePrototype {
  constructor(height) {
    this.height = height;
    this.sticks = height * 100;
  }

  copy() {
    return new StickHouse(this.sticks, this.height);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这减少了重复性，并确保每一个都完全相同。他甚至可以对每个副本进行修改，以获得额外的灵活性。

```
const smallStickHouse = new StickHousePrototype(15),
      largeStickHouse = new StickHousePrototype(50);

const housesForFriends = {
  'Amy': smallStickHouse.copy(),
  'Bob': smallStickHouse.copy(),
  'Cole': smallStickHouse.copy(),

  'Dingus': largeStickHouse.copy(),
  'Eragon': largeStickHouse.copy()
} 
```

Enter fullscreen mode Exit fullscreen mode

总的来说，如果您需要大量完全相同或几乎相同的实例，这种模式效果最好。

## 用建筑工人建造砖房

第三只猪是卖砖房的，这是一个更远大的目标。砖房更棘手，需要几个步骤。此外，由于他们在森林里是新来的，客户经常推迟决定它应该有多大，直到中途。第三只猪意识到他需要一种模式来处理所有这些额外的复杂性，而不会不知所措。

他偶然发现了建筑者模式，他的祈祷得到了回应！

这是做砖房的第三个猪的基类。它需要三个尺寸和水泥量来把砖块粘在一起。

```
class BrickHouse {
  constructor(width, length, height, cement) {
    this.width = width;
    this.length = length;
    this.height = height;
    this.cement = cement;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，猪编写他的构建器类。他想从中得到的主要东西是:

*   设置每个尺寸的单独方法。构建器应该能够花一些时间，暂停运行其他代码，然后从停止的地方继续。
*   一旦知道水泥的全部尺寸，计算需要多少水泥的方法。
*   最后一个方法是获取所有这些信息并返回完工的砖房。

```
class BrickHouseBuilder {
  setWidth(width) {
    this.width = width;
    return this;
  }

  setLength(length) {
    this.length = length;
    return this;
  }

  setHeight(height) {
    this.height = height;
    return this;
  }

  addCement() {
    this.cement = this.getCementBase() + this.getCementForBetweenBricks();
    return this;
  }

  getFloorSize() {
    return this.width * this.length;
  }

  getCementForBetweenBricks() {
    return this.getFloorSize() * 0.25;
  }

  getCementBase() {
    return this.getFloorSize() * (this.height / 5);
  }

  build() {
    return new BrickHouse(this.width, this.height, this.length, this.cement);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我知道这很难接受。但是要知道猪可以调用每一个方法，用它需要的任何数据，来精心建造每一个房子。

```
const newBrickHouse = new BrickHouseBuilder(),
      smallBrickHouse = newBrickHouse.setWidth(5).setLength(5).setHeight(5).addCement().build(); 
```

Enter fullscreen mode Exit fullscreen mode

当他不得不暂停代码中的构造时，它也能工作。

```
const largeBrickHouse = newBrickHouse.setWidth(20).setLength(25);

// Extra calculations here as the client decides what to do

largeBrickHouse.setHeight(20).addCement().build(); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，构建器非常适合抽象出制作更大的对象所需的更复杂的步骤和计算，以及多个语句。

## 单身创业

让我们把这个童话快进到未来。三只小猪遇到了大灰狼，他们都成立了一家房地产公司，名为猪与狼伙伴房地产有限责任公司。这是一家很棒的新公司，他们想用 JavaScript 管理他们的公司信息。

这三只猪意识到它们不能使用以前的任何模式，因为它们是为一个类的多个实例制作的。他们公司只有一个实例，因此该类只有一个实例。否则，想成为房地产动物的人可能会试图复制并接管他们一生的工作！

这就是汗衫的闪光点。设置了单例，因此只能创建一个实例。任何试图创建一个新的引用回原来的。

```
class RealEstateCompany {
  constructor(employees) {
    if (typeof RealEstateCompany.instance === 'object') {
      return RealEstateCompany.instance;
    }
    RealEstateCompany.instance = this;

    this.employees = employees;

    return this;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面你会看到有人试图制作该公司的两个实例，第二个是用不同的其他野生动物制作的。

```
const company = new RealEstateCompany(['Pig 1', 'Pig 2', 'Pig 3', 'Wolf']),
      fakeCompany = new RealEstateCompany(['Zebra', 'Aardvark', 'Chris Pratt']) 
```

Enter fullscreen mode Exit fullscreen mode

由于是单例的，`fakeCompany`将返回与`company`相同的结果。然后，pigs 可以在程序中的任何地方引用他们真实的公司，并获得原始实例，包括在其他地方对它所做的任何更改。适当的单例是可靠的“真理的单一来源”

如果你想了解更多，我已经在这里写了一个更实用的、与口袋妖怪相关的单例使用方法。

[![maxwell_dev](img/e584c820fe4a86bb236ed5cdf1a3fe68.png)](/maxwell_dev) [## Eevees，测验，和单曲，哦，我的天！

### 最大 Anton ucci Apr 5 ' 195 分钟读数

#javascript #ember #showdev](/maxwell_dev/eevees-quizzes-and-singletons-oh-my-1blh)

## 用抽象工厂接管房市

这些猪拥有一切。他们在市场上有三种类型的房子，他们有一家公司，他们每个人都有自己的游泳池和稳定的女朋友，他们希望有一天能结婚。他们还有一个问题就是大规模的混乱。

为了建造他们所有的房子，猪需要管理许多创造性的模式:

*   稻草屋工厂
*   棍子房子的原型制造者
*   砖房的建造者。

谢天谢地，还有最后一个创造性的模式来管理它们:抽象工厂！

常规工厂创建单个类的实例，而抽象工厂则同时创建多个类的实例。猪需要管理的不是一只，而是三只。抽象工厂可以调用他们需要的任何类，甚至可以添加一些额外的逻辑来涵盖常见的用例。

```
class PigHouseAbstractFactory {
  static strawHouse(size) {
    if (size === 'large') {
      StrawHouseFactory.create(25, true);
    } else {
      StrawHouseFactory.create(10)
    }
  }

  static stickHouse(size) {
    if (size === 'large') {
      return new newStickHousePrototype(50).copy();
    } else {
      return new newStickHousePrototype(15).copy();
    }
  }

  static brickHouse(size) {
    if (size === 'large') {
      return new BrickHouseBuilder.width(20).length(25).height(20).height(20).getCement().build();
    } else {
      return new BrickHouseBuilder.width(5).length(5).height(5).getCement().build();
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种模式允许它们从单个类开始填充任何顺序，并且不会将任何依赖类耦合得太紧。

```
const smallStrawHouse = PigHouseAbstractFactory.strawHouse(),
      largeStrawHouse = PigHouseAbstractFactory.strawHouse('large'),
      smallStickHouse = PigHouseAbstractFactory.stickHouse(),
      largeStickHouse = PigHouseAbstractFactory.stickHouse('large'),
      smallBrickHouse = PigHouseAbstractFactory.brickHouse(),
      largeBrickHouse = PigHouseAbstractFactory.brickHouse('large'); 
```

Enter fullscreen mode Exit fullscreen mode

看起来猪猪们在这个童话故事中有一个幸福的结局，在房地产业有一个光明的未来。

## 设计模式探索才刚刚开始

我希望在本系列中涵盖经典的四人组设计模式的全部 23 种。这些文章绝不是你需要了解的全部内容，但是我希望它们可以作为学习每一篇文章的复杂性的简单基础。在学习它们的时候，我努力寻找初学者友好的介绍，希望这些能帮助其他人避免同样的命运。

*未完待续...*

[封面图片由 SafeBooru.org 提供](http://safebooru.org/index.php?page=post&s=view&id=2484667)