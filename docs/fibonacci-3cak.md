# 斐波那契++

> 原文：<https://dev.to/dwd/fibonacci-3cak>

# 啊，斐波那契！

很经典的面试问题！斐波纳契数列通常通过递归来完成，它是一个简单的数列，其中每一项都是前两项之和。

1, 1, 2, 3, 5, 8, 13 ...诸如此类。

我已经记不清人们用这个作为例子的次数了。一般来说，程序员被要求编写一个程序来计算序列中的第 n 个元素。

# 啊，C++！

C++的一个有趣之处是它给了你很多工具可以选择。它被认为是终极的多范例语言。如今，它拥有从协程、生成器到模板的一切。据说它们都很快。

那么如果是这样的话，哪种技术最快呢？

# 一些样板文件。

这并不太科学，所以我们将会创建一个足够简单的计时器。我希望它使用起来简单，所以它只是打印出经过的刻度数:

```
// A code-block timer. Just prints how long the scope block takes.
class Timer {
private:
    using clock = std::chrono::high_resolution_clock;
    std::chrono::time_point<clock> m_start;
public:
    Timer() : m_start(clock::now()) {}
    ~Timer() {
        auto end = clock::now();
        auto duration = end - m_start;
        std::cout << "Elapsed: " << duration.count() << std::endl;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

你可以抱怨这有多可怕。要使用它，只需将它放在 scope 块的顶部——它会在最后自动打印出任意单位的持续时间。

## 试试一:经典递归。

递归是解决斐波那契问题的传统方法。

一个简单的例子是这样的:

```
long long fibonacci_recurse(long long i) {
    if (i <= 2) {
        return 1LL;
    }
    return fibonacci_recurse(i - 1) + fibonacci_recurse(i - 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

这很简单，也很容易理解。我们对列表中的前两项进行了特殊处理(这很常见)，然后简单地返回前两项的总和。

很简单。我用这个代码来调用它:

```
const long long target = 47LL;

// ...

    {
        Timer t;
        std::cout << "(Recurse) Fibonacci of " << target << " is " << fibonacci_recurse(target) << std::endl;
    } 
```

Enter fullscreen mode Exit fullscreen mode

它输出:

```
(Recurse) Fibonacci of 47 is 2971215073
Elapsed: 6912089915 
```

Enter fullscreen mode Exit fullscreen mode

显然我不知道这些是什么单位，但是我可以告诉你这是一个很长的停顿。

我们能做得更好吗？

## 试二:常量表达式。

从 C++11 开始，一个简单的优化方法就是`constexpr`关键字。这允许编译器对一个函数或表达式做一些假设，并且——希望——使事情变得更快。我们来看看:

```
constexpr long long fibonacci_const(long long i) {
    if (i <= 2) {
        return 1LL;
    }
    return fibonacci_const(i - 1) + fibonacci_const(i - 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

它与前面的代码只有一个字的不同，但是编译器应该能够省略许多调用。

```
(Const Expr) Fibonacci of 47 is 2971215073
Elapsed: 6918560577 
```

Enter fullscreen mode Exit fullscreen mode

太好了。我们的优化为我们节省了 600 多万个滴答。哈伦夫。

基本问题是，虽然编译器现在知道(例如)fibonacci_const(45)总是相同的，但它不知道我们将在下一次调用中再次重用它。

事实上，如果我没弄错的话，我们将在 2^37 时间附近计算 fibonacci_const(10 ),但是每次都在不同的范围内，这使得编译器看不到我们可以进行的优化。

具有讽刺意味的是，编译器插入的额外代码——可能是为了让这些结果可重用——实际上稍微减慢了我们的速度。

也许我们可以试试别的？

## 试三:矢

在容器中构建一个序列，而不是递归，可能是一个更有用的计划。当然，这是一种非功能性的做事方式，但也许这样更快一点？我们肯定只会计算每个元素一次。

```
long long fibonacci_vector(long long i) {
    std::vector<long long> fib = {1LL, 1LL};
    while (fib.size() < i) {
        fib.push_back(fib[fib.size() - 1] + fib[fib.size() - 2]);
    }
    return fib[fib.size() - 1];
} 
```

Enter fullscreen mode Exit fullscreen mode

代码只是在一个向量中构建序列，当它足够大时，就返回值。

```
(Vector) Fibonacci of 47 is 2971215073
Elapsed: 13298 
```

Enter fullscreen mode Exit fullscreen mode

现在，正如我所说，我不知道一个扁虱在这里有多长。但我知道 13，298 比 6，912，089，915 要少得多。看来我们有了一个制胜的策略。

我们能让它更快吗？可能是的——虽然 std::vector 非常有效，但我们正在构建一个我们永远不会再使用的值的向量，这似乎是浪费。

## 试四:变量

如果我们完全去掉向量，我们可以复制变量。虽然不太漂亮，但也可以用:

```
long long fibonacci_vars(long long i) {
    if (i <= 2) {
        return 1;
    }
    long long a, b = 1LL, c = 1LL;
    i -= 2;
    do {
        a = b;
        b = c;
        c = a + b;
    } while (--i);
    return c;
} 
```

Enter fullscreen mode Exit fullscreen mode

代码变得有点晦涩，但还是可读的。再来看性能:

```
(Vars) Fibonacci of 47 is 2971215073
Elapsed: 2030 
```

Enter fullscreen mode Exit fullscreen mode

是啊，所以...奏效了。

唯一能让它更快的方法就是完全避免计算。

有个想法。

## 试五:模板功能

C++的模板给了我们在构建时而不是运行时计算序列的有趣可能性。这通常不能用于任何不重要的事情，但是对于面试问题和编程示例来说，任何事情都是公平的，对吗？

我们将从递归解决方案的模板版本开始:

```
template<long long i> constexpr long long fibonacci_template() {
    return fibonacci_template<i-1>() + fibonacci_template<i-2>();
} 
```

Enter fullscreen mode Exit fullscreen mode

不过，对于前两个值，这里没有特殊情况。我们将用一两个模板专门化来添加它:

```
template<> constexpr long long fibonacci_template<1>() {
    return 1LL;
}

template<> constexpr long long fibonacci_template<2>() {
    return 1LL;
} 
```

Enter fullscreen mode Exit fullscreen mode

那里。现在来试试。这次的通话有点不同:

```
 {
        Timer t;
        std::cout << "(Template) Fibonacci of " << target << " is " << fibonacci_template<target>() << std::endl;
    } 
```

Enter fullscreen mode Exit fullscreen mode

而我们得到:

```
(Template) Fibonacci of 47 is 2971215073
Elapsed: 1945 
```

Enter fullscreen mode Exit fullscreen mode

嗯，真令人失望。是的，更快了，但远不如我们所愿。

哪里出了问题？

## 插曲

只是出于好奇，我想知道:

```
 {
        Timer t;
        std::cout << "Absolutely nothing " << target <<  std::endl;
    } 
```

Enter fullscreen mode Exit fullscreen mode

运行它，我们得到:

```
Absolutely nothing 47
Elapsed: 1925 
```

Enter fullscreen mode Exit fullscreen mode

是的，看起来这大部分是开销。我们的变量测试甚至没有注册，真的。将目标值提高到 92——斐波那契数列中适合 64 位`long long`的最后一个元素——也没有太大的区别(尽管早期的测试花费了*长的*时间)。

我们可以把事情改变一下，让它更暴露一点:

```
 {
        long long r;
        {
            Timer t;
            r = fibonacci_template<target>();
        }
        std::cout << "(Template) Fibonacci of " << target << " is " << r << std::endl;
    } 
```

Enter fullscreen mode Exit fullscreen mode

例如，它将`37`打印为节拍数，非常一致。

## 最后一次尝试:模板值

尽管如此，我们将演示另一种解决方案。原则上，模板函数技巧可能必须在运行时执行函数调用。有一个技巧可以避免这种情况，但这意味着在类或结构中定义一个静态字段。

```
template<long long i>
struct fibonacci_template_struct {
    static const long long value = fibonacci_template_struct<i - 1>::value + fibonacci_template_struct<i - 2>::value;
}; 
```

Enter fullscreen mode Exit fullscreen mode

上面是一个模板元函数——它实际上是一个伪装成模板化结构的函数定义。`value`字段是我们的返回值。

同样，我们需要对前两个值进行专门化:

```
template<>
struct fibonacci_template_struct<1> {
    static const long long value = 1LL;
};

template<>
struct fibonacci_template_struct<2> {
    static const long long value = 1LL;
}; 
```

Enter fullscreen mode Exit fullscreen mode

同样，这将完全在构建时完成——但这一次，它在运行时真的没有机会做任何事情。事实上，如果你进一步提高目标，那么当这里的其他例子在运行时溢出时，这个例子将无法编译，并且*编译器*将会给你一个模板堆栈跟踪。

当然，这最终只能打印出答案，因此运行起来完全没有意思...

## 多快才算快？

看起来，我们不能再快了——甚至模板工作都不是真正需要的。但是如果我们使用一个大的数字库，它可能是有用的。

但是实际上，像 Variables 变体中那样，下降到接近汇编的能力对于我们这里的目的来说已经足够好了。那里没有 ANSI C 中没有的东西。

然而，更复杂的问题(可能比简单的二进制加法运算更复杂)可能需要其他的处理方法。在某些情况下，所有这些例子都是有用的，这是毫无疑问的。

编码快乐！