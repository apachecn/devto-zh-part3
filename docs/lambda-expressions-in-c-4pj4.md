# C++中的 Lambda 表达式

> 原文：<https://dev.to/sandordargo/lambda-expressions-in-c-4pj4>

**你想每天出一道 C++题，为下一次面试做准备吗？免费注册[每日新闻采访](https://www.dailycppinterview.com/)！**

通读 [Scott Meyer](https://www.aristeia.com/) 的[高效的现代 C++](https://amzn.to/2EiL37U) 帮助我发现了许多现代 C++的特性，包括[右值引用](http://sandordargo.com/blog/2018/11/25/override-r-and-l0-values)、[尾随返回类型声明](http://sandordargo.com/blog/2018/11/07/trailing-return-type)和 lambda 表达式。在这个帖子里说说那些兰姆达斯吧。

你可能会想，拜托，这是老东西了，每个认真的开发者都应该知道 lambda 表达式。你可能是对的，但事实并非如此。最近我做了一个关于 lambdas 的非正式会议，在大约 15 个开发人员中，我们中的两个已经在 C++中使用了 lambdas，另外两个已经在 Java 中使用了。所以需求是存在的。

## 什么是 lambda 表达式？

Lambda 表达式是匿名函数。它们是一些小代码片段，如果没有隐藏在封闭类中，在大多数情况下可以提供更好的可读性。顺便说一下，在 C++中，这些封闭类被称为函子或函数对象。我们将在一分钟内介绍它们。

所以我们可以说，lambda 表达式是为了取代函子，让代码更有表现力。通过其易用性和极端的表达能力，它们促进了标准模板库的使用。

在这一点上，我必须坦白。我以前 C++很烂。我知道基本的语法，当然，我一直在提高代码的可读性，但是我对 STL，标准库，以及基本语法之外的所有东西的了解非常少。当我寻找一个新的团队时，对我来说，加入一个纯 C++团队是一种妥协。我更喜欢 Java 和 python。可能是因为我在他们的生态系统中活动得更舒服。

在我的新团队中，尽管我也在 Java 部件上工作了几周，但我最终还是做了 C++项目，并下定了决心。我决定今年更好地学习 C++，至少达到中高级水平。这段旅程帮助我~~爱上 C++~~ 比以前更喜欢它。兰姆达斯是这种新关系的重要组成部分。

适可而止。让我们回到我们的话题。

## lambdas 替代什么？傅；饲料单位...

函子，没错。函子，或者他们的娘家姓，函数对象是类的实例，其中`operator()`被覆盖。所以你可以这样称呼他们:

```
FunctorClass aFunctor;
aFunctor(); 
```

Enter fullscreen mode Exit fullscreen mode

或者如果它带一个参数:

```
FunctorClass aFunctor;
aFunctor(42); 
```

Enter fullscreen mode Exit fullscreen mode

定义它们非常简单。它们是普通的类，只是覆盖了`operator()`。

让我们快速画出一个函子，它将决定一个给定的数是否在 0 和 10 之间。

```
class IsBetweenZeroAndTen {
  public:
  bool operator()(int value) {
    return 0 < value && value < 10;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

相当容易，但是有时你真的不关心可重用性，你不想为这个功能找到一个合适的位置。你只是想一次性地定义它。兰达斯，他们来了！

## 语法

让我们学习一点 C++ lambda 语法。首先，我们将有一个小的概述，然后我们进入细节。

### 概述

```
[/* capture */] (/* parameters*/) { /* body */ } 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单。所以让我们把函子重写为 lambda 表达式:

```
 {
  return 0 < value && value < 10;
} 
```

Enter fullscreen mode Exit fullscreen mode

因为它非常简单，只要看代码，不用名字你就能很容易理解。你不需要在某个地方放置一个类，你只需要动态地声明它。然而，您可能认为给它添加一个名称可能有助于提高代码的可读性。那也行，有这种情况，还是，不用写类，保存在变量里就行:

```
auto isBetweenZeroAndTen =  {
  return 0 < value && value < 10;
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，就是这么简单。你对它的类型感兴趣吗？尝试使用`decltype`获取。

我们继续吧。

### 捕捉

C++ lambdas 的好处在于你可以练习英语。里面有各种类型的括号。你将不得不处理圆括号或圆括号(`()`)、方括号或方括号(`[]`)和大括号或花括号(`{}`)。先说方形的；

在 lambda 表达式的范围内，它们被称为捕获。到目前为止，你只看到它们是空的。他们捕捉到了什么？它们可能捕获不作为参数传递给 lambdas 的变量，并且它们也不是在内部创建的。

让我们回到我们的例子`isBetweenZeroAndTen`。假设我们想要上界变化。

```
auto upperBound = 42;
 {
  return 0 < value && value < upperBound; // doesn't compile, WTF is upperBound?
} 
```

Enter fullscreen mode Exit fullscreen mode

这个不会编译，因为在λ`upperBound`的范围内是未知的。它必须抓住它。让我们看看如何！

#### 一无所获

好吧，当它们是空的时候，它们什么也捕捉不到。这是愚蠢的简单。

#### 按值捕捉

写`[upperBound]`我们的 lambda 就会有它的值。

```
auto upperBound = 42;
[upperBound](int value) {
  return 0 < value && value < upperBound;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 参照捕获

使用,你可以通过引用来捕获变量，而不是值。

```
auto upperBound = 42;
[&upperBound](int value) {
  return 0 < value && value < upperBound;
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着——至少——两件重要的事情:

*   捕获变量的值甚至可以针对外部世界进行修改
*   你必须确保一旦 lambda 被执行，引用的变量仍然存在

#### 按值捕捉所有

`[=]`将保存"所有" lambda 体中需要的变量的值。听起来很有趣？你注意到我把所有的都写在双引号里了吗？我这样做是因为我们必须理解“*所有的*变量意味着什么。All 表示所有非静态局部变量。例如，如果你在 lambda 中引用一个成员变量，即使你在 lambda 声明的旁边使用了它，它也不会工作。

```
m_upperBound = 42;
[=](int value) {
  return 0 < value && value < m_upperBound; // doesn't compile, m_upperBound is not a non-static local
} 
```

Enter fullscreen mode Exit fullscreen mode

如何解决这个问题？有两个简单的方法。一种是制作一个本地副本并捕获它。

```
m_upperBound = 42;
auto upperBound = m_upperBound;
[=](int value) {
  return 0 < value && value < upperBound;
} 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法是传入整个周围对象，`this`，我们稍后会看到。

#### 通过引用捕获所有

`[&]`有了这个捕获块，所有必要的和可用的变量将通过引用被捕获。同样的概念也适用于通过值捕获所有变量。

别忘了。如果一个被捕获的变量在被捕获后超出了范围，那么你就有大麻烦了。

#### 按值捕捉所有，但

使用`[=, &divisor]`作为捕获，除了前面带有`&`的明确列出的变量之外，所有内容都将被值捕获。

#### 捕捉所有引用，但

使用`[&, divisor]`作为捕获，除了明确列出的变量之外，所有内容都将通过值来捕获。

#### 俘获`this`

如前所述，只有非静态局部变量可以与捕获块一起保存。但正如生活中经常发生的那样，这是有区别的。也可以这样保存周围的物体:`[this]`。`this`是一个指向封闭对象的指针，所以如果您捕获了`this`，您将可以访问成员，例如:

```
 [this](int value) {
  return 0 < value && value < this->m_upperBound;
} 
```

Enter fullscreen mode Exit fullscreen mode

但是我们不应该忘记`this`是一个指针。如果在我们捕获它和执行 lambda 之间它不再存在，我们将不得不面对未定义的行为。

### 参数列表

参数列表通常放在圆括号(`()`)中。一些评论:

*   在 C++11 中，你不能使用`auto`作为类型说明符。但是从 C++14 开始，你可以。
*   如果没有参数传递给 lambda，空列表可以省略。这意味着`[]{}`是一个有效的λ表达式。尽管出于可读性的原因，最好不要去掉空括号。

### 返回类型

嗯...在我们的例子中没有返回类型，那么这个部分在这里做什么呢？为什么在参数列表之后？

lambda 表达式的返回类型可以是，并且通常在以下情况下被省略

*   它是空的
*   或者它是否是可推断的(所以如果你可以使用`auto`)

因此，在实践中，大多数时候返回类型都被省略了。事实上，在生产代码中，我从未见过具有显式返回类型的 lambdas。

如果您必须或者想要声明它们，您必须使用[尾随返回类型语法],这意味着您将在参数列表和主体之间声明类型，将类型放在如下箭头之后:

```
 -> bool {
  return 0 < value && value < 10;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 正文

这只是一个正常的身体。作为最佳实践，它应该是一个非常精简的实践。如果你需要更长，更重的东西，也许 lambda 不是你要去的地方。

提醒一下，您可以使用以下变量:

*   主体中声明的局部变量
*   传递给 lambda 的参数
*   方括号内捕获的非静态局部变量称为*【捕获】*

再次强调，如果你选择捕获引用，你必须确保当 lambda 被执行时，被引用的变量仍然有效。

## 优点

我已经提到了使用 lambdas 的一些优点:

*   不需要写完整的类
*   不需要为这个类找到一个合适的名字
*   不需要为班级找一个好地方
*   增强了简单用例的可读性。

还有一点要提一下。[在这里](https://cppinsights.io/lnk?code=I2luY2x1ZGUgPGNzdGRpbz4KI2luY2x1ZGUgPHZlY3Rvcj4KI2luY2x1ZGUgPGFsZ29yaXRobT4KCmNsYXNzIGZ1bmN0b3J7CiAgcHVibGljOgogIGJvb2wgb3BlcmF0b3IoKShpbnQgdmFsKSB7CiAgICByZXR1cm4gMCA8IHZhbCAmJiB2YWwgPCAxMDsKICB9Cn07CgppbnQgbWFpbigpCnsKICAgIHN0ZDo6dmVjdG9yPGludD4gY29udGFpbmVyIHsyLDQsNiw4LCAyNX07CgogIAlzdGQ6OmZpbmRfaWYoY29udGFpbmVyLmJlZ2luKCksIGNvbnRhaW5lci5lbmQoKSwgZnVuY3RvcigpKTsKICAKICAgIHN0ZDo6ZmluZF9pZihjb250YWluZXIuYmVnaW4oKSwgY29udGFpbmVyLmVuZCgpLAogICAgICAgCQkgICAgICBbXShpbnQgdmFsKSB7IHJldHVybiAwIDwgdmFsICYmIHZhbCA8IDEwOyB9KTsKICAKfQ==&rev=1.0)你可以检查一个函子将生成多少代码。默认的构造函数，移动构造函数，复制构造函数，析构函数，除了操作符重载之外，lambda 什么都没有。哦，还有一个。编译器不会发现你是否忘记将`operator()`重载声明为 const。对 lambda 来说没问题。

## 一些例子

现在我们已经理解了 C++ lambda 表达式的语法，让我们来看几个关于它们用法的例子。我将坚持使用 C++11 语法，这意味着我不会在参数列表中使用`auto`关键字，在 STL 算法中，我不会使用[范围](https://www.fluentcpp.com/2018/02/09/introduction-ranges-library/)。

### 对列表中的所有元素做同样的事情

假设我们有一个`Widget`的列表，你想调用它们的`resize()`方法。

非λ方式:

```
auto widgets = std::vector<Widget> { … }; // a bunch of widgets
for (auto& widget : widgets) {
  widgets.resize();
} 
```

Enter fullscreen mode Exit fullscreen mode

λ道

```
#include <algorithm>  // ...

auto widgets = std::vector<Widget> { … }; // a bunch of widgets

std::for_each(std::begin(widgets), std::end(widgets), 
   {
  widgets.resize();
} ); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，是否真的要用 lambdas 就值得商榷了。语法有点笨拙，但它对所有 std 容器都是通用的，您可以定义想要迭代的范围。

如果我们采用传统的 C++0x 方式，我们甚至可以看到可读性方面的优势:

```
for(std::vector<Widget>::iterator it = widgets.begin(); it != widgets.end() ; ++it)
{
   widgets.resize();
} 
```

Enter fullscreen mode Exit fullscreen mode

这些迭代器很难管理。

但是通过这个例子，我们可能已经知道，在 STL 算法中，lambdas 将变得很方便。

### 获取一个字符串的所有整数

我知道，我知道，你可以用正则表达式很容易地做到这一点。但是假设你不想。

```
#include <string> #include <algorithm> #include <cctype>  
auto another = std::string{};
std::copy_if(std::begin(input), std::end(input),
            std::back_inserter(another),
             {
                return std::isdigit(c);
            }
); 
```

Enter fullscreen mode Exit fullscreen mode

`copy_if`函数将在由前两个参数定义的范围内迭代。第三个参数定义了如果最后一个参数定义的条件为真，将下一个字符复制到哪里。

在最后一个参数中，我们定义了一个 lambda 表达式。它获取一个字符作为参数，并根据传入的字符是否是数字返回`true`或`false`。幸运的是，在标准库中，有一个函数要做，这意味着我们不必尝试强制转换它，也不必检查它的 ASCII 值。

### 写一个函数检查字符串是否小写

同样，这也可以用正则表达式来完成，但是用 lambda(或者不用...).如果它更快或更慢，那应该被测量。

```
#include <string> #include <cctype> #include <algorithm>  
auto isLower(const std::string& phrase) -> bool {
    return std::all_of(std::begin(phrase), std::end(phrase), {return std::islower(c);});
} 
```

Enter fullscreen mode Exit fullscreen mode

`std::all_of`遍历前两个参数定义的范围，如果第三个参数中定义的 lambda 为所有值返回`true`，则返回`true`。如果至少有一个计算结果为`false`，则整个表达式返回`false`。再次，幸运的是`cctype`头有助于我们判断一个给定的字符是否是小写的。

### 对智能指针使用自定义删除器

作为最后一个例子，让我们来看看指针的阴暗世界。

大概我们都听说过，我们应该使用智能指针，而不是 new and all。如果我们必须处理动态内存分配和所有权，最好根据我们使用的 C++版本从 boost 或标准库中选择一个合适的智能指针。

当我们闪亮的智能指针到达它的寿命终点时，它里面保存的原始指针就会被删除。但是如果这不是我们唯一想做的事情呢？

你可能会问我们还想做什么。假设我们想要记录。如果你想看更多的用例，请阅读[这篇文章](https://www.bfilipek.com/2016/04/custom-deleters-for-c-smart-pointers.html)。

在需要一些额外工作的情况下，我们必须为智能指针定义一个删除器，并将其作为参数传递给声明。

你可以定义一个删除类，一个仿函数，或者你可能已经猜到了，你可以像这样传递一个 lambda:

```
std::shared_ptr<Widget> pw1(new Widget, { ... }); 
```

Enter fullscreen mode Exit fullscreen mode

缺点是你不能使用`make_shared`，但那是另一个故事，不是 lambdas 的错。

## 结论

我希望您喜欢这个不太新的 C++ lambdas 世界的短暂旅程。我们不仅讨论了为什么应该使用 lambdas，还详细讨论了它们的语法，并看到了几个例子。

如果你只学过 C++0x，你应该记住 C++最近获得了很多特性，而且它变得越来越有表现力，就像 lambdas 向我们展示的那样。

编码快乐！

*本文最初发表在[我的博客上。](http://sandordargo.com/blog/2018/12/19/c++-lambda-expressions)T3】*