# C# OOP:运算符重载

> 原文：<https://dev.to/aromig/c-oop-operator-overloading-4j5d>

跳转到:

*   [什么是运算符重载？](#what-is-operator-overloading)
    *   [复习:什么是运算符？](#refresher-what-are-operators)
*   [语法](#syntax)
    *   [关于一元运算符](#on-a-unary-operator)
    *   [上一个二元运算符](#on-a-binary-operator)
    *   [上一个关系运算符](#on-a-relational-operator)

## 什么是运算符重载？

重载一个方法(相同的方法，不同的输入)的概念也可以应用于操作符。操作符重载使我们能够使用操作符不仅为基本数据类型提供服务，还为像类这样的自定义类型提供服务。这给了我们一个实现这些操作的更自然的抽象。

[![Operator Cloud](img/8541e9a87d784376d6e29eeb20e0ab1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jeAUQViZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/30gl33y4y2bh0qk7ro4x.gif)

### 复习:什么是运算符？

从根本上说，运算符是指定在表达式中执行哪种类型的运算(如加法和减法)的符号。但是数学并不是编程中唯一可用的操作！

可以重载的运算符:

*   一元运算符(一个操作数，例如+x)
    *   +x -返回 x 的值
    *   -x -否定 x
    *   ！x -逻辑否定 x(转换为 NOT x)
    *   ~x 位补码(通过交换 1 和 0 来改变值中的所有位)
    *   ++带 x 前缀的增量(更新值后返回值)
    *   x++ -后缀增量(在更新值之前返回值)
    *   - x 前缀减量(更新值后返回值)
    *   x - -后缀减量(在更新值之前返回值)
*   二元运算符(两个操作数，例如 x + y)
    *   + -加法
    *   ——减法
    *   * -乘法
    *   / -除法
    *   % -模/余数
*   关系(比较)运算符(两个操作数，返回布尔值，例如 x == y)
    *   == -等于
    *   ！= -不等于
    *   >，> = -大于，大于或等于

> 资源: [C#运算符@微软](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/operators)

## 语法

重载运算符方法遵循以下规则:

*   它应该是它所返回的类的成员。
*   方法必须是*静态的*。
*   该方法必须有关键字`operator`，后跟将被覆盖的操作符。
*   参数将是操作数。

所以语法看起来像下面这样，用数据类型(例如，类类型，bool)替换[返回类型]，[？]和[parameters]以及适当的参数，就像在常规方法中一样。

```
// Operator Overload syntax:
// public static [return type] operator [?]([parameters])

// Example:
public static Foo operator +(Foo foo1, Foo foo2)
{
  // ... overload stuff where I add foo1 to foo2
} 
```

Enter fullscreen mode Exit fullscreen mode

### 上一元运算符

一元运算符只有一个操作数，因此它的重载语法如下所示。

```
Using System;

class Calc
{
  public int num1 { get; set; }
  public int num2 { get; set; }
  public Calc(int n1, int n2)
  {
    num1 = n1;
    num2 = n2;
  }

  // Overload to reverse each number's sign
  // Using the unary minus operator
  public static Calc operator -(Calc calc)
  {
    calc.num1 = -calc.num1;
    calc.num2 = -calc.num2;
    return calc;
  }
}

class Program
{
  static void Main()
  {
    Calc calc = new Calc(43, -21);
    calc = -calc;
    Console.WriteLine("Num1 = {0}", calc.num1);
    Console.WriteLine("Num2 = {0}", calc.num2);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

<small>输出:</small>

```
Num1 = -43
Num2 = 21 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/kGan2g)

我们能够在 Calc 对象中同时反转两个数字的符号。很疯狂吧。

### 上一个二元运算符

让我们试试另一个例子。假设我们有这个盒子类。

```
public class Box
{
  public int Height { get; set; }
  public int Width { get; set; }

  public Box(int h, int w)
  {
    Height = h;
    Width = w;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们希望添加盒子对象来制作一个更大的盒子。我们认为它应该用一个简单的等式工作，比如`Box b3 = b1 + b2;`，导致 b3 的尺寸是另外两个盒子的高度和宽度的总和。为此，我们将重载二进制加号运算符。

```
using System;

public class Box
{
  public int Height { get; set; }
  public int Width { get; set; }

  public Box(int h, int w)
  {
    Height = h;
    Width = w;
  }

  public static Box operator +(Box box1, Box box2)
  {
    int h = box1.Height + box2.Height;
    int w = box1.Width + box2.Width;
    Box result = new Box(h, w);
    return result;
  }
}

public class Program
{
  public static void Main()
  {
    Box b1 = new Box(14, 3);
    Box b2 = new Box(5, 7);
    Box b3 = b1 + b2;
    Box b4 = b1 + b2 + b3;

    Console.WriteLine("Box b1: Height: {0}, Width: {1}", b1.Height, b1.Width);
    Console.WriteLine("Box b2: Height: {0}, Width: {1}", b2.Height, b2.Width);
    Console.WriteLine("Box b3: Height: {0}, Width: {1}", b3.Height, b3.Width);
    Console.WriteLine("Box b4: Height: {0}, Width: {1}", b4.Height, b4.Width);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

<small>输出:</small>

```
Box b1: Height: 14, Width: 3
Box b2: Height: 5, Width: 7
Box b3: Height: 19, Width: 10
Box b4: Height: 38, Width: 20 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/VlHLu5)

重载运算符方法分别从两个 Box 操作数中获取高度和宽度值，并将它们相加。然后，它用这些新值创建一个新的 Box 对象。就像我们想象的那样工作——可能更多！看上面的代码，一个新的盒子是由三个盒子加在一起而成的。运算符重载在链接到多个操作数时有效！

如果我们想通过增加一个整数值的高度和宽度来制作一个新的盒子呢？喜欢`Box b4 = b1 + 10;`。绝对的！没有规则规定两个操作数都必须是类的类型。

```
public static Box operator +(Box box1, int num)
{
  int h = box1.Height + num;
  int w = box1.Width + num;
  Box result = new Box(h, w);
  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

除了第二个操作数是一个整数之外，这与我们已经完成的重载非常相似。

如果整数是第一个操作数呢？喜欢`Box b5 = 10 + b1;`。没问题。我们只需要创建一个操作符重载的重载方法。这比最后一句听起来要简单。

```
public static Box operator +(int num, Box box1)
{
  return box1 + num;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于这个重载，我们可以通过调用前面的运算符重载来简化它，我们已经按照它接受的顺序对操作数进行了重载。查看下面的完整代码:

```
using System;

public class Box
{
  public int Height { get; set; }
  public int Width { get; set; }

  public Box(int h, int w)
  {
    Height = h;
    Width = w;
  }

  public static Box operator +(Box box1, Box box2)
  {
    int h = box1.Height + box2.Height;
    int w = box1.Width + box2.Width;
    Box result = new Box(h, w);
    return result;
  }

  public static Box operator +(Box box1, int num)
  {
    int h = box1.Height + num;
    int w = box1.Width + num;
    Box result = new Box(h, w);
    return result;
  }
  public static Box operator +(int num, Box box1)
  {
    return box1 + num;
  }
}

public class Program
{
  public static void Main()
  {
    Box b1 = new Box(14, 3);
    Box b2 = new Box(5, 7);
    Box b3 = b1 + b2;
    Box b4 = b1 + 10;
    Box b5 = 10 + b1;

    Console.WriteLine("Box b1: Height: {0}, Width: {1}", b1.Height, b1.Width);
    Console.WriteLine("Box b2: Height: {0}, Width: {1}", b2.Height, b2.Width);
    Console.WriteLine("Box b3: Height: {0}, Width: {1}", b3.Height, b3.Width);
    Console.WriteLine("Box b4: Height: {0}, Width: {1}", b4.Height, b4.Width);
    Console.WriteLine("Box b5: Height: {0}, Width: {1}", b5.Height, b5.Width);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

<small>输出:</small>

```
Box b1: Height: 14, Width: 3
Box b2: Height: 5, Width: 7
Box b3: Height: 19, Width: 10
Box b4: Height: 24, Width: 13
Box b5: Height: 24, Width: 13 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/RYq0i1)

### 上一个关系运算符

像< and >这样的关系操作符可以设置成类似于二元操作符(有两个参数)。重要的是要记住，它们需要成对重载。例如，如果我们也让< operator, we need to overload the >过载。对于<= & > =和== &，也是如此！=运算符对。

让我们看看如何用我们的 Box 类实现它。

```
public class Box
{
  public int Height { get; set; }
  public int Width { get; set; }

  public Box(int h, int w)
  {
    Height = h;
    Width = w;
  }

  public static bool operator <(Box box1, Box box2)
  {
    int area1 = box1.Height * box1.Width;
    int area2 = box2.Height * box2.Width;
    return area1 < area2;
  }
  public static bool operator >(Box box1, Box box2)
  {
    int area1 = box1.Height * box1.Width;
    int area2 = box2.Height * box2.Width;
    return area1 > area2;
  }
}

public class Program
{
  public static void Main()
  {
    Box box1 = new Box(15, 32);
    Box box2 = new Box(44, 12);

    Console.WriteLine("Is box1 < box2 ? {0}", box1 < box2);
    Console.WriteLine("Is box1 > box2 ? {0}", box1 > box2);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

<small>输出:</small>

```
Is box1 < box2 ? True
Is box1 > box2 ? False 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/DXdx4P)

对于这个例子，我们将使用盒子的面积作为比较的基础。注意，返回类型是 *bool* ，因为关系操作返回 true 或 false 值。

## 整齐的货色吧？

从这些例子中可以看出，运算符重载是一种直观而自然的抽象。尽管如此，还是有可能滥用这一点，无意中创建复杂的实现。一个好的经验法则是，当操作符重载给类类型带来简单性并给应用程序带来整体可维护性时，使用操作符重载。

在本系列的下一篇文章中，我们将学习更多关于方法和类的知识，特别是泛型。