# 在 C#中重载一元运算符

> 原文：<https://dev.to/jefrypozo/overloading-unary-operators-in-c-2o30>

这是关于 C#重载操作符系列文章的第二篇。

**往期词条**:
-[c#中运算符重载介绍](https://dev.to/jefrypozo/overloading-operators-in-c-350e)

这次我们将看到如何重载一元运算符和一些用例。请记住，在 C#中，一元运算符是:

*   加法(+)
*   减法(-)
*   逻辑否定(！)
*   递增和递减(++和-)
*   按位补码(~)
*   真假

### 运算符重载的基础知识

C#语言对于操作符的重载有一个简单的结构，基本上你在一个类型上定义一个静态方法，它的返回类型和参数就是类型本身。例如:

```
 public static <TypeName> operator <OperatorSymbol>(<Type>Name> typeName) 
```

Enter fullscreen mode Exit fullscreen mode

其中 *TypeName* 是封闭类型， *OperatorSymbol* 是重载运算符(+、-等等)。当我们谈到具体的例子时，你会更好地理解它。

为了给出具体的例子和更容易理解，我将使用一个温度类，我们将在其上重载操作符并应用我们的自定义逻辑。

```
namespace UnaryOperators
{
    public enum TemperatureType
    {
        Celsius = 1,
        Farentheit,
        Kelvin
    }

    public class Temperature
    {
        public double Value { get; set; }
        public TemperatureType Type { get; set; }

        public Temperature(double value = 0, TemperatureType type = TemperatureType.Celsius)
        {
            Value = value;
            Type = type;
        }
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们将看到如何在温度对象上使用操作符，但是修改它的底层值属性。

### 加法(+)运算符

默认情况下，加法运算符只返回一元形式的数值类型的当前值。我们将改变这个行为，如果温度小于 0，它将返回温度的绝对值，否则返回当前温度值。

```
public static Temperature operator + (Temperature tempA)
{
   if (tempA.Value < 0) tempA.Value = System.Math.Abs(tempA.Value);
   return tempA;
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，该方法接收一个温度对象并返回一个温度对象，这应该是规则，因为您将对温度对象应用操作符。当我们谈到二元操作符时，我们将看到如何在我们的自定义类型和任何其他类型之间应用操作数。

### 减法(-)运算符

默认情况下，减法运算符返回数值类型的负值。我们将覆盖这个行为，这样我们就可以通过将操作符应用到温度对象来修改底层值:

```
public static Temperature operator - (Temperature temp)
{
    temp.Value = - temp.Value;
    return temp;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 递增(++)和递减(-)运算符

这些非常简单，我们将修改它们的行为，使它们将温度增加或减少 10 个单位。

```
public static Temperature operator ++(Temperature temp)
{
    temp.Value += 10;
    return temp;
}

public static Temperature operator --(Temperature temp)
{
    temp.Value -= 10;
    return temp;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 真假运算符

我看不出有必要重载这些操作符，因为您可以使用布尔标志来代替。但是这些操作符的一个特殊且非常具体的用途是当您需要重载 OR (|)和 AND (&)操作符时，因为您需要实现 True 和 False 来使它们工作。此外，真和假是成对的，所以如果你让一个过载，你就必须让另一个过载。

```
public static bool operator true (Temperature temp)
{
    if (temp.Type == TemperatureType.Celsius) return temp.Value < 40;
    if (temp.Type == TemperatureType.Farentheit) return temp.Value < 104;
    return temp.Value < 313.5;
}

public static bool operator false (Temperature temp)
{
    if (temp.Type == TemperatureType.Celsius) return temp.Value >= 40;
    if (temp.Type == TemperatureType.Farentheit) return temp.Value >= 104;
    return temp.Value >= 313.5;
} 
```

Enter fullscreen mode Exit fullscreen mode

比如你想检查体温是否过高，是否会导致脱水。正如您在代码中看到的，我将 40 摄氏度的温度设置为阈值，所以实际上您可以这样使用它:

```
var temperature = new Temperature(41, TemperatureType.Celsius);
if (temperature)
    Console.WriteLine($"Outside temp is {temperature.Value}. Looks like a sunny day");            
else
    Console.WriteLine($"Outside temp is {temperature.Value}. It's dangerous to be outside with this heat");

// Prints Outside temp is 41\. It's dangerous to be outside with this heat
Console.ReadLine(); 
```

Enter fullscreen mode Exit fullscreen mode

### 逻辑否定(！)和按位(~)运算符

我找不到这些操作符的具体用例，但是我将解释按位操作符，因为逻辑否定是非常基本的。

位运算符通过将组成数字的零和一更改为相反的值来处理数字类型。让我们看一个例子:
如果你有十进制的 78，在二进制中它将是 **1001110** 。
当应用按位运算时，所有的 1 都被转换成 0，反之亦然；因此，我们的二进制数将是 **0110001** ，对应于十进制数 49。

## 结论

这篇文章介绍了 C#中运算符重载的基础知识，以及如何使用它们来改变自定义类型上的一些操作的行为。在下一篇文章中，我将讨论二元运算符，这是 C#语言中更有趣也更有用的一个方面。