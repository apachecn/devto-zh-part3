# C++ &如何在 C++中使用&符号

> 原文：<https://dev.to/sandordargo/how-to-use-ampersands-in-c-3kga>

**你想每天出一道 C++题，为下一次面试做准备吗？免费注册[每日新闻采访](https://www.dailycppinterview.com/)！**

在我之前的一篇文章中，我写了 Scott Meyer 的[高效的现代 C++](https://amzn.to/2Rbh5pI) ，它专注于 C++11/14，就像发现了一种全新的语言。我已经写了关于[尾随返回类型声明](https://dev.to/blog/2018/11/07/trailing-return-type)。现在是时候回顾一下 C++中的与号(`&`)有哪些用法了。

让我们从更广为人知的古老用法开始:

*   `&`声明对类型的引用
*   `&`获取变量的地址
*   `&`作为一个逐位运算符
*   `&&`在条件表达式中

这些都不新鲜，但是“重复是学习之母”。

## 使用`&`来声明对类型的引用

如果你在变量声明的左边使用了`&`，这意味着你期望有一个对声明类型的[引用](https://www.tutorialspoint.com/cplusplus/cpp_references.htm)。它可以用在任何类型的声明中(局部变量、类成员、方法参数)。

```
std::string mrSamberg("Andy");
std::string& theBoss = mrSamberg; 
```

Enter fullscreen mode Exit fullscreen mode

这不仅仅意味着`mrSamberg`和`theBoss`将具有相同的值，而且它们实际上将指向内存中的相同位置。你可以在这里阅读更多关于推荐人[的信息](https://www.tutorialspoint.com/cplusplus/cpp_references.htm)。

## 使用`&`获得变量的地址

如果在表达式的右边使用,`&`的含义会发生变化。事实上，如果你在左边使用它，它必须在变量声明中使用，在右边，它也可以在赋值中使用。

当在变量的右边使用它时，它也被称为“地址操作符”。毫不奇怪，如果你把它放在变量前面，它会返回它在内存中的地址，而不是变量本身的值。这对于指针声明很有用。

```
std::string mrSamberg("Andy");
std::string* theBoss;

theBoss = &mrSamberg; 
```

Enter fullscreen mode Exit fullscreen mode

前面代码片段的最终结果与前面相同。虽然`theBoss`的类型不同。以前它是一个参考，现在它是一个指针。主要区别在于指针可以为空，而引用必须指向有效值。(嗯...有捷径...但是这超出了本文的范围。).更多关于这个话题的[在这里](https://stackoverflow.com/questions/57483/what-are-the-differences-between-a-pointer-variable-and-a-reference-variable-in)。

## 使用`&`作为按位运算符

它是按位与。它是一个中缀运算符，将两个数字作为输入，并对输入的每个位对执行一个`AND`。这里有一个例子。14 用二进制数表示为 *1110* ，42 可以写成 *101010* 。因此 *1110* (14)将从左侧开始进行零填充，然后操作是这样的。

|  | Thirty-two | Sixteen | eight | four | Two | one |
| --- | --- | --- | --- | --- | --- | --- |
| Fourteen | Zero | Zero | one | one | one | Zero |
| forty-two | one | Zero | one | Zero | one | Zero |
| 14&42=10 | Zero | Zero | one | Zero | one | Zero |

## 在逻辑表达式中使用`&&`

(逻辑)表达式中的`&&`就是 C 风格的`and`的说法。就是这样。

## 使用`&&`来声明右值引用

声明什么？-你可能会问。好的，让我们先弄清楚什么是左值和右值，以及它们的区别。

根据伊莱·本德斯基的说法:

> 左值(定位器值)表示占据存储器中某个可识别位置的对象(即具有地址)。
> 
> 右值是通过排除来定义的，即每个表达式要么是左值，要么是右值。因此，根据上面对左值的定义，右值是一个表达式，它不代表一个对象在内存中占据某个可识别的位置。

让我们举一个例子来展示左值和右值。

```
auto mrSamberg = std::string{"Andy"}; 
```

Enter fullscreen mode Exit fullscreen mode

`mrSamberg`表示左值。它指向内存中标识一个对象的特定位置。另一方面，你能在右边找到的`std::string{"Andy"}`实际上是一个右值。这是一个不能赋值的表达式，那已经是值本身了。它只能位于赋值运算符的右侧。

想要更好更深入的解释，请阅读 [Eli 的文章](https://eli.thegreenplace.net/)。

虽然右值只能出现在右边，但是仍然可以捕捉对它们的引用。那些“捕获”被称为*右值引用*，这样的变量必须用双&符号(`&&`)声明。为了实现移动语义和完美转发，需要绑定这样的临时表。(我将在后面的文章中解释完美转发和移动语义。)

## 使用`&&`来声明通用引用

坏消息是类型后面的`&&`可能意味着或者不意味着你在声明一个右值引用。在某些情况下，它只意味着[Scott Meyers]在他的[有效的现代 C++](https://amzn.to/2Rbh5pI) 中称之为通用引用的东西。

那些情况是什么？简而言之，如果发生类型演绎，则声明一个通用引用，如果不是右值引用。

```
Vehicle car;
auto&& car2 = car; // type deduction! this is a universal reference!
Vehicle&& car3 = car; // no type deduction, so it's an rvalue reference 
```

Enter fullscreen mode Exit fullscreen mode

还有一种可能，就是模板的情况。以[有效的现代 C++](https://amzn.to/2Rbh5pI) :
为例

```
template<typename T>
void f(std::vector<T>&& param);     // rvalue reference

template<typename T>
void f(T&& param); // type deduction occurs, so this is a universal reference! 
```

Enter fullscreen mode Exit fullscreen mode

在模板的情况下，有更多的微妙之处，但同样，这超出了范围。阅读[有效的现代 C++](https://amzn.to/2Rbh5pI) 中的第 24 项，如果你想了解更多关于如何区分通用引用和右值引用的信息。

## [T3 使用`&`或`&&`进行函数重载](#use-raw-amp-endraw-or-raw-ampamp-endraw-for-function-overloading)

我们还没有完成。

从 C++11 开始，您可以将单双&符号用作函数签名的一部分，但不能用作参数列表的一部分。如果我说得不够清楚，让我举几个例子:

```
void doSomething() &;
void doSomething() &&;
auto doSomethingElse() & -> int;
auto doSomethingElse() && -> int; 
```

Enter fullscreen mode Exit fullscreen mode

这意味着你可以根据`*this`是左值还是右值来限制成员函数的使用。所以你只能在类中使用这个特性，当然。让我们扩展一下我们的例子。

```
class Tool {
public:
  // ...
  void doSomething() &; // used when *this is a lvalue
  void doSomething() &&; // used when *this is a rvalue
};

Tool makeTool(); //a factory function returning an rvalue

Tool t; // t is an lvalue

t.doSomething(); // Tool::doSomething & is called

makeTool().doSomething(); // Tool::doSomething && is called 
```

Enter fullscreen mode Exit fullscreen mode

你什么时候会使用这种区分？主要是当您想利用 move 语义来优化内存占用时。在以后的文章中，我会更深入地探讨这个问题。

## 结论

在这篇文章中，你看到了 C++中与号的 7 种不同用法。它们可以以单精度或双精度形式用于变量声明、函数声明和条件表达式中。

我不打算给你一个完整的解释。移动语义和完美转发可以填满好书的多个章节，像在[有效的现代 C++](https://amzn.to/2Rbh5pI) 。另一方面，我会在以后的帖子中对这些话题进行更深入的解释。

*本文最初发表在[我的博客上。](http://sandordargo.com/blog/2018/11/25/override-r-and-l0-values)T3】*