# 信号槽

> 原文：<https://dev.to/dwd/sigslot-4iff>

## 信号槽游戏攻略

SigSlot 是我几年前采用的一个库，作为 C++中“[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)的一个可靠实现。

它可以在 GitHub 上获得，免费使用，由一个小的头文件组成。它支持带有 lambda 和指向成员的指针连接的经典观察器模式，也支持在协程中用作可调整对象。

## 一点历史

最初，用 C++编写这样的东西很困难，如果不是不可能的话，而且最常见的实现要么是非标准的，要么依赖于专业的预处理器。

基本概念是你有一个信号对象:

```
sigslot::signal0<> my_signal; 
```

Enter fullscreen mode Exit fullscreen mode

...还有哪些东西可以听:

```
my_signal.connect(this, &MyListener::slot); 
```

Enter fullscreen mode Exit fullscreen mode

...然后信号可以发出:

```
my_signal.emit(); // Calls its listeners. 
```

Enter fullscreen mode Exit fullscreen mode

萨拉·汤普森编写了最初的 SigSlot，他认为新的“ISO C++”标准在模板语言中有足够的灵活性来支持这个概念——事实上，它也是这样。据我所知，她明确地试图镜像 Qt 的信号和插槽预处理程序。

但是 ISO c++——用现代的术语来说就是 c++ 03——缺少一些有用的特性，这些特性会让程序员的生活更加顺畅。Sarah 的设计给了 signals 参数——一个有用且通用的特性——但是尽管她可以使用模板，因此参数的类型并不重要，但她必须为不同数量的参数使用不同的模板。

兰姆达斯也不存在，这使得更自然的适合。

最后，虽然 Sarah 已经使这个库成为线程安全的，但是 C++03 中没有线程原语，所以需要提供几个不同的模型。

然而，当我看到这个库时，我对它的简单性印象深刻——一个头文件包含了整个库，而且使用起来很简单。所以我更新了一些东西——很大程度上是为了学习新的(对我来说)C++11。

## SigSlot C++11

当 C++11 广泛可用时(2014 年)，我采用了 Sarah 的 SigSlot 库，并用新标准的几个特性对其进行了更新。

首先，我使用了“可变模板”，这意味着无论信号采用的参数数量或类型如何，现在都只有一个单一的“信号”模板。

其次，我给了它对 lambdas 以及(有点晦涩的)指向成员的指针语法的支持。

我没有做线程，后来我注意到其他方面也可以改进。尽管如此，它还是很方便，而且图书馆缩小了很多。虽然这个库总是一个单独的头文件，但是通过使用一点 C++11，我把它从 2500 行代码缩减到 500 多行。

## 而现在，C++17...

我最后的改动是引入了 C++14 和 C++17 的特性，并检查和润色了一些我遗漏的 C++11 部分。

首先要做的是不同的线程模型——我现在使用 C++11 线程原语，因此，用法中最后剩下的一个缺点已经消失了。一些模板也是如此——如果没有不同的线程模型，就不需要一些模板层次结构(事实上，我在 2014 年错过了一些简化)。

我还添加了一些协程支持——这可以在使用 MSVC 的 Windows 和使用 CLang 的 UNIXes 上运行。尽管添加了大约 100 行协程支持，库仍然较小——现在只有 430 行。

自 Sarah 的版本以来，这个库的使用并没有太大的变化——事实上，这个版本在某些方面更接近于她的版本，而不是我的 C++11 版本。然而，在其他情况下，情况明显不同。

```
// Only one template, parameters are signal arguments:
sigslot::signal<int> my_signal;

// ...using lambdas, for example:
my_signal.connect(this, [this](int i){
  std::cout << "I was signalled with " << i << std::endl;
});

// Emitting is by either emit(...) or simply calling the signal:
my_signal(4);

// Or, do a coroutine:

coroutine_type<int> coroutine() {
  int i = co_await my_signal;
  co_return i;
}

auto c = coroutine();
// This awaits, so stops immediately.
my_signal.emit(5);
// Now it's continued and is ready to return its value:
std::cout << "I was signalled with " << c.get() << std::endl; 
```

Enter fullscreen mode Exit fullscreen mode

在源代码树中有更多的例子，包括经典的和基于协程的信号的例子，它们可以在 UNIX 和 Windows 上干净地编译。

## 为什么？

还存在其他信号/插槽库——例如，Boost 就有一个。那我为什么还要坚持这个呢？从某种程度上来说，这是一个小而简洁的实现——一个在我参与之前就存在的设计。

但是像这样的小库对我学习 C++的新特性很有帮助。对我来说，这是一个有趣的游戏，也是一个产生有用代码的游戏。我在 XMPP 中广泛使用 SigSlot，met。

你可以随意使用它——或者使用它，甚至更好——它已经放弃了版权(也就是说，在现代版权法中，它尽可能地接近“公共领域”),并且可以在 [GitHub](https://github.com/dwd/sigslot) 上获得。