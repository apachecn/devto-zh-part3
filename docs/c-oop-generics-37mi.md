# C# OOP:泛型

> 原文：<https://dev.to/aromig/c-oop-generics-37mi>

跳转到:

*   什么是仿制药？
    *   [定义类属](#defining-a-generic)
*   [通用方法](#generic-method)
*   [通用类](#generic-class)
*   [约束条件](#constraints)
*   [收藏](#collections)
*   [好处](#benefits)

## 什么是通用？

OOP 中的泛型允许我们定义一个可以用于任何数据类型的类或方法的规范。当我们设计一个泛型时，方法参数或类的数据类型是未知的——直到它被调用或实例化。

### 定义类属

泛型类或方法可以使用尖括号< >和一个标识符来定义。用于泛型的标准标识符是一个大写字母 T，即`<T>`，但是任何字母都可以使用——甚至是一个单词。如果使用不同于 *T* 的标识符是有意义的，请随意。这不是规则，只是大多数程序员遵循的准则。

```
public static void Method<T>(T param1) { } 
```

Enter fullscreen mode Exit fullscreen mode

如果需要的话，我们也可以使用多个泛型类型，通常第二个被表示为`<U>`。

```
public static void Method<T, U>(T param1, U param2) { } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 通用方法

请看下面这个通过引用交换两个整数或字符串值的[重载方法](https://dev.to/aromig/c-oop-primer-method-overloading-1d0f)的例子。

```
using System;

public class Maths {

  public static void Swap(ref int a, ref int b) {
    int temp = a;
    a = b;
    b = temp;
  }

  public static void Swap(ref string a, ref string b) {
    string temp = a;
    a = b;
    b = temp;
  }
}

public class Program {
  public static void Main() {
    int twenty = 20;
    int thirty = 30;
    string forty = "forty";
    string fifty = "fifty";

    Maths.Swap(ref twenty, ref thirty);
    Console.WriteLine("twenty = {0}", twenty);
    Console.WriteLine("thirty = {0}", thirty);

    Maths.Swap(ref forty, ref fifty);
    Console.WriteLine("forty = {0}", forty);
    Console.WriteLine("fifty = {0}", fifty);

  }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
twenty = 30
thirty = 20
forty = fifty
fifty = forty 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/vWGwm8)

当然，很管用。但是如果我们为 floats 添加另一个重载会怎么样呢？另一个是布尔型的。另一个用于类对象。这个重载的方法越来越大。现在，如果我们需要对所有的重载做一个根本性的改变呢？用一种方法来管理它们不是更好更简单吗？

```
using System;

public class Maths {

  public static void Swap<T>(ref T a, ref T b) {
    T temp = a;
    a = b;
    b = temp;
  }
}

public class Program {
  public static void Main() {
    int twenty = 20;
    int thirty = 30;
    string forty = "forty";
    string fifty = "fifty";

    Maths.Swap<int>(ref twenty, ref thirty);
    Console.WriteLine("twenty = {0}", twenty);
    Console.WriteLine("thirty = {0}", thirty);

    Maths.Swap<string>(ref forty, ref fifty);
    Console.WriteLine("forty = {0}", forty);
    Console.WriteLine("fifty = {0}", fifty);

  }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
twenty = 30
thirty = 20
forty = fifty
fifty = forty 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/x0gSYy)

我们可以将它精简为一个，而不是两个单独的重载方法。主要的区别是方法名后面的`<T>`，以及用`T`替换参数和临时变量的数据类型。

当我们从主程序调用该方法时，我们可以为 *T* 指定数据类型。在第一部分，我们传递整数，所以这个方法被称为`Swap<int>(...)`，而不仅仅是`Swap(...)`。在第二部分，我们做了同样的事情，但是因为我们传递的是字符串，这个方法被称为`Swap<string>(...)`。

尽管如此，我们实际上可以在调用泛型方法时省略指定*类型，因为编译器会从传递的参数中推断出数据类型。* 

```
// This works too!
Maths.Swap(ref twenty, ref thirty);
Maths.Swap(ref forty, ref fifty); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 类属

按照同样的思路，类可以是泛型的。泛型类的一个常见用途是定义项的集合，无论数据类型如何，添加和移除都以相同的方式执行。

一个常见的泛型类是一个称为堆栈的集合，其中的项被**推入**(添加到堆栈顶部)和**弹出**(从堆栈顶部移除)。这被称为后进先出，因为在*弹出*操作中移除的项目是最近添加的最后一个项目。

[![LIFO Example, credit Wikipedia](img/67544c2d2873df51602ac89eb8209923.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dwbnCXJJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7smeoqdm9u4ok4ncolwx.png)T3】

<center><small>*LIFO Example, credit Wikipedia*</small></center>

虽然下面的例子并不完全是 Stack <>类(如。Net 框架)，它应该显示我们正在谈论的关于泛型类的使用。

```
using System;

public class Stack<T> {
  readonly int max_size;
  int index = 0;
  public int size { get; set; }
  T[] items = null;

  public Stack() : this(100) { } // default max_size of 100

  public Stack(int size) {
    max_size = size;
    items = new T[max_size];
  }

  // Push - Adds item to the top of the stack
  public void Push(T item) {
    if (size >= max_size)
      throw new StackOverflowException();

    items[index++] = item; // adds item in next spot
    size++; // increments actual size
  }

  // Pop - returns item from top and then removes it from the stack
  public T Pop() {
    if (size <= 0)
      throw new InvalidOperationException("Stack is empty, cannot Pop");

    return items[--index]; // decrements by 1 and returns value at index
  }

  // Peek - returns the last item in the listt
  public T Peek() {
    return items[index - 1];
  }

  // Get - returns the value of the specified index
  public T Get(int idx) {
    return items[idx];
  }
}

public class Program
{
  public static void Main() {
    Stack<string> StarWars = new Stack<string>();
    StarWars.Push("The Phantom Menace");
    StarWars.Push("Attack of the Clones");
    StarWars.Push("Revenge of the Sith");
    StarWars.Push("Rogue One");
    StarWars.Push("Solo");
    StarWars.Push("A New Hope");
    StarWars.Push("The Empire Strikes Back");
    StarWars.Push("Return of the Jedi");
    StarWars.Push("The Force Awakens");
    StarWars.Push("The Last Jedi");
    StarWars.Push("The Rise of Skywalker");

    Console.WriteLine("Size of Stack: {0}", StarWars.size);
    Console.WriteLine(StarWars.Peek()); // The Rise of Skywalker
    Console.WriteLine(StarWars.Pop());  // The Rise of Skywalker
    Console.WriteLine(StarWars.Pop());  // The Last Jedi
    Console.WriteLine(StarWars.Peek()); // The Force Awakens
    Console.WriteLine(StarWars.Get(5)); // A New Hope
    Console.WriteLine("Size of Stack: {0}", StarWars.size);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Size of Stack: 11
The Rise of Skywalker
The Rise of Skywalker
The Last Jedi
The Force Awakens
A New Hope
Size of Stack: 9 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/cVqGfB)

还记得[坐标结构](https://dev.to/aromig/c-oop-data-types-structs-and-classes-453p#structs)吗？

```
struct Coords {
  public int x;
  public int y;

  // constructor
  public Coords(int p1, int p2) {
    x = p1;
    y = p2;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用泛型结构(是的，也是结构！)取而代之，它不再局限于整数。

```
using System;

struct Coords<T> {
  public T X;
  public T Y;
}

public class Program
{
  public static void Main()
  {
    Coords<int> intCoords;
    intCoords.X = 1;
    intCoords.Y = 2;
    Console.WriteLine("intCoords = {0}, {1}", intCoords.X, intCoords.Y);

    Coords<float> floatCoords;
    floatCoords.X = 1.23f;
    floatCoords.Y = 4.56f;
    Console.WriteLine("floatCoords = {0}, {1}", floatCoords.X, floatCoords.Y);

    Coords<string> stringCoords;
    stringCoords.X = "Over there";
    stringCoords.Y = "A bit closer";
    Console.WriteLine("stringCoords = {0}, {1}", stringCoords.X, stringCoords.Y);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
intCoords = 1, 2
floatCoords = 1.23, 4.56
stringCoords = Over there, A bit closer 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/J5Pzof)

* * *

## 约束

我们还可以限制泛型只接受某种类型，比如结构、类或特定的类/接口。

通过使用 *where* 关键字，后跟泛型类型变量(例如 T)、冒号和约束类型，在泛型上实现约束。

有 6 种类型的约束:

|  |  |  |
| --- | --- | --- |
| 其中 T:结构 | → | 类型参数必须是值类型 |
| 其中 T:类 | → | 类型参数必须是引用类型 |
| 其中 T : new() | → | 类型参数必须有一个公共的无参数构造函数 |
| 其中 T: | → | 类型参数必须从类继承 |
| 其中 T: | → | 类型参数必须从接口实现 |
| 其中 T : U | → | 有两种类型参数 T 和 u，T 必须从 u 继承。 |

也许上面的例子对于坐标结构来说没有意义，因为它的 X & Y 值是字符串。让我们给它添加一个约束。

```
struct Coords<T> where T : struct {
  public T X;
  public T Y;
} 
```

Enter fullscreen mode Exit fullscreen mode

整洁！但是为什么我们要用结构来约束呢？上表显示，使用 struct 约束会将类型参数限制为值类型。由于字符串是引用类型，这应该允许此结构不接受字符串。会的；但是，请接受单个字符。不幸的是，泛型不能被约束到单独的数据类型，但是这已经足够接近于展示一个约束了。

```
using System;

struct Coords<T> where T : struct {
  public T X;
  public T Y;
}

public class Program
{
  public static void Main()
  {
    Coords<int> intCoords;
    intCoords.X = 1;
    intCoords.Y = 2;
    Console.WriteLine("intCoords = {0}, {1}", intCoords.X, intCoords.Y);

    Coords<float> floatCoords;
    floatCoords.X = 1.23f;
    floatCoords.Y = 4.56f;
    Console.WriteLine("floatCoords = {0}, {1}", floatCoords.X, floatCoords.Y);

    Coords<string> stringCoords;
    stringCoords.X = "Over there";
    stringCoords.Y = "A bit closer";
    Console.WriteLine("stringCoords = {0}, {1}", stringCoords.X, stringCoords.Y);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/397uBQ)

这将导致实例化 *stringCoords* 的行出错。

```
Compilation error (line 22, col 12): The type 'string' must be a non-nullable value type in order to use it as parameter 'T' in the generic type or method 'Coords<T>' 
```

Enter fullscreen mode Exit fullscreen mode

## 收藏

如上所述，存在像 Stack <>这样的泛型类集合。另一个常见的是 List <>。

| 列表<> |  | 功能 |
| --- | --- | --- |
| 。添加() | → | 在列表末尾添加一个项目 |
| 。清除() | → | 从列表中移除所有项目 |
| 。包含() | → | 检查项目；返回一个布尔值 |
| 。数数 | → | 返回列表中的项目数 |
| 。插入() | → | 在指定索引处添加一项 |

下面是一个使用 List <>集合的小例子。

```
using System;
using System.Collections.Generic;

public class Program
{
  public static void Main()
  {
  List<string> colors = new List<string>();
  colors.Add("Red");
  colors.Add("Green");
  colors.Add("Blue");
  colors.Remove("Green");

  Console.WriteLine("# of Colors: {0}", colors.Count);
  foreach (var color in colors) {
    Console.WriteLine(color);
  }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
# of Colors: 2
Red
Blue 
```

Enter fullscreen mode Exit fullscreen mode

[→网络游戏](https://dotnetfiddle.net/bBK0Rr)

。Net 在 [*系统中还有其他几个内置的集合类。Collections.Generic* 命名空间](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic?view=netframework-4.8)，如队列、链表、字典等。这个名称空间中可用的集合类型相当多样，所以如果其中一个能够提供您需要的功能，就使用它们吧！然而，如果创建自己的通用集合有意义，那就继续吧。

* * *

## 好处

*   **可重用性:**一个单一的泛型类型定义可以通过相同的代码用于多种目的，无需任何修改。维护方法/类的一个版本比多个重载容易得多。
*   类型安全:泛型数据类型提供了更好的类型安全，尤其是在与集合一起使用时，因为我们需要指定传递给它们的对象的类型。类型检查是在编译时而不是运行时完成的，允许在发布前捕捉到错误。
*   **性能:**方法&利用泛型的类提供了更好的性能，因为它们减少了对装箱/取消装箱(从值类型到对象的转换，反之亦然)以及所用对象的类型转换的需求。泛型的数据类型版本的实际代码是按需完成的，而不是可能不使用的多类型版本。

* * *

## 牛逼的玩意儿！

泛型是 C#的一个强大特性，它允许我们创建更加通用的数据结构和方法。使用泛型可以提供很好的抽象性和可扩展性，允许代码重用、类型安全和性能提升。找到在哪里使用它们可能需要一些实践和预先考虑，因为这可能不是显而易见的。不过没问题！至少要记住重构机会的概念。*