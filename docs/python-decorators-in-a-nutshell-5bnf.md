# 简而言之，Python 装饰者。

> 原文：<https://dev.to/svinci/python-decorators-in-a-nutshell-5bnf>

## 装修工到底是什么？

装饰器是一种扩展软件行为的方式，实际上并不需要修改它们。

特别是在 Python 中，它们是可以改变其他函数的行为而不改变其代码的函数。它们提供了一种向已经定义的功能添加辅助功能的透明方式。

例如，假设我们想测量程序中执行几个函数所花费的时间。我们可以围绕它们的行为编写一个计时器，方法是修改它们所有的行为，**或**我们可以创建一个装饰器，将一个函数计时的逻辑集中在一个地方，并将其应用于几个函数。

## 我们需要先了解的几个概念...

### 在 Python 中，函数是一级对象。

这意味着函数可以赋给变量和参数，甚至可以由其他函数返回。

返回其他函数或接收其他函数作为参数的函数称为高阶函数。

```
def prepare_hello(name: str):
    def say_hello():
        print("Hello, {}!".format(name))
    return say_hello

hello_brian = prepare_hello("Brian")
hello_brian() 
```

在上面的代码片段中，`prepare_hello`是一个高阶函数，因为它返回另一个函数。此外，我们可以看到如何将函数分配给变量，因为`hello_brian`是一个保存由`prepare_hello`返回的函数的变量。

Python 中两个已知的高阶函数是`map`、`filter`、`sort`和`reduce`。这些函数接收集合*和函数*作为参数，让我们以不同的方式使用给定的函数处理集合。

### `*args`和`**kwargs`。

你可能已经知道这些人了，他们在 Python 领域非常有名。`*args`和`**kwargs`都允许你给一个函数传递可变数量的参数。如果签名包含这两个人中的一个或两个，你的功能基本上就是一张空白支票。

```
def blank_check(*args, **kwargs):
    for arg in args:
        print("Arg: {}".format(arg))
    for keyword, arg in kwargs.items():
        print("Kw: {}, Arg: {}".format(keyword, arg))

blank_check(1, 2, 3, hello="Hello", word="World") 
```

下面是上面函数的输出:

```
Arg: 1
Arg: 2
Arg: 3
Kw: hello, Arg: Hello
Kw: word, Arg: World 
```

如您所见，不带关键字传递的参数由`*args`处理，带关键字传递的参数由`**kwargs`处理。

这允许我们编写完全不关心它们所接收的参数的函数，这将使编写多用途的装饰器容易得多。

需要注意的是，`*args`和`**kwargs`的重要部分是美学家。这个名字可以是任何东西，就像任何其他变量一样，`args`和`kwargs`是约定俗成的用法，因为这种做法有点阴暗，需要马上发现。

## 现在，装修工们！

装饰器基本上是包装其他函数的函数。在 Python 中，真的就这么简单。

```
def a_decorator(wrapped_function):
    def wrapper(*args, **kwargs):
        print('I am the decorator!')
        return wrapped_function(*args, **kwargs)
    return wrapper 
```

这就是装潢师的定义，就在这里。你可以看到两件有趣的事情:

*   装饰器接收它应该包装的函数作为参数，**和**返回一个包装函数。因此，装饰器是一个高阶函数。
*   装饰器通过使用`*args`和`**kwargs`，根本不关心**被包装的函数的参数是什么**，它只是做自己的事情，用所有给定的参数调用被包装的函数。

现在，我们可以随时随地使用它:

```
@a_decorator
def sum_numbers(num_a, num_b):
    return num_a + num_b

print(sum_numbers(1, 2)) 
```

输出将是:

```
I am the decorator!
3 
```

它真的是透明的，看到了吗？虽然这很神奇，但也很直白。你可以看到函数的定义，你可以看到，在你的脸上，有哪些装饰器正在被执行。

然而，这个实现有一个微妙的问题:

```
print(sum_numbers.__name__) 
```

这将打印出`wrapper`，因为这是装饰器返回的`wrapper`函数的名称。现在，调试的时候，那个东西的屁股会有痛感。但是我们能修好它！我们可以用标准库来修复它！:D

python 中有一个名为`functools`的模块，它提供了很多很棒的功能。我真的鼓励你去看看它提供了什么，但是今天我们只使用它提供的一个装饰器。

装饰器`functools.wraps`接收一个函数作为参数，并用给定函数的属性覆盖包装的函数属性。

让我们用`functools.wraps`来改变我们的装饰器，看看它是如何工作的。

```
from functools import wraps

def a_decorator(wrapped_function):
    @wraps(wrapped_function)
    def wrapper(*args, **kwargs):
        print('I am the decorator!')
        return wrapped_function(*args, **kwargs)
    return wrapper

@a_decorator
def sum_numbers(num_a, num_b):
    return num_a + num_b

print(sum_numbers.__name__) 
```

这将再次打印`sum_numbers`，优雅地修复我们的问题。现在，`wraps`是如何接收参数的？我该怎么做？您将需要一个接收这些参数并返回包装函数的中间函数。

您的装饰器实际上不是一个装饰器，而是一个装饰器*构建器*，它接收几个参数并返回一个装饰器。

```
def a_decorator(name):
    def actual_decorator(wrapped_function):
        @wraps(wrapped_function)
        def wrapper(*args, **kwargs):
            print('I am the decorator, {}!'.format(name))
            return wrapped_function(*args, **kwargs)
        return wrapper
    return actual_decorator

@a_decorator("Sum Numbers")
def sum_numbers(num_a, num_b):
    return num_a + num_b

print(sum_numbers(1, 2)) 
```

## 我们想要解决的实际问题

比方说，我们有一个函数，我们想计时:

```
def fibonacci(n, a = 0, b = 1):
    if n == 0: 
        return a 
    if n == 1: 
        return b 
    return fibonacci(n - 1, b, a + b) 
```

现在，让我们放弃直接向它添加计时功能的想法，因为这会污染它的代码，使它更复杂，只是因为我们想向它添加非关键行为。

因此，让我们编写一个名为`timer`的装饰器，它接收一个很酷的操作名(使它在日志中更易读)并将`@timer("Fibonacci calculator")`添加到原始函数中。

```
from datetime import datetime
from functools import wraps

def timer(operation_name):
        def timer_decorator(wrapped_function):
                @wraps(wrapped_function)
                def wrapper(*args, **kwargs):
                        start = datetime.now()
                        result = wrapped_function(*args, **kwargs)
                        end = datetime.now()
                        print("{} took {}.".format(operation_name, end - start))
                        return result
                return wrapper
        return timer_decorator

@timer("Fibonacci calculator")
def fibonacci(n, a = 0, b = 1):
    if n == 0:
        return a
    if n == 1:
        return b
    return fibonacci(n - 1, b, a + b)

fibonacci(30) 
```

现在，在输出中，我们将看到 fibonacci 的每次执行花费了多少时间，考虑到它是一个递归函数，对于`n=30`它将自己执行 30 次。所以我们会在输出`Fibonacci calculator took <time>`中看到三十次。

这个实验也清楚地表明 Python 不做尾部递归优化，所以要记住这一点:p。

## 结论

Python 使得 decorators 变得相当容易，对于我们在生产环境中都需要的这种特性，它们真的很棒。

缓存、单元测试、路由，这些都是可以使用 decorators 很好地解决的问题，我认为，特别是在 Python 中，它们对初学者来说非常友好。