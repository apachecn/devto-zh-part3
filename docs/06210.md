# C# OOP:继承和多态性

> 原文：<https://dev.to/aromig/c-oop-inheritance-polymorphism-m5f>

跳转到:

*   [继承](#inheritance)
    *   [base()](#base)
*   [多态性](#polymorphism)
    *   [抽象类](#abstract-classes)
    *   [接口](#interfaces)

让我们学习如何创建更复杂的类以及我们可以从中创建的对象。

## 继承

在面向对象编程中，我们可以从类中创建对象。但是，如果我们想创建一个和另一个类一样(属性和方法)但更多的类呢？我们应该将一个类中的所有内容复制粘贴到另一个类中吗？如果这种想法让你有点畏缩，没关系。我们可以使用继承来做到这一点。

继承允许一个类接受(继承)另一个类的属性和方法。用作新类基础的类被称为**基类**，从其继承的类被称为**派生类**。派生类不仅包含基类的属性，还包含自己的属性。从一个基类可以有多个派生类。此外，派生类可以是另一个类的基类。

> 基/派生关系可以理解为一个**是一个**子句。
> 
> *   苹果是一种水果。
> *   正方形*是一个*形状。三角形*也是一个*形状。
> *   哈巴狗*是*狗，狗*是*动物。

在 C#中，我们用冒号:后跟基类名来指定我们从另一个类继承。然后我们可以引用基本属性和方法。

```
class Animal
{
  public int Legs { get; set; }
  public bool hasFur { get; set; }

  public Animal()
  {
    // Animal constructor
  }
}

class Dog : Animal {
  public string Name { get; set; }

  public Dog(string name) : base()
  {
    // Dog constructor
    Legs = 4;
    hasFur = true;
    Name = name;
  }

  public void Bark()
  {
    Console.WriteLine("Woof!");
  }
}

public static void Main()
{
  Dog doggo = new Dog("Doggo");
  Console.WriteLine("{0} has {1} legs.", doggo.Name, doggo.Legs);
  // Doggo has 4 legs.
  doggo.Bark();
  // Woof!
} 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/68tjy7)

注意，这两个类都有自己的构造函数。创建对象时会调用两个构造函数和析构函数(如果定义了的话)。调用它们的顺序概述如下:

1.  基本构造函数
2.  派生构造函数
3.  派生析构函数
4.  基础析构函数

基本上，构造时调用顺序是向内的(从基类到派生类),析构时是向外的。如果有多个级别的派生类(例如，base >从 base 派生>从第一个派生类派生)，则遵循此模式。

### 基数()

注意 Dog 类的构造函数中的关键字`base`。当与派生类构造函数一起使用时，它指定派生类的构造函数应该从哪个基类构造函数派生。我们需要指定哪一个，因为一个类可以有多个构造函数:一个缺省的(没有参数)，有参数的，其他的有不同的参数(重载的构造函数)。

> 被访问的基类是在类声明中指定的基类，不管该类本身是否基于另一个类。

base 关键字还可以用于从派生类中访问基类的成员。如果上面的`Animal`类有自己的方法，我们可以从`Dog`类的方法中调用它。即`base.MethodName();`

* * *

## 多态性

[字典定义](https://www.merriam-webster.com/dictionary/polymorphism):以不同形式存在或呈现的性质或状态

多态声明了一个接口，但是它的实现细节留给了派生类。

> 简单地说，一个类的成员可以在不同的派生类中以不同的方式实现..

**定义多态方法时的关键字**

*   虚拟-在基类成员上使用
*   覆盖-用于派生类成员

让我们为角色扮演游戏创建一个简单的英雄。这个*英雄*可以专攻特定的武器学科。我们将创建一个普通英雄的基类，每个职业都是从它派生出来的。

```
// Base Class
class Hero {
  public string Weapon { get; set; }
  public string Name { get; set; }

  public Hero(string name) {
    Name = name;
  Weapon = "pair of fists";
  }

  public virtual void Attack() {
  Console.WriteLine("Punch Punch!");
  }
}

// Derived Classes
class Rogue : Hero {
  public Rogue(string name) : base(name) {
    Name = name;
    Weapon = "dagger";
  }

  public override void Attack() {
    Console.WriteLine("Stab Stab!");
  }
}

class Archer : Hero {
  public Archer(string name) : base(name) {
    Name = name;
    Weapon = "bow & arrows";
  }

  public override void Attack() {
    Console.WriteLine("Pew Pew!");
  }
}

class Warrior : Hero {
  public Warrior(string name) : base(name) {
    Name = name;
    Weapon = "sword";
  }

  public override void Attack() {
    Console.WriteLine("Slash Slash!");
  }
}

public static void Main() {
  Hero avatar = new Rogue("Bob");
  Console.WriteLine("{0}'s weapon is a {1}.", avatar.Name, avatar.Weapon);
  // Bob's weapon is a dagger.
  avatar.Attack();
  // Stab Stab!
} 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/6yjeNX)

多态性的使用可能不会马上显现出来，但是如果没有它，基类将不得不考虑我们的英雄类型的每一个版本。这可能导致过于复杂的[意大利面条式代码](https://en.wikipedia.org/wiki/Spaghetti_code)和重载方法(具有多个实现的方法，稍后详细解释)，所有这些都在基类中。这些变化属于派生类，保持它的组织性。多态性的目的与拥有可维护的简洁的语义代码有很大关系。

使用这个例子，不同的武器属性不能放在基础英雄类中。不是所有的英雄都知道如何使用所有的武器，只有那些用它们训练的英雄才知道-因此每个职业都有衍生职业。

在。在 Main()方法中，继续改变我们用来创建我们的角色的职业(例如，把盗贼变成弓箭手，战士，或者仅仅是普通的英雄)。

### 抽象类

多态性可以更进一步，基类可以被认为是抽象的，这里根本没有实现。一个常规的基类可以有多态属性&方法**和**有自己的没有被派生类定义的属性。

在抽象类中，实现必须在派生类中定义。

**抽象类:**

*   无法实例化；它们必须源自
*   可能包括具体和抽象成员
*   必须在所有继承的抽象成员的派生类中定义它们的实现

为什么要这么做？因为在某些情况下，虚拟成员没有必要在基类中有单独的定义。该类被声明为抽象类，以提供一种*模板*。

```
abstract class Shape {
  public abstract void Draw();
}

class Circle : Shape {
  public override void Draw() {
    // draw a circle!
    Console.WriteLine("Imagine a circle is drawn");
  }
}

class Rectangle : Shape {
  public override void Draw() {
    // draw a rectangle!
    Console.WriteLine("Imagine a rectangle is drawn");
  }
}

public static void Main() {
  Shape c = new Circle();
  c.Draw();
  // Imagine a circle is drawn
} 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/LQYCfU)

### 界面

接口是一个完全抽象的类，只包含抽象的属性和方法(没有字段/变量)。一个接口的名字通常以大写字母`I`开头。

一个接口简单地描述了一个类应该做什么。实现接口的类必须定义如何完成行为。将接口视为创建蓝图(类)的严格指南。

> 一个简单的比喻可以是:一所房子需要墙壁和屋顶，但它们是如何建造的(数量、大小、颜色等)取决于建筑师。

接口**中的每个成员必须**在派生类中实现。

```
public interface IShape {
  void Draw();
  double Area();
}

class Circle : IShape {
  public double Radius { get; set; }

  public Circle(double radius) {
    Radius = radius;
  }

  public void Draw() {
    // draw a circle!
  }
  public double Area() {
    return Math.PI * Math.Pow(Radius, 2);
  }
}

public static void Main() {
  IShape c = new Circle(5);
  Console.WriteLine(c.Area());
  // 78.5398163397448
} 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/PK3EAW)

一个类可以从一个基类继承，但是它可以从多个接口实现。所以通过使用接口，你可以在一个类中包含来自多个来源的行为。

也许我们想做一个从`IShape`和`IAnimal`接口实现的对象:`class AnimalShape : IShape, IAnimal { }`

* * *

## 迅速升级！

总而言之，我们已经讨论了如何通过继承来创建基于其他类的类&理解多态和抽象类的基础知识，包括对接口的简短介绍。

主要要点:

*   把基础/派生关系想象成一个**”是一个**子句。
*   我们使用冒号:来表示一个类是从基类继承的。
*   多态性是指当类成员在派生类中实现时，可以用不同的方式定义它们。
*   抽象类和接口可以用来提供一个更严格的指导方针，指导如何构建实现它们的类。

我承认所有这些信息可能很难理解，但是希望你能想象这些复杂的类如何给程序更多的结构和可维护性。

在本系列的下一篇文章中，我们将了解作为一种多态性的方法重载。