# 永远不要相信编译器错误消息

> 原文：<https://dev.to/therealdarkmage/never-trust-compiler-error-messages-27o8>

一位 codementor 同事发了一篇名为[基础调试](https://www.codementor.io/erikeidt/basic-debugging-v75z9dcbw?utm_swu=7179)的帖子。这家伙根本不知道如何调试。他提到了两种方法，但随后开始讲述如何编写简单的代码。在我看来，这并不是真正的调试。当然，你经常不得不为了调试而写代码，但是这并不完全一样。编写简单代码的过程不同于调试过程。

这家伙开始写简单的代码，实际上，他的咆哮根本不是关于调试的。

首先，我个人觉得简单代码应该是标准。每行一个语句。很少将函数调用混合到其他语句或表达式中。保持简单，这样当错误发生时，您可以将 bug 减少到一个行号(通常)。

我认为这篇文章有点让我反感，因为我经常和学生一起经历调试过程，重写和/或重构(请上帝告诉我区别)代码的过程通常是在调试过程中完成的，但不是过程本身。通常，你应该想要重写你自己的或者其他人的代码以达到清晰的目的，特别是当你不太明白事情是如何实现的时候。将语句和调用混合到复杂的一行表达式中，对代码的效率、速度或规模都没有任何好处。

我在想我最喜欢的虫子，最讨厌的。通常，您已经在其他地方编写了完美的逻辑代码，但是您忘记了一个关键的语法字符，编译器/解释器就开始出错了。在高中的时候(大约 2003-2004 年)，我让一个 C++编译器因为省略了一个分号而抛出了 100 多个错误(这就是我对 C/C++/Java/Javascript/PHP 学生最常说的一句话:“不要忘记你的分号”)。

我想知道...我能故意让那个错误再次发生吗？如果是这样，我当然可以告诉你如何解释编译器产生的错误信息。

让我们来看一个简单的 C++示例程序。我们声明一个名为`MyClass`的类，并编写一个基本的 Hello World 程序。

你能发现这个语法错误吗？

```
1  #include <iostream>
2  
3  using namespace std;
4  
5  class MyClass {
6      public:
7          MyClass() {
8  };
9  
10  int main() {
11      cout << "Hello, world!" << endl;
12      return 0;
13  } 
```

Enter fullscreen mode Exit fullscreen mode

如果您说“第 7 行声明的构造函数缺少一个右花括号”，那么您是正确的！

然而，让我们看看编译器`clang++`对这段代码是怎么说的:

```
clang++ main.cpp                                                                                                           
main.cpp:13:2: error: expected '}'
}
 ^
main.cpp:5:15: note: to match this '{'
class MyClass {
              ^
main.cpp:13:2: error: expected ';' after class
}
 ^
 ;
2 errors generated. 
```

Enter fullscreen mode Exit fullscreen mode

编译器在文件末尾引用了花括号，但是发现类声明符缺少了它的右花括号。它实际上引用了第 13 行两次，并声称有两个错误:

1.  它认为类声明符缺少右花括号
2.  它为类声明符匹配的右大括号缺少分号

这两个错误都是由于编译器解析代码的方式而产生的。错误本身并不是我们实际面临的逻辑、语法问题，所以你不能完全相信错误消息。

让我们看看另一个编译器的输出:`g++`

```
main.cpp:13:1: error: expected ‘}’ at end of input
 }
 ^
main.cpp:13:1: error: expected unqualified-id at end of input 
```

Enter fullscreen mode Exit fullscreen mode

又是两个错误，但是这一次实际的错误消息是不同的。Clang 实际上意识到了类声明符右花括号的丢失。这并不是要贬低 g++或任何东西，但这是我第一次真正比较两者的输出。

如果你只是盲目地在程序末尾添加一个额外的花括号，想着“哦，我只是在末尾少了一个花括号，让我们添加它吧”，会发生什么

```
g++ main.cpp 
main.cpp:14:2: error: expected ';' after class
} 
```

Enter fullscreen mode Exit fullscreen mode

好的，那么，现在让我们添加缺失的`;`:

```
Undefined symbols for architecture x86_64:
  "_main", referenced from:
     implicit entry/start for main executable
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation) 
```

Enter fullscreen mode Exit fullscreen mode

现在它连我的主程序都找不到了！

```
#include <iostream>

using namespace std;

class MyClass {
    public:
        MyClass() {
};

int main() {
    cout << "Hello, world!" << endl;
    return 0;
}
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在编译器甚至没有返回错误行号或任何东西，它只是说它找不到`int main()`。这是一个比以前更糟糕的处境，都是因为你不能花时间去阅读你自己的代码...

啧啧啧。

我们该怎么办？

许多可以用来研究这类问题的技术都可以用来简化查找 bug 的过程。适当地格式化你的代码，使花括号和缩进对称，并允许你的眼睛只看代码，而没有任何可怕的格式化(我对此有一个咆哮，并将最终被写出来)是好的，但这本身只是使调试更容易，而不是调试本身。

如果你连编译器的错误信息都不相信，你还能相信什么？

*   当您的编译器编译代码失败时，您可以信任它。代码编译并不意味着它运行起来是安全的。
*   你可以相信你的程序崩溃。通常，分段错误是由于不正确的指针使用和内存引用造成的。有没有尝试过访问位置 0 的内存？也许这也将是另一篇博文。
*   你可以相信你的代码的输出，当它成功地编译并返回你告诉它的东西时，所以如果它返回不正确的东西，那是因为你的代码在逻辑上/语义上是错误的，你需要看看你到底告诉计算机做什么。通常，你认为你告诉计算机做的事情并不是你想要告诉它的。计算机没有能力很容易地推断出你的意思(打字检查，有人吗？)所以你的指令一定要明确。少一点就是在要求暧昧。如果你没有得到你想要的结果，检查一下你对已经编写和编译的东西的先入之见，看看有什么需要改变的。

编译器错误消息有时只是提示您的代码有什么问题，尤其是在 C++中，通常没有任何帮助。

确保你的花括号使用你喜欢的样式，不管是斯特劳斯特鲁普样式还是 K&R 样式:

*斯特鲁普式*

```
int main() {
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

*K & R 型*T2】

```
int main()
{
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

还有，别忘了你的分号！

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)