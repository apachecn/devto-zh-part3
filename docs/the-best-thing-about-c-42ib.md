# C++最大的优点是

> 原文：<https://dev.to/nichartley/the-best-thing-about-c-42ib>

> 注意:对于这篇文章，你需要对编程有相当不错的理解。我将解释我在文章中使用的大部分词汇，但是我仍然希望你知道什么是“对象”,而不仅仅是在 OOP 的意义上。

析构函数很棒。当我第一次学习 C++时，它们看起来像魔法一样，现在我对没有它们的语言感到有点恼火。问题是，*大多数*语言没有它们。如果你只用过 Java 或 C#编程，你可能会奇怪为什么我喜欢这么糟糕、无用的东西，因为你从来没有真正体验过析构函数。Java 有`finalize` overrideable 方法，C#有它*称之为*的析构函数(它甚至模仿了 C++的语法)，但这两个都不是真正的析构函数。

# 物体的寿命

在我们讨论析构函数是什么以及为什么它很棒之前，我们需要讨论一些相关的东西:对象生存期。一个对象的生存期就是它作为可用的已分配内存存在的时间。如果你有这个对象的句柄，不管是指针、引用、变量还是其他什么，那么直到这个对象的生命周期结束，你都可以继续使用它。

在大多数语言中，当*每个*对象的生命周期结束时，完全不可能保证*精确到*。然而，在某些情况下，他们比其他人更接近。例如，在 Java 中，对象至少持续到不再被引用，然后直到垃圾收集器开始收集垃圾。你无法保证什么时候会发生，甚至无法保证*是否会发生。*

另一方面，像 C++、Rust 和 Swift 这样的语言有更严格定义的对象生存期。我不会详细说明确切的规则，但经验法则是，一旦引用该对象的每个变量都超出范围，该对象的生存期就会立即结束，而不是在之后的某个不确定点结束。我将这些称为“严格的”寿命，因为它们有一个精确的结束时间。

当然，C++中的指针是个例外；在这种情况下，一个对象的生命周期在它到达`delete` d 时结束。

需要注意的一个重要推论是，当一个对象的生存期结束时，它的字段的生存期也会结束。

# 析构函数

析构函数是一个在严格的生命周期结束时运行的函数。它基本上是构造函数的逆，因此得名。你可以绝对保证:

1.  析构函数将会运行，除非发生非常奇怪的事情。
2.  它将仅在对象不再被使用后运行 <sup>1</sup> 。
3.  它将在对象不再被使用(大多数情况下)后不久运行

第三点有些模糊，但是如果你的语言有严格的生命期，并且你把事情限制在你能合理做到的最小范围内，那么析构函数将在对象最后一次使用后快速运行。

因为一个字段的生存期结束于它的包含对象，所以你不需要显式地调用你的字段的析构函数。它们在包含对象的析构函数之后被调用。

# 他们为什么好？

析构函数允许你做两件相关的事情:更简单的清理和 RAII。

## 更简单的清理

有没有用 C 编写过复杂的东西？您可能得到了不止几个这样的代码块:

```
struct foo *my_foo = construct_foo();
use_a_foo(my_foo);
use_it_again(my_foo);
destruct_foo(my_foo); 
```

这一切都很好，直到你记得`use_a_foo`可能会失败，如果它失败了，我们不应该运行`use_it_again`。如果`use_a_foo`确实失败了，它将返回`false`，所以让我们检查一下:

```
struct foo *my_foo = make_a_foo();
if (!use_a_foo(my_foo)) return false;
use_it_again(my_foo);
destruct_foo(my_foo); 
```

这里的问题是，现在，有时，你没有正确地析构`my_foo`。当然，在这个例子中，修复它是微不足道的，但它很快就失去了控制，试图保持代码清晰，同时也正确地析构每个对象。

相比之下，这是等效的 C++:

```
foo my_foo;
if (!use_a_foo(my_foo)) return false;
use_it_again(my_foo); 
```

一旦`my_foo`超出范围，就会隐式调用`foo`析构函数，因此您不需要管理它。这意味着无论需要做什么清理——关闭文件句柄，释放内存，等等。在 C++中你也有异常，如果你抛出一个异常，当你到达对象所在的作用域之上时，析构函数仍然会被调用*。*

 *## RAII

