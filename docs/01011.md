# 在 Python 中使用装饰器

> 原文：<https://dev.to/abe21412/working-with-decorators-in-python-559m>

在我的上一篇[帖子](https://dev.to/abe21412/understanding-functional-closures-4la8)中，我写了 Python 中的函数闭包，我提到关于它们最有趣的事情是它们可以用来创建装饰器。他们就是这样做的，他们用额外的功能来修饰现有的功能，而且他们也是可重用的，所以他们可以同时应用于多个不同的功能。

## **装修工定义的**

装饰器本质上是一个高阶函数，它既接受函数作为参数，也返回函数。结果是一个函数，其功能与您传递给装饰器的功能相同，并带有您在装饰器主体中定义的任何额外功能。这可以通过一个代码示例得到最好的解释:

```
def example_func():
    print("I'm a function!")

def decorator(func):
    def inner():
        func()
        print("I've been decorated!")
    return inner 
```

现在我们可以说:

```
decorated_func = decorator(example_func)
decorated_func() 
```

现在，如果我们运行程序，输出将是:

```
I'm a function!
I've been decorated! 
```

装饰者实际上有一个很酷的语法糖，而不是输入这个:

```
decorated_func = decorator(example_func)
decorated_func() 
```

我们可以在修饰函数的定义上添加`@decorator`，就像这样:

```
@decorator
def example_func():
    print("I'm a function!") 
```

现在如果你调用`example_func()`，输出将和之前一样。

## 修饰带参数的函数

装饰器也可以应用于接受参数的函数，装饰器实际上可以访问传递给被装饰函数的任何参数。

取此功能:

```
def capitalize_first_name(func):
    def wrapper(first_name,last_name):
        capitalized = first_name.upper()
        func(capitalized,last_name)
    return wrapper

@capitalize_first_name
def print_name(first_name,last_name):
    print(f"Hi, my name is {first_name}  {last_name}") 
```

现在，例如，如果我们说`print_name("John", "Doe")`，
，输出将是`Hi, my name is JOHN Doe!`，而我们不必改变原始函数。

你不需要总是有相同数量的参数来让装饰器工作；您可以将`*args`操作符传递到包装器中，并且能够处理任何带有任意数量参数的函数！

## 结论

在我看来，装饰者是 Python 提供的最酷的特性之一。你并不总是有一个很好的理由去使用它们，除非你真的不得不这样做，否则你也不应该这样做，但是当情况需要它们的时候，它们会非常有用。Decorators 在 Flask 和 Django 等 web 框架中被大量使用，用来添加 HTTP 路由功能、身份验证等。