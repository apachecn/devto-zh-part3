# 简化的基于类和函数的编程

> 原文：<https://dev.to/iamshadmirza/class-vs-function-based-programming-simplified-1jkg>

这个问题在面试中经常被问到*你对函数式编程或面向对象编程的理解是什么？这个博客将会回答这个问题，但会以一种更简单、对初学者更友好的方式。

我会在最后分享关于这两个主题的一些深入阅读的链接，但现在这篇文章只会给你一个洞察和清晰的想法来区分这两者。*

## 为什么争论？

如果你是一个开发人员，正在或者将要开发一个有几千行代码的大型应用程序，你会立刻意识到你需要有效地管理你的代码，使它可伸缩并且没有错误，否则你会迷失方向。为此，你必须选择一种编程模式。

> 编程范式是一种处理问题和设计解决方案的编程风格或“方式”。

我们将讨论 JavaScript 中两个被谈论最多的范例。

*   面向对象编程/基于类的编程。
*   功能编程/基于功能的编程。

一些语言支持多种范例，但是不同的开发人员有不同的思维方式，他们通常喜欢使用特定的一种。

## 什么是函数式编程？

让我们听听维基百科是怎么说的

> 函数式编程是一种编程范式——一种构建计算机程序的结构和元素的风格——将计算视为数学函数的评估，并避免改变状态和可变数据。

***等等，你说简体和初学者友好？***

是的，我明白了。

简单来说，可以这么说

> 这是一种编程范例，它建议编写完全脱离功能的程序。确切地说是纯函数。

### 什么是纯函数？

函数基本上是一个代码块，接受一些输入，给出一些输出。这里需要注意的重要一点是 ***函数的输出将完全依赖于作为输入传递的参数*** 即，

> 如果给定相同的参数，它将返回相同的结果

纯函数是只使用传递给它的那些参数并计算结果的函数。

如果函数使用了函数之外的任何其他参数，则该函数不是纯函数。

#### 不纯函数的例子

假设一个函数正在计算一个圆的面积，并接收*半径*作为参数。

```
function areaOfCircle(radius){ 
    var area = radius * radius * PI;
    return area;
} 
```

为什么这是一个不纯函数？原因很简单，因为它使用了一个全局对象 *PI* ，该对象没有作为参数传递给函数。

因此，这里函数的输出不依赖于输入(仅服务于)。

> PI 是一个外部变量，它的值可以改变，在传递相同的输入时会给出不同的输出。

如果 PI = 50，半径= 10，则输出为 500。

取同样的半径= 10 但现在 PI = 40 则输出为 400。

#### 纯函数的例子

想象同样的功能，稍加改动。现在我们正在计算一个圆的面积，函数正在接收半径为*的*和作为参数的*圆周率*。

```
function areaOfCircle(radius, PI){ 
    var area = radius * radius * PI;
    return area;
} 
```

这个函数确实是一个纯函数，因为它不访问任何外部变量，并且输出完全依赖于传递给函数的参数。

*   对于参数 radius = 10 & PI = 3.14，我们将总是得到相同的结果:314.0
*   对于参数 radius = 10 & PI = 42，我们将总是得到相同的结果:4200

函数式编程遵循的另一个概念是*不变性*。

### 什么是不变性？

**不变的定义**是

: *不能或者不容易改变。*

在函数式编程中，我们将数据视为不可变的。

> 当数据是不可变的时，它的状态在创建后不能改变。如果你想改变一个不可变的对象，你不能。

相反，您可以用新值创建一个新对象，并用旧值替换它。

所以，我们的函数完全依赖于参数，作为参数传递的数据是*不可变的*，也就是说，它不能被改变。由此我们可以得出的结论是:—

*   给定一个参数 A →函数将总是返回值 B
*   给定一个参数 C →函数将总是返回一个值 D

代码肯定更容易测试，因此可以更好地防止 bug。此外，它更容易理解，因为它只做一件事，所以更容易维护。

有时，我们必须在应用程序中进行一些计算，这需要我们将函数视为实体。