RAII 是“资源获取即初始化”的缩写，这是一个相当小的飞跃，只要你意识到“清理”可以稍微概括一下。这个名字意味着任何控制资源的东西——不管是线程句柄，文件句柄，还是其他什么——在构造的时候控制它，然后确保在析构的时候清理它。

例如，这是在 C++中获取[互斥](https://en.wikipedia.org/wiki/Mutual_exclusion) es 的一种相当常见的模式:

```
{
  std::lock_guard _l(the_mutex);
  // ... do your processing ...
} 
```

当它被构造时，一个`std::lock_guard`通过锁定来控制一个互斥体。这样，不管在`// ... do your processing ...`中发生了什么，除非你以某种方式强制退出整个线程，否则一旦该块完成，你仍然会解锁互斥体，这是自动发生的。

但是这个概念有所扩大。有些类型会在销毁时进行错误检查，比如`std::thread`，它会确保线程要么执行完毕并重新加入到父线程中，要么被分离，这样它就可以自己继续执行。

# 那么什么是终结器呢？

终结器就像析构器一样，都在生存期结束时运行，但是当生存期不严格时，就使用“终结器”这个词。因为生存期并不严格，并且因为生存期可能直到程序停止才结束，所以您不能保证终结器会运行，或者在对象停止被使用到任何有用的程度后它会“很快”运行。

现在，澄清一下，语言没有析构函数并没有什么本质上的错误。大多数语言都提供了一些解决这种缺乏的方法。在 C#中，您可以这样做:

```
lock (object) {
  // ... do your processing on object ...
} 
```

这可以说是比 C++的锁守卫更好的设计，因为它清楚地表明了*什么*被控制访问，而不仅仅是*什么*被控制。无论发生什么，lock 语句也会及时释放。

更一般地说，任何实现`IDisposable`的东西基本上都可以被赋予一个析构函数:

```
using (Foo f = new Foo()) {
  // use f
} 
```

只有两个小问题:

1.  这是编码时需要学习和记住的另一条语法。
2.  你需要实际实现`IDisposable`，这在糟糕的代码 <sup>2</sup> 中可能做不到。

如果有必要的话，这两者都可以解决，但是与 RAII 固有的简单和优雅相比，这令人沮丧。再说一次，这不一定是一个糟糕的设计，只是不舒服。

# 为什么是终结器？

简而言之，它们对语言设计者和用户都更容易。仅仅是挥挥手让对象销毁消失，然后说“一旦你完成了它，它最终会消失”比编写或学习 Rust 复杂的对象生存期语义要容易得多。就我个人而言，我更喜欢 Rust，因为它*确实*给了我这些信息，并且在大多数情况下避免了垃圾收集的开销，但是当你刚刚开始编程时，它只是已经有一大堆东西要学了。

另外，析构函数对于大多数语言来说并不是必需的。例如，Java 和 C#已经引入了虚拟化层的开销；把一个垃圾收集器扔进混合堆里也没有那么多。添加一些类似`using`或 try-with-resources 的东西，你就有了一个可行的替代方案，尽管会多一点精神负担。

# 提问？

如果你仍然对此感到困惑，请随意发表评论！这可能是一个很难理解的概念，尤其是如果你以前从未接触过析构函数。如果你阅读这篇文章时只有 JavaScript 背景知识...我向你表示哀悼。可能很艰难。谢谢你坚持到底！

* * *

**脚注**

1.  c++*允许你手动调用析构函数(语法是`foo->~type_of_foo()`)但是*不建议这么做*。这导致了各种各样的问题，因为析构函数通常依赖于所有三个假设成立，即使你做的一切都是正确的，你仍然可以破坏东西。*
**   有趣的是，Java 似乎有一种不使用`AutoCloseable`的流行病。有一段时间我很奇怪为什么，但后来我意识到`AutoCloseable`只是在 Java 7 中添加的！如果你处理的代码比泛型还要老，那肯定会令人沮丧。幸运的是，在大多数情况下，它可以很简单地解决。**