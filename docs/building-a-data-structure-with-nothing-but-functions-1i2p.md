# 建立一个只有函数的数据结构

> 原文：<https://dev.to/scotchka/building-a-data-structure-with-nothing-but-functions-1i2p>

能够嵌套函数的一个简单但意义深远的结果是，数据可以存储在内部函数的封闭范围(或闭包)中。考虑以下函数:

```
def make_node(value, next_node):
    return lambda func: func(value, next_node) 
```

当用两个参数调用`make_node`时，它们被附加到返回函数的闭包上。随后，我们可以通过

```
def value(node):
    return node(lambda value, next_node: value) 
```

和

```
def next_node(node):
    return node(lambda value, next_node: next_node) 
```

举个例子，

```
>>> node = make_node("head", "tail")
>>> value(node)
'head'
>>> next_node(node)
'tail' 
```

有了这些函数，很容易将堆栈实现为链表。我们定义了常见的堆栈操作

```
def push(value, stack):
    return make_node(value, stack) 
```

和

```
def pop(stack):
    return value(stack), next_node(stack) 
```

该行为符合预期:

```
>>> stack = None
>>> stack = push(1, stack)
>>> stack = push(2, stack)
>>> stack = push(3, stack)
>>> val, stack = pop(stack)
>>> val
3
>>> val, stack = pop(stack)
>>> val
2
>>> val, stack = pop(stack)
>>> val
1 
```

值得注意的是，这个堆栈没有使用任何内置的数据结构(如数组)，也没有通过实例化节点对象来构建链表。相反，有一个函数链，其中每个函数的闭包都包含一个值和对下一个函数的引用。

这个讨论是受经典的[结构和计算机程序的解释](https://mitpress.mit.edu/sites/default/files/sicp/index.html)的启发。特别是，2.1 节介绍了使用闭包构建数据结构的概念。