> 其思想是将函数视为值，并像传递数据一样传递函数。通过这种方式，我们可以组合不同的功能来创建具有新行为的新功能。

这里出现了高阶函数的概念。我真的希望你的面试官会问什么是高阶函数。所以，

### 什么是高阶函数？

当我们谈到高阶函数时，我们指的是这样的函数:

*   接受一个或多个函数作为参数，或者
*   返回一个函数作为结果

你可能已经听说过过滤、映射和减少。让我们来看看这些

#### 滤镜

给定一个集合，我们希望按属性过滤。filter 函数需要一个 true 或 false 值来确定该元素是否应该包含在结果集合中。

**返回值:**只包含通过条件的项目的新数组。
我们举个过滤偶数的例子:-

```
let numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
let even = numbers.filter(function(num) {
    return num % 2 === 0;
});
console.log(even);
// expected output: [0, 2, 4, 6, 8, 10] 
```

在这里，我们可以看到`Array.filter`函数接受一组数字和一个函数作为参数，并返回一组偶数。

#### 地图

map 方法通过对集合的所有元素应用函数并从返回值构建新的集合来转换集合。

**返回值:**原始数组的副本，带有修改后的值(如果有)

```
let numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
let incremented = numbers.map(function(num) {
    num = num + 1;
    return num;
});
console.log(incremented);
// expected output: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11] 
```

在这里，我们可以看到`Array.map`函数接受数字数组和一个函数作为参数，并返回每个数字加 1 的数组。

#### 减少

reduce 的思想是接收一个函数和一个集合，并返回通过组合这些项而创建的值。

**返回值:**累加器

```
let numbers = [1, 2, 3, 4];
let sum = numbers.reduce(function(accumulator, currentValue) {
    return accumulator + currentValue;
});
console.log(sum);
// expected output: 10 
```

## 什么是面向对象编程？

它是基于类的编程范例，其中一切都围绕着类及其属性。

### 什么是阶级？

它是定义核心属性和功能的实体的设计

> 一个类只是一个定义了一切的**设计**，就是这样。

让我们看一个现实生活中的类的例子。

#### 人类

如果我们举一个人的例子，我们可以说它包含:—

*   身体部位:眼睛、嘴巴、耳朵、手、腿等。
*   身体机能:走、说、吃、看等。

现在假设 Human 是一个类/类型(我使用 type 是为了让类的使用更加清晰)。

我们可以归为*人类*的**型**。人类的设计中定义了所有的身体部分和功能。意味着所有属于这个*人类*类的人都会拥有这些属性(身体部位和功能)。让我们再听一遍。

> 类只是一个**设计**来定义它的属性。

现在，让我们继续人类的子类——男性和女性。继承的概念来了。

### 什么是继承？

考虑一下人类类，它已经定义了男性和女性共有的所有身体部分和功能。但是无论如何我们都需要男女班的设计。
我们能做的是创建两个新的类，男性和女性，因为男性和女性也是类，他们可以使用*继承*的概念继承人类类的一切。

> 雄性和雌性是可以包含其自身属性的类，即特定于雄性和雌性的属性。

这样，我们现在有了两个新的类雄性和雌性，它们包含了人类类的所有属性以及它们特有的属性。

> 类只是一个设计/蓝图，仅此而已。他们没有实体存在。

那么我们如何使用这些类呢？答案是*物体*

### 什么是物体？

现在考虑一个特定的人。你，我或者任何人。我们可以说我们属于一个男性或女性阶层。因此，我们是这个引用中的对象。我们拥有男性或女性阶级和人类阶级的所有属性(因为遗传)。
我们是物质存在，*物体*。

> 对象是一个类的实例，它有一个物理存在。对于人类，我们只是指一种*类型/种类*。

我是 Shad，我是班上男生的对象。我有一个物理存在，而类只是一个逻辑定义，它定义了我们的类型/种类。你可以说:-

> Shad 是雄性类的对象

还有一些其他的概念，如*抽象*、*封装*和*多态*，这些都属于面向对象编程。所有这些特性一起帮助定义一个程序的结构。

