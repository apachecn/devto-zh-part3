# 尾部递归

> 原文：<https://dev.to/deciduously/tail-recursion-35nb>

我是一个函数式编程狂热者，当你进行函数式编程时，你会在所有地方重复出现。通常是通过像`map`和`reduce`这样的库函数，而不是编写自己的递归函数，但这是一个非常常见的主题。做对是如此令人满意，并导致一些非常简洁、优雅的实现。

但是*天哪*能慢吗。这对于小案例来说没问题，但是会严重阻碍大程序和输入，而且不总是以容易预测的方式。

减轻这种痛苦的一种方法叫做*尾部递归*。这意味着你可以递归，但是你必须只在函数调用的尾部调用，这意味着递归调用最后调用的是返回值。C++有一个高度优化的编译器，在这种情况下，它实际上可以优化掉递归，使尾部递归函数比非尾部递归函数的性能更好。

基本策略是重用堆栈框架。基本上，每次调用一个函数，它都会将新的*帧*推送到*调用栈*中。这个框架包含评估这个函数(更准确地说，是子例程)的状态信息，比如调用它的参数。这个帧必须被分配到内存中的某个地方，然后被填充并推送到这个堆栈上。所有这些都需要时间和资源，尤其是如果值本身很大的话。在递归函数中，你要求这种情况重复发生，通常参数越来越大。它可能会发疯，尤其是因为这些帧只有在子例程完成时才会弹出调用堆栈并释放——这将在所有子例程完成后*。*

但是，如果在尾部位置递归，下一次递归调用实际上不需要改变堆栈帧。取而代之的是，这些值可以在适当的位置交换，并且已经为这个调用分配的堆栈帧可以被回收。不要把越来越多的帧放在调用栈顶，为越来越多的临时函数调用分配越来越多的内存。这一切都发生在记忆中。快多了。

不过，C++编译器通常比这更聪明，可能会破坏我们的递归，在它的位置弹出一个常规循环，这样会更快。

我会让例子非常简单。下面是你如何在 C++中以递归方式定义`factorial`:

```
int factorial(int n) {
    if (n > 1) {
        return n * factorial(n - 1);
    }
    else {
        return 1;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种实现虽然漂亮、整洁、简单，但不是尾部递归的——它在自身内部调用阶乘*，然后*将结果乘以 n。在这种情况下，乘法在尾部，而不是递归调用。为了使它是尾部递归的，乘法需要发生在函数调用的参数列表中(或者以其他方式发生在它之前)，为此你可以提供一个默认值:

```
int factorial(int n, int b = 1) {
    if (n == 0) {
        return b;
        }
    return factorial(n - 1, b * n);
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数以几乎相同的方式工作，只是重新组织，以便递归调用位于尾部位置，乘法在调用内部。由于运算符的优先级(以及该函数的工作方式)，乘法首先被计算。我们存储结果，因为我们在这个幻影 b 参数中向下递归到 0。这有点像携带额外的状态。第一次迭代我们的默认值是 1 乘以提供的 n 值。如果提供的 n 是零，我们只返回那个 1，否则当我们最终将 n 减少到零时，b 将保存我们想要的值。

通常这些的关键是看你是否能把你的基本情况放入你的参数中，或者使用一个辅助函数，这个辅助函数实际上用存储在它的参数中的所有额外信息递归。另一个常见的递归函数例子是斐波那契数列:

```
int fibonacci(int n) {
    if (n == 0) {
        return 0;
    }
    else if (n == 1) {
        return 1;
    }
    else { 
        return fibonacci(n - 1) + fibonacci(n - 2);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

应该能行，不是吗？

***非***

它会很快喷到你身上，一点都不好笑。把它扔成一个圈，看着它在你眼前慢慢爬行

```
int main() {
    int n;
    std::cout << "nth fibonacci" << std::endl << "N: ";
    std::cin >> n;
    for (int i = 0; i <= n; i++) {
        std::cout << fibonacci(i) << " ";
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我谅你也不敢。

幸运的是，我们可以在那些默认的基础案例中进行重构，使其尾部递归:

```
int fibonacci(int n, int a = 0, int b = 1)
{
    if (n == 0)
        return a;
    if (n == 1)
        return b;
    return fibonacci(n - 1, b, a + b);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，序列是建立在两个基值 0 和 1 上的。没关系，我们可以把它们都放入参数中，然后从那里开始计数。我们只是沿着这条线跳，将`b`参数转移到`a`并构建一个新的`b`。

将`200`放入你的循环打印机。它们都会立即出现，整数溢出问题等等。嘿，这比在十几次迭代后感到无聊并逃避要好得多，对吗？

你在评论中的举动。让我看看你摇摇那些尾递归函数，或者进一步优化这些！