# 从头开始制作类似 googletest 的测试框架

> 原文：<https://dev.to/dannypsnl/make-googletest-like-test-framework-from-scratch-2cbg>

回到 2016 年，我学习了 [googletest](https://github.com/google/googletest) 如何改进我的 C++项目。从那以后，我一直想知道它是怎样的。

让我们来看一些基本的例子。

```
TEST(Arithmetic, Integer) {
  ASSERT_EQ(1 + 2, 3);
} 
```

我认为这对任何编写过单元测试的人来说都很容易理解。

在 googletest 的上下文中，你会得到很多`TEST`，比如:

```
TEST(Suit1, test1) {
  // ...
}
TEST(Suit1, test2) {
  // ...
}

TEST(Suit2, test1) {
  // ...
}
TEST(Suit2, test2) {
  // ...
} 
```

回到我们的文章，想一想，为什么编译器不抱怨`TEST`被重定义那么多次，而且从来不指定参数的类型(以及返回类型)？

答:因为那不是函数。

然后问题变成了:那是什么？

答案很明确，是 C 宏！只有宏可以表现得像函数调用格式`TEST(Suit, Test)`。

众所周知，C 宏只是扩展了它内部的代码。所以问题又变了，现在是:在全局级用代码块扩展后，什么样的代码是有效的？

这个问题的答案也很简单:一个函数(或者一个方法实现，因为我们使用的是 C++)。

说了这么多，开始编码吧！创建一个文件`unittest.hpp`，代码:

```
#ifndef UNITTEST
#define UNITTEST 
#define TEST(suit, test) 
#endif 
```

所以我们可以有一个代码为
的`main.cpp`

```
#include "./unittest.hpp" 
TEST(Arithmetic, Integer) 
```

然后编译:`g++ main.cpp`，这个现在应该好了。

然后我们添加一个代码块:

```
TEST(Arithmetic, Integer) {} 
```

哎呀！

```
$ g++ main.cpp
main.cpp:3:27: error: expected unqualified-id
TEST(Arithmetic, Integer) {}
                          ^
1 error generated. 
```

让我们修理它。在您的`unittest.hpp`中，键入:

```
#define TEST(suit, test) void foo() 
```

这一次，编译器非常高兴，没有任何抱怨。所以我们必须进入下一步:如何自动执行这些测试？

为此，我们必须有一个存储(或引用)我们的测试的地方。所以我们创建了一个全局变量。

```
#include <functional> // for std::function
#include <vector> // for std::vector 
std::vector<std::function<void()>> all_test; 
```

并在宏`TEST` :
中添加插入调用

```
#define TEST(suit, test) \
  void foo(); \
  all_test.push_back(foo); \
  void foo() 
```

```
$ g++ -std=c++11 main.cpp
main.cpp:3:1: error: unknown type name 'all_test'
TEST(Arithmetic, Integer) {}
^
././unittest.hpp:11:3: note: expanded from macro 'TEST'
  all_test.push_back(foo); \
  ^
main.cpp:3:1: error: cannot use dot operator on a type
././unittest.hpp:11:11: note: expanded from macro 'TEST'
  all_test.push_back(foo); \
          ^
2 errors generated. 
```

但这是行不通的，让我们看看这里发生了什么。错误信息是关于编译器期望有一个类型`all_test`但是没有，然后它报错一个类型名不能包含`.`。

为了绕过错误并得到预期的插入调用，我们需要一些有趣的技巧。这都是关于 C++构造函数，它承诺在结构创建时被调用。

```
struct unittest_insert {
  unittest_insert(std::function<void()> &&f);
};

unittest_insert::unittest_insert(std::function<void()> &&f) {
  all_test.push_back(f);
}

#define TEST(suit, test) \
  void foo(); \
  unittest_insert ut{foo}; \
  void foo() 
```

现在，让我们将一些打印语句添加到我们的测试中，并实现运行所有测试来证明我们所做的是可行的。`main.cpp`的内容:

```
#include <iostream> 
#include "./unittest.hpp" 
TEST(Arithmetic, Integer) {
  std::cout << "test "
            << "test" << std::endl;
}

int main() {
  run_all_tests();
  return 0;
} 
```

`run_all_tests`的实现:

```
void run_all_tests() {
  for (auto test : all_test) {
    test();
  }
} 
```

现在我们知道如何进行测试。我们需要知道如何确定失败。这就是为什么我们需要断言宏。下面是一个测试的例子:

```
TEST(Arithmetic, Integer) { ASSERT_EQ(1, 1); } 
```

然后看`ASSERT_EQ`是怎么做出来的。

```
#define ASSERT_EQ(le, re) \
  if (le != re) { \
    throw "assert equal failed"; \
  } 
```

运行，有趣的是如果你写了`ASSERT_EQ(1, 2)`，你会得到一个运行时错误提示:

```
libc++abi.dylib: terminating with uncaught exception of type char const*
[1] 35777 abort ./a.out 
```

但是在改进我们的错误报告之前，我们应该考虑一个问题:我们能创建第二个测试吗？答案是**没有**。

你可以试一试，然后从编译器那里得到一个重定义错误的列表。要解决这个问题，我们需要从宏观上得到一些帮助。

```
#define TEST(suit, test) \
  void utf_suit##test(); \
  unittest_insert ut_suit##test{utf_suit##test}; \
  void utf_suit##test() 
```

`##`，来自宏的魔力，你可以从[https://stackoverflow.com/questions/4364971/and-in-macros](https://stackoverflow.com/questions/4364971/and-in-macros)那里得到更多的帮助

现在，我们不会得到扩展宏两次的错误。并有机会停下来思考错误报告设计。

现在，我们只是`throw`一个`char const *`，我们没有捕捉到异常，所以我们会收到一个`terminating with uncaught exception`错误。这导致了两个问题:

*   测试不会继续下去，因为用户不知道实际上有多少测试失败了。
*   用户不知道实际上抛出了什么异常。

要解决这个问题，我们应该做的是捕捉异常，报告并继续下去。下面是代码:

```
#include <exception> 
void run_all_tests() {
  for (auto test : all_test) {
    try {
      test();
      std::cout << "test pass" << std::endl;
    } catch (char const *e) {
      std::cerr << e << std::endl;
    } catch (std::exception &e) {
      std::cerr << e.what() << std::endl;
    } catch (...) {
      std::cerr << "test failed: unknown exception" << std::endl;
    }
  }
} 
```

现在它将报告测试是否通过，作为概念证明看起来不错。

现在算一算，我们从这些中学到了什么？

*   如何从宏生成函数
*   如何处理异常

这里有一些你可以做的练习。

*   改进错误报告，不只是说断言失败，还显示表达式为什么不相等(提示:自定义异常)
*   如何避免用户访问生成的函数？(提示:尝试生成类)
*   我们能报道报道吗？(提示:`llvm-cov`或类似的东西)
*   输入表达式没有可比性怎么办？