因此，OOP 代码非常容易理解。不那么容易的是首先决定如何将一个应用程序分解成这些小对象。随着时间的推移，您将获得经验，并使更多的代码符合这一理论概念。

## 让我们潜入更深的地方

我希望你已经对面向对象编程或者说基于类的编程有了一个基本的概念。对我来说，一开始要掌握一切有点难。为了让事情更清楚，我们将讨论另一个真实世界的例子，但现在是用`code`。

考虑一个购物用例，您将产品放入购物篮，然后计算您必须支付的总价。如果你用你的 JavaScript 知识编写没有 OOP 的用例，它会是这样的:

```
const bread = new Product('bread', 1);
const water = new Product('water', 0.25);
const basket = new Basket();
basket.addProduct(2, bread);
basket.addProduct(3, water);
basket.printShoppingInfo(); 
```

让我们根据刚刚学过的东西来破译密码。

*   产品是定义产品属性的类。
*   `new`关键字用于创建对象(实际术语是实例化的)。
*   面包和水是产品类的两个对象。他们拥有一个*产品*的所有属性。
*   `Basket()`是另一个类，它返回一个我们存储到变量`basket`(小写)的对象。
*   有了这个`basket`对象，我们可以使用在`Basket()`类中定义的函数。`addProduct()`就是这些功能之一。
*   第四和第五行代码通过调用以数量和名称为参数的函数将产品添加到购物篮中。
*   最后，我们通过调用`Basket()`类中定义的`printShoppingInfo()`函数来打印购物篮中的内容。

这里的好处是你几乎可以像阅读真正的英语句子一样阅读代码，并且你可以很容易地知道发生了什么。让我们更好地看看我们刚刚使用的类:-

1.  产品类别

```
class Product {
    constructor(_name, _price) {  
        this.name = _name;  
        this.price = _price;
    }
    getName() {    
        return this.name;  
    }
    getPrice() {    
        return this.price;  
    }
} 
```

1.  篮子类

```
class Basket {
    constructor() {
        this.products = [];
    }
    addProduct(amount, product) {
        this.products.push(...Array(amount).fill(product)); 
    }
    calculateTotal() {
        return products.map(function(product){
            return product.getPrice();
            })
            .reduce(function(a, b){ 
            return a + b;
        }, 0);  
    }
    printShoppingInfo() {    
        let total = this.calculateTotal();
        console.log('one has to pay in total: ' + total);  
    }
} 
```

> 每个类中的`constructor`是一个函数，它在每次对象被实例化时执行。

**产品**具有参数 _ 名称和 _ 价格。每个新对象都在内部存储这些值。

类**筐**创建新对象不需要任何参数。实例化一个新的 Basket 对象只是生成一个空的产品列表，程序可以在以后填充它。让我们继续我们之前离开的其他概念。

### 封装

简而言之，封装阻止了对数据的访问，除非通过对象的函数。

> 它是一个保护屏障，防止数据被这个屏障之外的代码访问。

我们之前写的代码有一个缺点(我是故意这样写的)。通过添加 *this* 关键字，我们可以从外部完全访问这些属性。所以每个人都可以访问和修改它:

```
const bread = new Product('bread', 1);
bread.price = -10; 
```

这是你想要避免的，因为价格低于零是没有意义的，不是吗？

对象应该对自己的数据有独占的控制权。换句话说，这些对象“封装”了它们的数据，并防止其他对象直接访问这些数据。访问数据的唯一方式是通过写入对象的函数间接访问。
代码现在看起来像这样:-

```
function Product(_name, _price) {  
    const name = _name;  const price = _price;
    this.getName = function() { return name;  };
    this.getPrice = function() { return price;  };
} 
```

> 注意:JavaScript 中的类是 facade，它是在开发的后期添加的。JavaScript 最初是一种基于原型的语言。您可以通过在静态面向对象语言(如 Java)中将 price 定义为 final 来防止同样的问题。

在 JavaScript 中，防止这种情况的一种方法是冻结对象。

