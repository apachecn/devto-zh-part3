# C# OOP:方法重载

> 原文：<https://dev.to/aromig/c-oop-primer-method-overloading-1d0f>

跳转到:

*   [什么是超载？](#what-is-overloading)
*   [方法重载](#method-overloading)
    *   [类型提升](#type-promotion)
    *   [可选参数](#optional-parameters)
    *   [命名参数](#named-arguments)
*   [重载和继承](#overloading-and-inheritance)

## 什么是重载？

重载通常指同一函数调用有多个定义的能力。我们可以使用重载来执行相同的任务，但是参数稍有不同，通常是不同的数据类型。以这种方式实现方法可以消除不必要的复杂性，并使代码更易于阅读。

## 方法重载

方法重载实际上是多态的一种形式。在 C#中，方法可以有不同的*方法签名*，这意味着方法有多个名称相同但可以接受不同参数的定义。不仅有不同的数据类型，还有允许的参数数量及其顺序。

*   重载方法的区别在于它接受的参数的数量和类型。举几个例子:
    *   `FunMethod(int param1)` + `FunMethod(int param1, int param2)`
    *   `FunMethod(int param1)` + `FunMethod(string param1)`
*   不能用完全相同的名称、顺序和类型多次定义一个方法。每个重载需要在某些方面有所不同。
*   区分重载方法时不考虑返回类型。也就是说，两个重载的方法不能有相同的签名，而是不同的返回类型。方法*可以有不同的返回值，但最终重要的是参数列表。*

简单的例子:

```
static int Multiply(int a, int b)
{
  int product = a * b;
  return product;
}

static int Multiply(int a, int b, int c)
{
  int product = a * b * c;
  return product;
}

static double Multiply(double a, double b, double c, double d)
{
  double product = a * b * c * d;
  return product;
}

public static void Main()
{
  int product_one = Multiply(4, 5);
  int product_two = Multiply(4, 5, 6);
  double product_three = Multiply(10, 3.14, 5.25, 4);

  Console.WriteLine("One Method, Different Uses\n");
  Console.WriteLine("Multiplying 2 integers : {0}", product_one);
  Console.WriteLine("Multiplying 3 integers : {0}", product_two);
  Console.WriteLine("Multiplying 4 doubles  : {0}", product_three);
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
One Method, Different Uses

Multiplying 2 integers : 20
Multiplying 3 integers : 120
Multiplying 4 doubles  : 659.4 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/FdgqdP)

### 类型晋升

所以我们的简单例子处理整数和双精度数，但是如果我们想传递一个不同的数据类型作为参数，比如说一个浮点数呢？

我们可以简单地为它添加一个过载。

```
static float Multiply(float a, float b)
{
  float product = a * b;
  return product;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果...我们没有吗？从技术上讲，我们可以做到这一点。当一个*较小的*数据类型可以被*升级*为一个*较大的*数据类型时，类型升级就发生了。例如:短整型可以提升为整型，整型提升为长整型，浮点型提升为双精度型。

```
static int Multiply(int a, int b)
{
  int product = a * b;
  Console.WriteLine("Called the \"int\" method");
  return product;
}

static double Multiply(double a, double b)
{
  double product = a * b;
  Console.WriteLine("Called the \"double\" method");
  return product;
}

public static void Main()
{   
  Console.WriteLine("Multiply with integers: {0}", Multiply(3, 9));
  Console.WriteLine("Multiply with doubles: {0}", Multiply(3.14, 5));
  Console.WriteLine("Multiply with floats: {0}", Multiply(3.14f, 5));
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Called the "int" method
Multiply with integers: 27
Called the "double" method
Multiply with doubles: 15.7
Called the "double" method
Multiply with floats: 15.7000005245209 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/u3D0oh)

即使我们的代码只有 int 和 double 类型的重载，带有 float 的代码行(3.14 f-f 表示一个 float 值)运行得很好。它通过利用“双”重载来实现这一点。就连 int，5，也得到了提升。仔细想想,“与 doubles 相乘”行中方法调用的第二个参数在技术上是一个整数，而不是 double。即使在第一个例子中也是类型提升！

类型提升并不总是发生。如果调用方法时使用的确切数据类型存在重载，程序将首先选择该类型。

> 资源:[隐式数值转换表@微软](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/implicit-numeric-conversions-table)

### 可选参数

方法重载的一个非常有用的好处是有可选的参数。我们通过用变量名声明一个*默认值*来创建一个可选参数。

```
static int Multiply(int a, int b = 0)
{
  int product = a * b;
Console.WriteLine("Called Multiply(int, int)");
return product;
}

static double Multiply(double a, double b = 5.5)
{
  double product = a * b;
Console.WriteLine("Called Multiply(double, double)");
  return product;
}

public static void Main()
{
  Console.WriteLine("Passing one int argument   : {0}", Multiply(4));
  Console.WriteLine("Passing two int arguments  : {0}", Multiply(4, 5));
Console.WriteLine("Passing one double argument: {0}", Multiply(4.0));
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Called Multiply(int, int)
Passing one int argument   : 0
Called Multiply(int, int)
Passing two int arguments  : 20
Called Multiply(double, double)
Passing one double argument: 22 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/nWALvh)

在挑选重载方法的规则上没有什么不同，只是成功调用它不需要第二个参数。但是当我们提供它时，缺省值被忽略。

### 命名论据

另一个可以用来处理重载方法的特性是*命名参数*。调用方法是相同的，只是所提供的参数以参数列表中的变量名为前缀。

```
static int Square(int a)
{
  int square = a * a;
Console.WriteLine("Called Square(int a)");
return square;
}

static double Square(double b)
{
  double square = b * b;
Console.WriteLine("Called Square(double b)");
  return square;
}

public static void Main()
{
  Console.Write("Square of 9 is {0}", Square(b: 9));
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Called Square(double b)
Square of 9 is 81 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/zgUoiy)

尽管一个整数作为参数传递给了 Square 方法，但是 double 重载还是被调用了，因为我们用`b:`作为命名参数来调用它。如果我们使用了`a:`，就会使用整数重载。

## 重载和继承

将方法重载和类继承一起使用有时会令人困惑。通过考虑方法调用的“目标”的类并首先检查属于它的方法来处理重载。如果在那个类中没有合适的方法，那么考虑它的父类。然后是该类的父类，依此类推。它不会看目标类的孩子。

```
class Parent
{
  public void Method(int x)
  {
    Console.WriteLine("Parent's version of Method");
  }   
}

class Child : Parent
{
  public void Method(double y)
  {
    Console.WriteLine("Child's version of Method");
  }
}

public class Program
{
  public static void Main()
  {
    Child c = new Child();
    c.Method(10);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Child's version of Method 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/h8LPvx)

在该方法的目标之上是属于`Child`类的`c`。如果只有方法(由于类型提升，这也是一个适用的方法)，就没有必要查看`Parent`类的方法。

C#这样做是为了帮助避免面向对象编程的一个基本问题，叫做[脆弱基类问题](https://en.wikipedia.org/wiki/Fragile_base_class)。这个想法是，基类可以被认为是脆弱的，因为对它们看似很小的改变可能会在派生类中导致意想不到的，有时甚至是严重的问题。语言已经做了很多事情来帮助防止这种类型的问题，但是在设计类的时候需要注意这一点。这包括跨类重载方法。

更多奇怪的事情发生了。

因此，让我们将基类方法改为虚拟方法，这样我们就可以添加一个覆盖方法`Method(int x)`。现在当我们调用`c.Method(10);`时，它仍然像以前一样传递整数类型。所以它会调用新的覆盖方法，对吗？

```
class Parent
{
  public virtual void Method(int x)
  {
    Console.WriteLine("Parent's version of Method");
  }   
}

class Child : Parent
{
  public override void Method(int x)
  {
    Console.WriteLine("Child's override version of Method(int)");
  }
  public void Method(double y)
  {
    Console.WriteLine("Child's version of Method");
  }
}

public class Program
{
  public static void Main()
  {
    Child c = new Child();
    c.Method(10);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Child's version of Method 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/Qca8Gu)

不，不是的！在 C#中，如果你重写一个基类方法，它不算是对重载方法的声明。尝试从方法声明中删除关键字`override`。现在它做了我们认为它会做的事。

困惑吗？避免跨继承线重载方法并将其保持在目标类内可能是一个好的选择。

> 有趣的阅读: [Eric Lippert 关于脆弱基类的文章](https://blogs.msdn.microsoft.com/ericlippert/tag/brittle-base-classes/)

* * *

## 哇，太棒了。

希望您可以看到重载方法是如何有用的，即使有所有的规则和潜在的古怪。我的建议是让重载方法的结构和使用尽可能简单。

好的重载的一个可能的想法是类构造函数，要么通过重载构造函数本身，要么通过一个单独的静态方法在后台创建新的实例(将构造保持为定义良好的有限进程)。

另一个是各种接受不同类型参数的辅助函数。例如，我编写了一个错误日志记录方法，它要么接受一个`Exception`对象，要么接受一个纯文本`string`，以防异常不可用。

或者有时会变得有点复杂。不要害怕后退一步。你可能会发现重载并不是最好的选择，只是创建另一个方法作为一个独立的方法。

对于技术方面的很多事情，我倾向于选择最简单的解决方案。不过，在下一篇文章中，我们将讨论更多关于重载的内容，但这次是用*操作符*。