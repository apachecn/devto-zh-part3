# 资源获取是初始化

> 原文：<https://dev.to/dwd/resource-acquisition-is-initialization-120h>

```
Article(); 
```

Enter fullscreen mode Exit fullscreen mode

## 正在建设和破坏中

与许多现代语言不同，C++语言内部没有自动内存管理——至少在默认情况下是这样。

我们将以这个类为例:

```
class Foo {
public:
  Foo() {
    std::cout << "Foo(): Instance created" << std::endl;
  }
  void method() {
    std::cout << "Hello, World!" << std::endl;
  }
  ~Foo() {
    std::cout << "~Foo(): Instance destroyed" << std::endl;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

当构造函数被调用时，当一个实例被创建时，当析构函数被调用时，当一个实例被销毁时，它会被打印出来。

比如看这个函数:

```
void fn() {
  auto foo = new Foo;
  foo->method();
} 
```

Enter fullscreen mode Exit fullscreen mode

每次调用它时，都会在堆上创建一个新的 Foo 实例，并且...失落。没有办法再次获得对它的引用。它还在，被遗忘了，但没有消失。

如果您调用该函数，您将只看到打印的“实例已创建”文本。

但是，如果您创建一个没有`new`操作符的实例，它会在堆栈上创建:

```
void fn2() {
  Foo foo;
  foo.method();
} 
```

Enter fullscreen mode Exit fullscreen mode

每次调用该函数时，都会创建一个“自动”实例，然后在我们离开该函数时销毁。更准确地说，它在名称超出范围时被销毁。

不像很多语言——比如 Java 实例在某个特定的时刻被完全销毁。这里没有垃圾收集——编译器已经为我们明确地插入了代码。

我们可以用一些额外的代码和非标准的`alloca()`函数来模拟这种情况，这个函数可以获得一些原始的堆栈空间:

```
void fn3() {
  auto foo = reinterpret_cast<Foo *>(alloca(sizeof Foo));
  new (foo) Foo; // In-place constructor call.
  foo.method();
  foo->~Foo(); // Manual destructor call.
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，这既可怕又毫无意义，所以我们永远不必这样做。但关键是我们知道析构函数何时被调用。事实上，我们仍然知道在游戏中有额外的物体——物体总是以它们被建造的相反顺序被摧毁。这一切都是令人愉快的决定性的。

## 相当打赏

不过，我的`fn3`并不是在所有情况下都有效。如果我们在`Foo::method`中抛出一个异常，析构函数将永远不会被调用。然而在`fn2`，这仍然可以完美地运作。

你可以有多个`return`语句，它仍然会工作。

没有办法在析构函数不运行的情况下离开这个函数。(嗯，这个*是*c++——可能有一些可怕的方法来欺骗编译器，但你必须非常努力地尝试)

这些析构函数只是函数。你可以在里面放任何你想要的东西。

## 聪明点

在堆上创建对象是有用的——栈的大小是有限的，堆上的对象可以有意地用新的方式来管理，以控制它们的生存期。但是在堆上创建对象意味着编译器为我们做了大量的自动化管理。

现代 C++包括一套“智能指针”,让我们可以两全其美。它们都是模板化的类，理解起来可能有点困难。让我们只为 Foo 做一个:

```
class foo_ptr {
private:
  Foo * m_ptr = nullptr;
public:
  foo_ptr() = default;
  foo_ptr(Foo * f) : m_ptr(f) {}
  ~foo_ptr() {
    if (m_ptr) delete m_ptr;
  }
  Foo * operator ->() {
    return m_ptr;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在尝试我们的第一个函数，但是使用这个智能指针，我们会得到一个不同的结果:

```
void fn4() {
  foo_ptr foo = new Foo;
  foo->method();
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数看起来和原来的`fn()`一样，但是行为和`fn2()`一样——如果你运行它，你会看到它和`fn2()`打印的一样，但是没有泄漏。这是因为编译器自动销毁了`foo_ptr`，它又手动销毁了`Foo`。

如果您想知道，这不会在运行时产生额外的开销。实际的机器代码运行将与`fn4()`和
相同

```
void fn5() {
  auto foo = new Foo;
  foo->method();
  if (foo) delete foo;
} 
```

Enter fullscreen mode Exit fullscreen mode

标准库为我们提供了四种主要的智能指针类型:

`std::unique_ptr<Foo>`为我们保存 Foo，超出范围就删除。您不能复制它们，但是可以移动它们(因此，可以从函数中返回它们)。

`std::shared_ptr<Foo>`可以复制，并且会在最后一个副本超出范围时销毁 Foo。这是基本的引用计数——少量的开销，但大量的实用性和安全性。

`std::weak_ptr<Foo>`不为我们保存 Foo，而是只知道保存在`std::shared_ptr`中的 Foo 是否已经超出范围。

`std::observer_ptr<Foo>`本质上是一个非智能指针，用于向其他开发人员表明您确实打算避免使用其中一个。

智能指针允许我们将手动内存管理转变为自动内存管理。但记忆只是一种资源——其他的呢？

## 足智多谋

在我们的`foo_ptr`中，我们在对象初始化期间分配了我们想要的内存。这意味着对象的生命周期——作为一个堆栈对象，它是由编译器自动控制的——也对应于我们保存内存的时间。

内存泄漏在编程中是如此痛苦，以至于许多语言都致力于解决这个资源问题——结果，几乎每种现代语言都使用某种垃圾收集。但不是 c++——相反，这种语言仅仅提供了管理内存的方法，这些方法几乎同样适用于其他任何东西。

例如，如果你不关闭一个文件，许多语言都会受到影响。相反，语言倾向于让你为对象构造一个作用域，比如 Python 和`with`，或者 Java 的`try (...)`构造。

然而，在 C++中，文件在初始化时打开，一旦超出作用域，就会自动关闭。一个简单的`ifstream`实现——忽略实际的阅读，mind——可能是这样的:

```
class foo_file {
private:
  int m_fd = -1;
public:
  foo_file() = default;
  foo_file(std::string const & s) {
    m_fd = open(s.c_str());
  }
  ~foo_file() {
    if (m_fd >= 0) close(m_fd);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，编译器将为您关闭文件，而不是自动释放内存。

C++不像 Java 那样有一个`synchronised`块。原来就是没那么有用。相反，你可以这样做:

```
// Somewhere:
std::recursive_mutex m_mutex;

void fn6() {
  std::lock_guard<std::recursive_mutex> l; // Give this any name.
  // Do strange thready things.
} 
```

Enter fullscreen mode Exit fullscreen mode

是另一个 RAII 类——它的初始化需要一个锁，当它超出范围时，它会自动释放它。

异常、提前返回等等——编译器知道并会为您处理。

## 资源采集正在初始化

RAII 是 C++编程的一个基本方面，值得花时间去真正理解它。它为我们提供了对内存、文件句柄、锁和所有其他系统资源的可靠管理。它以一种异常安全且高度灵活的方式做到了这一点。

```
~Article(); 
```

Enter fullscreen mode Exit fullscreen mode