```
const bread = new Product('bread', 10);
Object.freeze(bread)
bread.price = 20; // this will not change the price of bread to 20 
```

> 注意:不建议在生产中使用。

### 抽象

抽象是对用户隐藏实现细节的过程。只向用户提供功能。

另一种方式，它只向用户显示必要的东西，隐藏内部细节，例如，在你键入文本和发送消息的地方发送短信。你不知道关于消息传递的内部处理。
让我们看看代码中的一个例子:-

```
basket.printShoppingInfo(); 
```

该代码打印添加产品的总量。调用这个函数的用户不知道如何计算总值的细节。这是一种抽象的形式。用户完全看不到内部细节。

### 多态性

它被定义为

> 以几种不同形式出现的状态

JavaScript 是一种动态类型的语言，这个特性更适合在静态类型的语言如 Java 上说明。

简单来说，我们可以说一个功能可以采取多种形式。让我们来看一个例子:-

```
function Dog(name) {
  this.name = name;
}

var dog1 = new Dog('Gabby');
console.log(dog1.toString()); //// returns [object Object] 
```

默认情况下， *toString()* 方法由 object 的每个子对象继承。

> toString()返回“[对象类型]”，其中 type 是对象类型。

现在，让我们尝试给这个 *toString()* 函数多一种形式来实现多态性。

```
function Dog(name) {
  this.name = name;
}

var dog1 = new Dog('Gabby');
Dog.prototype.toString = function dogToString() {
  return 'Name of your doggo is ' + this.name;
}
console.log(dog1.toString()); //// returns "Name of your doggo is Gabby" 
```

看，现在 *toString()* 方法的实现被改变了，我们基本上覆盖了这个方法。

我们已经涵盖了面向对象编程的所有四个支柱，即

*   遗产
*   包装
*   抽象
*   多态性

## 哪个比较好？

选择权完全在你，尽管我会提出我的想法。如果某件事让你的系统变得复杂，那很可能不是好事。复杂？

> "复杂性是任何使软件难以理解或修改的东西."—约翰·奥特豪特

简而言之，如果一个范式具有以下特征，那么它就是更好的

*   降低代码的复杂性
*   更易于维护
*   更容易修改
*   减少错误发生的机会。

面向对象编程的一些优点和缺点如下

1.  团队可以同时在不同的类上工作，因此它提供了并行开发。
2.  类是可重用的。
3.  更容易阅读和维护。
4.  可扩展性太强，大量臃肿、不必要的代码。
5.  这可能会导致重复。

函数式编程的一些优点和缺点如下

1.  允许您编写更具压缩性和可预测性的代码。
2.  程序员避免任何共享状态或副作用，这消除了多个函数竞争相同资源所导致的错误。
3.  一个函数做一件事，导致更少的错误。
4.  更容易测试。
5.  较少的样板文件，不必要的代码很容易避免。
6.  可读性降低，因为生成的代码通常更加抽象。
7.  更陡峭的学习曲线，从零开始学习并不容易。

毕竟是开发商的选择。选择最适合自己的。

希望对你有帮助。

鲥鱼

## 引用

*   函数式编程基本原理介绍- [@leandrotk_](https://twitter.com/LeandroTk_) 链接:[https://www . freecodecamp . org/news/an-introduction-to-the-basic-principles-of-Functional-Programming-a2 C2 a15 c 84/](https://www.freecodecamp.org/news/an-introduction-to-the-basic-principles-of-functional-programming-a2c2a15c84/)
*   对象和类-studytonight.com 链接:[https://www.studytonight.com/java/object-and-classes.php](https://www.studytonight.com/java/object-and-classes.php)
*   面向对象编程入门 www.freecodecamp.org 链接:[https://www . freecodecamp . org/news/an-introduction-to-object-oriented-programming-in-JavaScript-8900124 e316 a/](https://www.freecodecamp.org/news/an-introduction-to-object-oriented-programming-in-javascript-8900124e316a/)
*   MDN 网络文档术语链接:[https://developer.mozilla.org/en-US/docs/Glossary](https://developer.mozilla.org/en-US/docs/Glossary)