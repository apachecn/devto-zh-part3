# “const”的许多面具

> 原文：<https://dev.to/somedood/the-many-masks-of-const-29c9>

在许多编程语言中，不变性通常被认为是一种“最佳实践”。它降低了向代码库引入不必要的副作用的可能性，从而使代码不容易出现错误。

作为这种“最佳实践”的证明，Rust 中的[变量在默认情况下是不可变的](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html)。人们必须经历显式使用`mut`关键字来声明可变变量的麻烦。

C++，一种以依赖状态和可变性而闻名的语言，也不例外。像许多其他语言一样，`const`关键字用于声明常量“变量”。然而，`const`关键字并不像在其他语言中那样简单。在 C++中，它有太多的含义，每一个都取决于它在每种情况下的用法。在某种程度上，可以说——类似于多重人格分裂——`const`这个关键词戴着许多面具。

# 直白的道

使用`const`关键字最简单、最熟悉、最直观的方式是声明常量。与许多其他编程语言一样，任何声明为`const`的变量(或函数参数)都不能被重新赋值。

```
int main() {
  // Mutable variable
  int variable = 5;
  // Immutable constant
  const int constant = 10;

  // This is legal
  variable = 1;
  // Throws an error
  constant = 25;

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 类字段

与变量类似，类字段也可以声明为常量。从这个类实例化的任何对象都有各自不可变的属性。

```
#include <string> 
class Dog {
  public:
    std::string name;
    const std::string breed;

    Dog(const std::string& dogName, const std::string& dogBreed)
      : name(dogName)
      , breed(dogBreed)
    { }
};

int main() {
  Dog presto("Presto", "Siberian Husky");

  // This is legal
  presto.name = "Not Presto";
  // Throws an error
  presto.breed = "Not a Husky";

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 类方法

类方法也可以声明为`const`。通过这样做，一个类方法*保证*永远不会改变它的任何类字段。

```
#include <string> 
class Dog {
  private:
    std::string name;
  public:
    Dog(const std::string& dogName) : name(dogName) { }

    // This is a `const` method because it does not
    // mutate any of the fields.
    std::string getName() const { return name; }

    // On the other hand, this setter method
    // can **not** be `const` because it
    // mutates/sets a field to a value.
    void setName(const std::string& newName) { name = newName; }
}; 
```

Enter fullscreen mode Exit fullscreen mode

# 指针

关键字`const`变得与指针相当混淆。乍一看，这似乎是一个简单的想法。然而，在指针上使用`const`关键字回避了这样一个问题:到底什么*被声明为`const`。是指向内存地址的*实际指针*还是内存地址的*解引用值*？对于这种困境，C++有办法指定哪个要声明为`const`。* 

```
int main() {
  int foo = 1;
  int bar = 2;
  int baz = 3;

  // Constant actual pointer
  int* const actualPointer = &foo;

  // Constant dereferenced value
  const int* dereferValue = &bar;

  // Constant pointer **and** dereferenced value
  const int* const both = &baz;

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

向后阅读变量声明将大大有助于记住这些细微差别。[这个 StackOverflow 答案](https://stackoverflow.com/questions/1143262/what-is-the-difference-between-const-int-const-int-const-and-int-const/1143272#1143272)总结的*很好*并且提供了更高级的例子和练习。对于初学者和老手来说，这个答案是任何使用 C++的人的必读之作。

# 结论

关键字`const`不仅仅是一种实施不变性的机制。它不仅仅是一个防止有害副作用的安全网。对于开发者来说，它是一个暗示意图的注释。它是一个函数(或变量)如何工作的隐含文档。例如，`const` getter 方法向开发人员保证它不会以任何方式弄乱他们的代码，因为在`const`约束下不可能这样做。

有人可能会认为在整个代码库中添加多个`const`声明是不必要的冗长。是的，这是一个有效的论点，但是隐式文档提供的好处和不变性的安全性完全超过了冗长的缺点(至少对我来说)。此外，它被认为是一个“最佳实践”,理由很充分。

*所以去用尽可能多的* `const` *声明来装饰你的代码库吧！**