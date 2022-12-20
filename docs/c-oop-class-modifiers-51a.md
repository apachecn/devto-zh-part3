# C# OOP:类修饰符

> 原文：<https://dev.to/aromig/c-oop-class-modifiers-51a>

跳转到:

*   [封装](#encapsulation)
*   [类访问修饰符](#class-access-modifiers)
    *   [公共](#public)
    *   [私人](#private)
    *   [自动实现的属性](#auto-implemented-properties)
    *   [受保护的](#protected)
    *   [封存](#sealed)
*   [其他修饰词](#other-modifiers)
    *   [常量&只读变量](#constants--read-only-variables)
    *   [静态](#static)
*   [类构造函数](#class-constructors)
*   [类析构函数](#class-destructors)
*   [这个](#this)

在[之前的文章](https://dev.to/aromig/c-oop-data-types-structs-and-classes-453p)中，我们讨论了基本形式的类。在这一点上，我们只是看到了冰山一角。在这里，我们将探索我们还能利用它们做些什么。

## 封装

如前所述，封装是在一个逻辑单元中组织相关的项目(如字段、属性、方法等)。这个过程不仅仅是组合一个类中的成员。它的目的更多的是“保护”一个类的成员，以及对程序的其他部分隐藏信息的能力。我们可以使用上面的修饰语来帮助这一努力。

## 类访问修饰符

可以用特殊的关键字修改对象，以改变它们的可访问性。这些修饰符改变了我们可以对对象做什么的范围，以及其他名称空间如何访问它们。

> 名称空间是对象的逻辑集合，为项目中的类、结构、函数、变量等提供层次结构。通常，单个类或许多相关类将是名称空间的一部分，或者是现有名称空间的一部分。
> 
> [微软的更多信息](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/namespaces/)

### 公共

标记为 **public** 的对象可以被任何其他代码访问，不管它是在同一个类中还是在引用它的另一个类中。对访问它们没有限制。

```
class Coords
{
  public int x;
  public int y;
}

static void Main()
{
  Coords point = new Coords();
  point.x = 40;
  point.y = 80;
  // We can access x & y directly because they are public
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 私人

私有对象只能被它直接声明的类或结构访问。如果未指定访问修饰符(例如 private 或 public ),则默认情况下对象是私有的。

```
class Dog
{
  private string name;

  public string Name
  {
    get { return name; }
    set { name = value; }
  }
}

static void Main()
{
  Dog doggo = new Dog();

  // Cannot do this because 'name' is private
  string dog_name = doggo.name;

  // Instead access 'name' indirectly via the Name property
  string dog_name = doggo.Name;

  // And we can use the same property for setting the value on 'name'
  doggo.Name = "Doggo";
} 
```

Enter fullscreen mode Exit fullscreen mode

> 我们为什么要这样做？
> 
> 这个问题的答案叫做**抽象**。虽然封装是在一个逻辑单元中组织项目的过程，但是抽象通过隐藏不必要的细节和提供属性和方法使它更进一步，以便程序员在它的基础上实现更复杂的逻辑。
> 
> 真实世界抽象的一个例子是银行交易。你可以存钱、取钱、查余额、付账等等。这些活动背后都有流程，但你不必担心，尤其是有了在线账单支付系统:你知道有一个写支票并将其邮寄给有线电视公司的流程，但你不必做这一部分。你只需在网站上填写表格，它就会得到处理。主要的辅助工作已经被抽象掉了。
> 
> 这个概念在编码框架中非常明显，在这里我们可以写出一个应用程序，而不需要对更细节的实现进行编码，比如数据库连接、URL 路由和 DOM 操作。
> 
> 抽象将接口与实现分开。在 OOP 中，我们不希望任何类或名称空间能够改变类字段的值。我们希望这些更改通过一个公共的渠道来实现，比如一个属性或方法，这样所有的更改都是统一的，并且允许数据、验证和安全性的管理。

#### 自动实现的属性

如果除了 getter & setter 之外，属性不需要额外的逻辑，我们可以使用自动实现的属性来节省几行代码。

```
// Instead of
class Dog
{
  private string name;

  public string Name
  {
    get { return name; }
    set { name = value; }
  }
}

// Use this
class Dog
{
  public string Name { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 受保护

标记为 **protected** 的对象只能在它的类以及从它派生的任何类中访问。在本系列的后面，我们将探索由其他类构成的类。protected 关键字也允许类成员成为其中的一部分。

### 封存

一个**密封的**类阻止其他类继承它，与 protected 修饰符完全相反。

### 访问修饰符汇总

| 修饰语 | 易接近 |
| --- | --- |
| 公众的 | 课堂内外 |
| 私人的 | 仅在班级内 |
| 保护 | 在类和派生类中 |
| 密封的 | 但阻止继承 |

* * *

## 其他修饰词

### 常量&只读变量

您可以使用`const`关键字来表示变量是一个常量，它在编译时被解析。也就是说——当程序被编译时,`const`声明的变量被替换为它被赋予的值。

常量非常适用于标准路径或自定义值，它们在代码中作为名称而不是数字值更容易阅读。

```
const string PUBLIC_URL = "http://www.site.com/";
const int ACCESS_LVL_ADMIN = 5; 
```

Enter fullscreen mode Exit fullscreen mode

使用`readonly`修饰符来防止变量在构造后被修改。

Readonly 和 constants 在 3 个主要方面有所不同:

*   常数在声明时必须初始化。只读字段不需要。
*   只读字段值可以在构造函数中更改。
*   可以为只读字段分配一个作为计算结果的值。常数不能。

```
class Age
{
  readonly int year;
  Age(int year)
  {
    this.year = year;
  }
  void ChangeYear()
  {
    year = 1942; // This won't work; it will give a compile error.
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 静态

标记为**静态**的变量和方法只*属于*类，而不是被创建的对象。在内存中，静态成员只有一个副本。该成员必须由类访问，而不是由对象访问。在 Main()方法的下例中，尝试将`Cat.Meow();`改为`c2.Meow();`，并注意报告的错误。

```
class Cat
{
  public static int count = 0;
  public static void Meow()
  {
    Console.WriteLine("Meow");
  }
  public Cat()
  {
    count++;
  }
}

public static void Main()
{
  Cat c1 = new Cat();
  Cat c2 = new Cat();
  Console.WriteLine(Cat.count); // 2
  Cat.Meow(); // Meow
} 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/x2hLJ1)

静态类只能包含静态成员，不能实例化为对象。静态类的常见例子是 Main 和 Math 类。

常量成员(由`const`初始化)根据定义是静态的。

* * *

## 类构造函数

构造函数是在类(或结构)中定义的方法，在实例化时调用。它和它的类同名，没有返回类型，并且总是公共的。

```
class Person
{
  public string Name { get; set; }

  // v-- this is the constructor
  public Person()
  {
    Console.WriteLine("Hello there!");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，每次创建一个人对象时，“你好！”将被写入控制台。

构造函数可以用来通过参数设置初始值。

```
class Person
{
  private string name;

  public Person(string _name)
  {
    name = _name;
  }

  public string getName()
  {
    return name;
  }
}

public static void Main()
{
  Person bob = new Person("Bob");
  Console.WriteLine(bob.getName()); // outputs: "Bob"
} 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/5EVyaM)

## 类析构函数

析构函数是在类中定义的方法，当对象被销毁时调用。它和它的类有完全相同的名字，就像一个构造函数，除了它的前缀是一个波浪号(~)。

```
class Person()
{
  ~Person()
  {
    // things to do when an object is destroyed
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 这个

`this`关键字可以在一个类中使用，以引用该类的当前实例(当前对象)。如果方法参数和类字段具有相同的名称(比如在构造函数中)，它也可以用来区分它们。

如果你仍然不明白`this`指的是什么，那就跳出它的代码块(括号`{ }`所在的地方)一层。见下文:

```
class Person
{
  public string name { get; set; }

  public Person(string name)
  {
    // using this to distinguish between the class variable name & the parameter name
    this.name = name;
  }

  public void GetDetails()
  {
    Console.WriteLine("Name: {0}", name);

  // using this to pass the current instance of this class to another class method
    Console.WriteLine("Favorite Number: {0}", Stuff.MakeNumberFromName(this));
  }
}

class Stuff
{
// This method takes the name from a Person object that is passed to it
// and multiplies its length by 10.
  public static int MakeNumberFromName(Person p)
  {
    return p.name.Length * 10;
  }
}

public static void Main()
{
  Person Bob = new Person("Bob");
  Bob.GetDetails();
  // outputs:
  // Name: Bob
  // Favorite Number: 30
} 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/kkrWBn)

使用`this.name`将构造函数参数“name”分配给类属性“name ”,以明确指出您在代码中谈论的是哪一个。

此外，我们使用`this`作为 Stuff 类(独立于 Person 类)中 MakeNumberFromName 方法的参数。这听起来像是我们把 Person 类发送给它，但我们没有。我们发送该类的当前实例，当程序运行时，当前实例是我们从 Person 类创建的 Bob 对象。

* * *

## 那可是屈指可数！

现在我们知道了如何将关键字应用于类、变量、对象等，以控制它们如何被访问和使用。有很多东西需要学习，我们很快就会进入有趣的部分，但是这个基础将会在我们学习更多关于职业的知识时显示出它的用途。