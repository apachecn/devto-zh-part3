# 重载操作符介绍(由初学者为初学者编写)

> 原文：<https://dev.to/somedood/an-introduction-to-overloading-operators-for-beginners-by-a-beginner-1g0n>

最近，我开始学习 C++这种既漂亮又让人头疼的编程语言。我目前非常投入到通往精通的艰苦旅程中。毕竟，它最终会帮助我成为一名游戏开发者。

来自相对不太难的编程语言 JavaScript，对我来说有很多不熟悉的概念，比如内存管理和头文件。当然，我最初对指针感到困惑，所有初学者都是如此。然而，随着我对这门语言了解的越来越多，我偶然发现了一个如此新、如此奇怪、如此强大、如此有用的概念，以至于我不得不从初学者*自己*的角度为初学者解释它*。*

到本文结束时，我希望用简单的用例和例子有效地解释**操作符重载**的概念——远离所有花哨的术语、语法、技术细节和形式，这些只会让初学者(比如我)感到困惑。出于这个原因，我会有目的地省去与手头的话题不一定相关的话题~~，因为我甚至没有资格谈论它们。~~

在进入本文的核心部分之前，我先简单说明一下，我假设您至少已经掌握了 C++的一些重要特性和概念(类型系统、名称空间、引用、`const`关键字、`struct` s、`class` es 和[成员初始化列表](https://www.learncpp.com/cpp-tutorial/8-5a-constructor-member-initializer-lists/))。否则，你可能会被我写的代码弄糊涂。

# 什么是重载？

为了简化冗长的技术定义，在强类型编程语言中，“重载函数”允许函数在不同情况下接受多组不同类型的参数。换句话说， ***函数对*** 中传递的参数做出相应的反应。最好通过一个例子来说明这一点。

```
#include <iostream> 
// Accepts integers
int overloaded(const int& num) {
  return num;
}

// Accepts three integers
int overloaded(const int& num1, const int& num2, const int& num3) {
  return num1 + num2 + num3;
}

// Accepts booleans
int overloaded(const bool& someBoolean) {
  // The argument is not used in the function to prove that it is overloaded.
  return 100;
}

int main() {
  // The `overloaded` function can accept multiple types and arguments.
  int inputNumber = overloaded(5);
  int sum = overloaded(3, 6, 1);
  int inputBool = overloaded(false);

  std::cout << inputNumber << std::endl; // 5
  std::cout << sum << std::endl; // 10
  std::cout << inputBool << std::endl; // 100

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的例子，但是它很有希望展示重载是如何工作的。考虑到这一点，您可能会猜到“重载操作符”是什么意思。

# 运算符只是函数

在 C++中，操作符可以被认为是函数的语法糖。因为操作符本质上是函数，这意味着重载一些操作符是可能的。

为了证明这一点，假设我们有一个代表一个复数[的`struct`。这个`struct`有两个成员属性，分别以`a + bi`的形式表示一个复数的实部和虚部。它还有一个方法允许你添加两个`ComplexNumber`和](https://www.mathsisfun.com/numbers/complex-numbers.html) 

```
struct ComplexNumber {
  const double a, b;

  // Constructor
  ComplexNumber(const double& a, const double& b) : a(a), b(b) {}

  // Adding method
  ComplexNumber add(const ComplexNumber& z) const {
    const double sumA = this->a + z.a;
    const double sumB = this->b + z.b;
    return ComplexNumber(sumA, sumB);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在`main`函数中，我们现在可以添加两个`ComplexNumber`并将结果打印到屏幕上。

```
int main() {
  ComplexNumber z1(2, 5); // 2 + 5i
  ComplexNumber z2(4, 7); // 4 + 7i

  ComplexNumber sum = z1.add(z2);

  printf("%.2f + %.2fi", sum.a, sum.b); // "6.00 + 12.00i"

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

这很棒，但是链接`ComplexNumber#add`方法太麻烦了。通过操作符重载，我们可以使代码更加易读和直观。

为此，我们使用`operator`关键字来告诉 C++我们想要重载什么操作符。在这种情况下，我们将通过将重载方法命名为`operator+`来重载`+`操作符。因为我们在`struct`中已经有了一个`add`方法，我们可以只使用它的重载实现，如下例所示。

```
struct ComplexNumber {
  const double a, b;

  // Constructor
  ComplexNumber(const double& a, const double& b) : a(a), b(b) {}

  // Adding method
  ComplexNumber add(const ComplexNumber& z) const {
    const double sumA = a + z.a;
    const double sumB = b + z.b;
    return ComplexNumber(sumA, sumB);
  }

  // Overload the `+` operator.
  // This overload returns a `ComplexNumber`.
  // This method takes in a `ComplexNumber` as an
  // argument, which comes from the right-hand
  // side of the `+` operator.
  ComplexNumber operator+(ComplexNumber& z) const {
    return add(z);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们使用`+`操作符，而不是链接`ComplexNumber#add`方法。

```
int main() {
  ComplexNumber z1(2, 5); // 2 + 5i
  ComplexNumber z2(4, 7); // 4 + 7i

  // Using the `+` operator in this case is
  // equivalent to saying:
  // z1.add(z2);
  // The right-hand side of the operator
  // is the input to the overload method.
  ComplexNumber sum = z1 + z2;

  printf("%.2f + %.2fi", sum.a, sum.b); // "6.00 + 12.00i"

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，如果操作符重载被定义为`struct`或`class`的**成员方法**，那么下面三个语句在逻辑上是等价的*。* 

```
// Chaining methods
z1.add(z2).add(z3).add(z4).add(z5);

// Overloading the `+` operator
z1 + z2 + z3 + z4 + z5;

// Adding parentheses to put emphasis on the
// left- and right-hand side of the `+` operator
((((z1 + z2) + z3) + z4) + z5); 
```

Enter fullscreen mode Exit fullscreen mode

# 定义操作员从外部过载一个`struct`或`class`

我们可以通过重载`<iostream>`库中的`cout`类的流插入操作符(`<<`)来进一步重载操作符。在某种程度上，我们的目标是将一个`ComplexNumber`对象作为字符串显示到屏幕上。如果你熟悉 Java 或 JavaScript，这就像覆盖一个对象的`toString`方法。

```
#include <iostream> 
struct ComplexNumber {
  // ...
}

// Overload the `<<` operator.
// The first argument corresponds to the
// left-hand side of the operator, while
// the second argument corresponds to the
// right-hand side of the operator.
std::ostream& operator<<(std::ostream& stream, const ComplexNumber& z) {
  return stream << z.a << " + " << z.b << 'i';
} 
```

Enter fullscreen mode Exit fullscreen mode

在`main`函数中，现在可以将一个`ComplexNumber`对象记录为一个字符串。最重要的是，我们有能力将多个`ComplexNumber`对象“加总”在一起，因为我们重载了它的`+`操作符。此外，由于我们基于`ComplexNumber#add`实现了`+`操作符重载，我们可以通过链接方法调用或使用`+`操作符来选择将多个`ComplexNumber`对象“添加”在一起。无论哪种方式，输出都是一样的。

```
int main() {
  ComplexNumber z1(2, 5);
  ComplexNumber z2(4, 7);
  ComplexNumber z3(1, 9);

  // These statements are logically equivalent
  ComplexNumber sumChain = z1.add(z2).add(z3);
  ComplexNumber sumOverload = z1 + z2 + z3;

  std::cout << sumChain << std::endl; // "7 + 21i"
  std::cout << sumOverload << std::endl; // "7 + 21i"

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 巨大的权力=巨大的责任

让 C++中的每一个操作符过载是很诱人的——是的，这是可能的——我们必须记住，就像生活中的大多数事情一样，“强大的能力伴随着巨大的责任。”必须谨慎使用操作符重载，只有在*完全*和*绝对*必要时才使用。

在编写数学 API 和数据结构的情况下，比如我的`ComplexNumber`例子，从长远来看，它是使代码更加可读和直观的必要工具。这无疑是给代码库(或 API)增加了一层合理的复杂性。

相反，通过让`+`操作符减去它的操作数，我们可以更疯狂地重载操作符。不用说，这是一个非常糟糕的想法，但至少你知道这是可能的。

总之，你不会想让每个操作员都超负荷工作，因为，让我们面对现实吧，那只是一场等待发生的灾难。也占用了你太多的时间。然而，如果这是一个你想尝试的实验，那么继续探索操作符重载的深度。我鼓励你练习你的 C++技能。

*负责任地超载。*