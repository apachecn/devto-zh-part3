# C#中重载运算符简介

> 原文：<https://dev.to/jefrypozo/overloading-operators-in-c-350e>

这是关于如何修改 C#中可用的一些操作符的行为的系列文章中的第一篇，这些操作符包括加法(+)、减法(-)。

## **重载运算符**

您可以修改大多数一元和二元运算符，其他运算符可以通过其他方式进行“修改”，有些运算符根本无法修改。

### 一元运算符

一元运算符是那些只适用于一个操作数的运算符。您可以重载这些一元运算符:

*   加法(+)
*   减法(-)
*   逻辑否定(！)
*   递增和递减(++和-)
*   按位补码(~)
*   真假

### 二元运算符

二元运算符是应用于两个操作数的运算符。您可以重载这些二元运算符:

*   加法(+)
*   减法(-)
*   产品(*)
*   除法(/)
*   余数(%)
*   逻辑与(&)
*   逻辑或(|)
*   逻辑异或(^)
*   左右移动(<< and >>)

### 比较运算符

比较运算符是对表达式求值并根据是否满足条件返回 true 或 false 的运算符。您可以重载这些比较运算符:

*   相等(==)
*   不等式(！=)
*   小于(
*   大于(>)
*   小于或等于(< =)
*   大于或等于(> =)

注意，如果你重载了这些操作符中的一个，你也必须重载另一个操作符。例如，如果重载相等运算符(==)，还需要重载不等运算符(！=).

## **非显式重载运算符**

有些运算符不能显式重载，但您可以使用另一种方法来修改使用该运算符时的行为。不能显式重载的运算符有:

*   条件逻辑操作符(&&和||)，但是它们使用&和|操作符，这些操作符可以重载
*   数组索引操作符[]，但是你可以在一个对象上定义[索引器](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/index)
*   强制转换操作符(T)x，但是你可以重载[隐式](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/implicit)和[显式](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/explicit)操作符
*   复合赋值运算符(+=，-=等等)，但它们是使用一元运算符计算的，您可以重载这些运算符

## **不可重载的运算符**

这些是编译器使用的操作符，它们的行为不能以任何方式修改。它们是:

*   赋值运算符(=)
*   成员访问运算符(。)
*   条件(或三元)运算符(？:)
*   零合并运算符(？？)
*   [解引用操作符](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/dereference-operator) (- >)，用于不安全(非托管)的上下文中
*   Lambda 运算符(= >)
*   无效运算符 _)
*   *为*，*为*操作员
*   [被检查的](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/checked)和[未被检查的](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/unchecked)操作员
*   *默认*，*委派*，*新增*操作员
*   *[sizeof](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/sizeof)* 和 *[typeof](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/typeof)* 运算符

# 结论

这是一个介绍性条目，因此您可以知道在 C#语言中哪些运算符可以重载，哪些不能重载。请继续关注下一篇文章，它将介绍一元运算符。