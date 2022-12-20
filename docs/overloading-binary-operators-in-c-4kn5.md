# 在 C#中重载二元运算符

> 原文：<https://dev.to/jefrypozo/overloading-binary-operators-in-c-4kn5>

这是关于 C#中运算符系列的第三篇文章。这一次，我们将看到如何重载二元运算符以及它们的一些用例。

以前的条目:

*   [运算符重载简介](https://dev.to/jefrypozo/overloading-operators-in-c-350e)
*   [在 C#中重载一元运算符](https://dev.to/jefrypozo/overloading-unary-operators-in-c-2o30)

### 二元运算符

您可能知道，二元运算符适用于两个操作数。其中大多数是数学运算符，其他一些是逻辑运算符，以及相等/比较运算符。
C #中可用于重载的二元运算符有:

*   加法(+)
*   减数(-)
*   产品(*)
*   除法(/)
*   逻辑与(&)和逻辑或(|)
*   逻辑异或(^)
*   左移(<>)
*   比较运算符
    *   相等(==)
    *   不等式(！=)
    *   小于(
    *   大于(>)
    *   小于或等于(< =)
    *   大于或等于(> =)

### 加法

继续讨论温度数据类，我们将覆盖二进制加法运算符，这样它就可以对不同范围内的两个温度求和。
为了保持代码的一致性，我只考虑从摄氏温度到华氏温度的转换，反之亦然:

```
public static Temperature operator +(Temperature tempA, Temperature tempB)
{
    if (tempA.Type == tempB.Type) tempA.Value += tempB.Value;
    if (tempA.Type == TemperatureType.Celsius && tempB.Type == TemperatureType.Farentheit)
    {
        double tempInCelsius = (tempB.Value - 32) * (5d / 9d);
        tempA.Value += tempInCelsius;
    }
    else if (tempA.Type == TemperatureType.Farentheit && tempB.Type == TemperatureType.Celsius)
    {
        double tempInFarenheit = tempB.Value  * (9d / 5d) + 32;
        tempA.Value += tempInFarenheit;
    }
    return tempA;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以这样使用它

```
 var celsius = new Temperature(20, TemperatureType.Celsius);
 var farenheit = new Temperature(68, TemperatureType.Farentheit);
 celsius = celsius + farenheit;

 // Prints "The current temperature in Celsius is: 40"
 Console.WriteLine("The current temperature in Celsius is: " + celsius.Value);
 Console.ReadLine(); 
```

Enter fullscreen mode Exit fullscreen mode

### 减法

减法运算符的代码几乎与加法运算符的代码相同:

```
public static Temperature operator +(Temperature tempA, Temperature tempB)
{
    if (tempA.Type == tempB.Type) tempA.Value -= tempB.Value;
    if (tempA.Type == TemperatureType.Celsius && tempB.Type == TemperatureType.Farentheit)
    {
        double tempInCelsius = (tempB.Value - 32) * (5d / 9d);
        tempA.Value -= tempInCelsius;
    }
    else if (tempA.Type == TemperatureType.Farentheit && tempB.Type == TemperatureType.Celsius)
    {
        double tempInFarenheit = tempB.Value  * (9d / 5d) + 32;
        tempA.Value -= tempInFarenheit;
    }
    return tempA;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于乘积和除法运算符也是如此。

### 逻辑运算符

您可以覆盖逻辑 AND 和 OR 运算符，以便修改在条件语句中使用的&&和||运算符的行为。我们将沿着 XOR 操作符简要地看一下它们的定义和一个使用它们的简单例子。对于 AND 运算符，如果它们的类型相同，我们将返回 true。
对于 or 运算符，如果任一值高于阈值，我们将返回 true。
对于 XOR 运算符，如果它们的类型不同，我们将返回 true。

```
public static bool operator &(Temperature tempA, Temperature tempB)
{
    if (tempA.Type == tempB.Type) return true;
    else return false;
}

public static bool operator |(Temperature tempA, Temperature tempB)
{
    var tempAInCelsius = tempA.Type == TemperatureType.Celsius ? tempA.Value : (tempA.Value - 32) * (5d / 9d);
    var tempBInCelsius = tempB.Type == TemperatureType.Celsius ? tempB.Value : (tempB.Value - 32) * (5d / 9d);

    if (tempAInCelsius >= 37 || tempBInCelsius >= 37) return false;
    else return true;
}

public static bool operator ^(Temperature tempA, Temperature tempB)
{
    if (tempA.Type != tempB.Type) return true;
    else return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们就可以像下面这样使用重载操作符

```
var celsius = new Temperature(20, TemperatureType.Celsius);
var farenheit = new Temperature(68, TemperatureType.Farentheit);

if (celsius & farenheit)
{
    Console.WriteLine("The temperatures are equivalent");
}
if (celsius | farenheit)
{
    Console.WriteLine("The temperatures are cool");
}
if (celsius ^ farenheit)
{
    Console.WriteLine("The temperatures are different");
}

//Prints "The temperature are coool" and "The temperatures are different"
Console.ReadLine(); 
```

Enter fullscreen mode Exit fullscreen mode

&&和||运算符是不可重载的，但是您可以通过重载&或|以及 true 和 false 运算符来实现这一点。

### 比较运算符

对于比较运算符，您需要成对重载它们。这意味着，如果重载等号(==)比较运算符，还需要重载不等式(！=)比较运算符，否则会出现编译器错误。

这里是摄氏和华氏温度的代码:

```
public static bool operator == (Temperature tempA, Temperature tempB){
    if(tempA.Type == tempB.Type) return tempA.Value == tempB.Value;
    var tempValueA = tempA.Type == TemperatureType.Celsius ? tempA.Value : (tempA.Value - 32) * (5d / 9d);
    var tempValueB = tempB.Type == TemperatureType.Celsius ? tempB.Value : (tempA.Value - 32) * (5d / 9d);
    return tempValueA == tempValueB;

}

public static bool operator　!= (Temperature tempA, Temperature tempB){

    if(tempA.Type == tempB.Type) return tempA.Value != tempB.Value;
    var tempValueA = tempA.Type == TemperatureType.Celsius ? tempA.Value : (tempA.Value - 32) * (5d / 9d);
    var tempValueB = tempB.Type == TemperatureType.Celsius ? tempB.Value : (tempB.Value - 32) * (5d / 9d);
    return tempValueA != tempValueB;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用比较运算符直接计算温度值，而无需自己进行转换:

```
var tempA = new Temperature(25.8, TemperatureType.Celsius);
var tempB = new Temperature(78.44, TemperatureType.Farentheit);
var tempAreEquals = tempA == tempB;

// Prints true
Console.WriteLine("Are temperature equals? "+ tempAreEquals);
Console.ReadLine(); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在这个条目中，我们看到了如何将二元运算符应用于我们的自定义数据类型，尽管它们的用例很少，但它们提供了一种简单而简洁的方式来为我们的对象提供额外的行为和比较功能。

请继续关注本系列的下一篇也是最后一篇文章，在这篇文章中，我将讨论用于类型之间转换的显式和隐式操作符，以及用于类似数组的索引访问的索引操作符。