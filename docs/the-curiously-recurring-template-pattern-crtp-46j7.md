# 奇怪地重复出现的模板模式(CRTP)

> 原文：<https://dev.to/sandordargo/the-curiously-recurring-template-pattern-crtp-46j7>

**你想每天出一道 C++题，为下一次面试做准备吗？免费注册[每日新闻采访](https://www.dailycppinterview.com/)！**

在本文中，我们将发现一种被称为奇怪重复模板模式的模式。你好奇吗？请继续阅读！

## 简介

你有没有想过一个派生类，它的基类可以访问派生类的成员？这看起来像是不可能的事吗？不可能？真的吗？但在这个世界上，没有什么是不可能的。甚至不访问派生类的成员。从基地。你需要的只是一些好奇心。

一些奇怪的重复出现的模板模式。CRTP 是 C++中的习语，其中[一个我们称之为 X 的类使用 X 本身作为模板参数](https://en.wikipedia.org/wiki/Curiously_recurring_template_pattern)从一个类模板实例化中派生出来。

```
// The Curiously Recurring Template Pattern (CRTP)
template<class X>
class Base
{
    // methods within Base can use template to access members of Derived
};

class Derived : public Base<Derived>
{
    // ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

像历史上的许多事情一样，CRTP 在世界上的许多地方几乎同时被发现。

这种技术本身更早就形式化了，但是在 C++中，是 Jim Coplien(他写了许多关于高级 C++和一般编程的书)在 1995 年提出了这个名字，并在一些早期的 c++模板代码中观察到了它。

另一条线索将我们引向微软，它也在同一年被发现，并成为活动模板库的一部分。有趣的是，第一批代码审查者之一认为这样的代码甚至无法编译。从编程历史中我们可能知道，它不仅仅是编译，而且 ATL 和 Windows 模板库都大量使用了这种技术。

顺便说一句，您可能听说过这种技术，称之为倒置继承。

## 如何使用 CRTP

CRTP 的使用分为两大类。您可以向派生类中添加一些功能，也可以使用该技术来实现静态多态性。

让我们检查两个类别。

### 添加功能

CRTP 包括:

*   从模板类继承
*   使用派生类本身作为基类的模板参数

如前所述，这种技术的主要优点是基类可以访问派生类方法。这是为什么呢？

基类使用派生类作为模板参数。

在基类中，我们可以通过静态强制转换获得底层派生对象:

```
class Base {
  void foo() {
    X& underlying = static_cast<X&>(*this);  
    // now you can access X's public interface
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

实际上，这给我们带来了通过一些基类来丰富我们的派生类的接口的可能性。在实践中，你可以用这种技术给一个类添加一些通用的功能，比如给一个传感器类添加一些数学函数(就像张克帆·芭卡拉解释的那样)。虽然这些功能可以实现为非成员函数或非成员模板函数，但是当您检查类的接口时，很难知道这些功能。而 CRTP 基类的公共方法是接口的一部分。

#### 给一个类添加数值函数(张克帆·芭卡拉)

下面是完整的例子:

```
template <typename T>
struct NumericalFunctions
{
    void scale(double multiplicator);
    void square();
    void setToOpposite();
};

class Sensitivity : public NumericalFunctions<Sensitivity>
{
public:
    double getValue() const;
    void setValue(double value);
    // rest of the sensitivity's rich interface...
};

template <typename T>
struct NumericalFunctions
{
    void scale(double multiplicator)
    {
        T& underlying = static_cast<T&>(*this);
        underlying.setValue(underlying.getValue() * multiplicator);
    }
    void square()
    {
        T& underlying = static_cast<T&>(*this);
        underlying.setValue(underlying.getValue() * underlying.getValue());
    }
    void setToOpposite()
    {
        scale(-1);
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 对象计数器

添加功能的另一个例子是实现对象计数器。

您可以创建一个计数器基类:

```
template <typename T>
class Counter
{
    static int _createdObjects;
    static int _aliveObjects;

    Counter()
    {
        ++_createdObjects;
        ++_aliveObjects;
    }

    Counter(const Counter&)
    {
        ++_createdObjects;
        ++_aliveObjects;
    }
protected:
    ~counter() // objects should never be removed through pointers of this type
    {
        --_aliveObjects;
    }
};
template <typename T> int Counter<T>::_createdObjects( 0 );
template <typename T> int Counter<T>::_aliveObjects( 0 ); 
```

Enter fullscreen mode Exit fullscreen mode

那么，即使您有一对静态计数器来跟踪已创建的和活动的对象，您也可以为不同的类型使用不同的计数器。所有这一切，因为计数器是一个模板基类，你用派生类来专门化:

```
class X : counter<X>
{
    // ...
};

class Y : counter<Y>
{
    // ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 静态多态

如果你想在 C++中使用多态性，你必须把你想重载的函数声明为虚函数。对吗？

对！但是...不一定。

你可以避免使用虚拟表，所以你可以通过使用静态接口和 CRTP 模式来避免虚拟表的运行时开销。

我们举个例子。我们想要对车辆建模，在这个例子中，接口将有一个方法，`getNumberOfWheels` :

```
template <typename T>
class Vehicle
{
public:
    double getNumberOfWheels() const
    {
        return static_cast<T const&>(*this).getNumberOfWheels();
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以这样创建派生类:

```
class Bus : public Vehicle<Bus>
{
public:
    explicit Bus(int value) : value_(value) {}
    double getNumberOfWheels() const {return value_;}
private:
    int value_;
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
class Scooter : public Vehicle<Scooter>
{
public:
    double getNumberOfWheels() const {return 2;}
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果您真的想节省运行时间，这种技术可能会对您有所帮助。记住，不会有虚拟调用的运行时解析，因为没有虚拟函数调用。

## 结论

奇怪地重复出现的模板模式是一种有趣的技术，至少知道它，有时还会用到它。在该模式的帮助下，您可以从基类访问派生类的公共接口，这对您有很大帮助:

*   通过基类向派生类添加功能
*   实现多态而不需要虚拟表

如果你想获得更深层次的知识，我建议你阅读以下网页和文章:

*   [https://en . Wikipedia . org/wiki/wildly _ recurring _ template _ pattern](https://en.wikipedia.org/wiki/Curiously_recurring_template_pattern)
*   [https://www . fluentcpp . com/2017/05/12/gently-recurring-template-pattern/](https://www.fluentcpp.com/2017/05/12/curiously-recurring-template-pattern/)
*   [https://stack overflow . com/questions/4173254/what-the-chilly-recurring-template-pattern-crtp](https://stackoverflow.com/questions/4173254/what-is-the-curiously-recurring-template-pattern-crtp)

*本文最初发表在[我的博客上。](http://sandordargo.com/blog/2019/03/13/the-curiously-recurring-templatep-pattern-CRTP)T3】*