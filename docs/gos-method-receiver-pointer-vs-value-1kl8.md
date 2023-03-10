# Go 的方法接收器:指针 vs 值

> 原文：<https://dev.to/chen/gos-method-receiver-pointer-vs-value-1kl8>

# 简介

这个话题很大，网上有很多信息。这个博客尽量保持简短，并且对新手有经验的程序员有用。

来自 Python 的“来来去去”向我介绍了一个新的概念，我不必花心思去想它。Python 是一种按对象引用传递的语言。你不能直接控制它。
这意味着，当你传递一个对象(*Python 中的一切都是对象*)给一个函数，
你传递一个*引用*给对象本身。

我们可以使用`id()`函数看到它，该函数*返回一个对象的* ***身份*** *。*该标识必须是**唯一的**并且在该对象的生命周期内保持不变**。** 

```
>>> def action_with_string(s):
...     print(id(s))
...
>>> v = "sample string"
>>> id(v)
4360321520
>>> action_with_string(v)
4360321520
>>>

# A list example 
>>> ls = list()
>>> def append_list(l):
...     l.append(1)
...
>>> append_list(ls)
>>> ls
[1]
>>> append_list(ls)
>>> ls
[1, 1] 
```

Enter fullscreen mode Exit fullscreen mode

函数内部的对象与调用者对象相同。无论你传递什么，都可以变异(只要是可互换的)。如果你有兴趣进一步阅读，可以在 [StackOverflow: Python 函数引用调用](https://stackoverflow.com/questions/13299427/python-functions-call-by-reference)中讨论。

使用 Go，当你在一个结构上定义一个方法时，你可以选择接收者(*方法执行的对象，Python 中的一种自我)是值类型还是指针类型。*

这到底意味着什么？

简单来说， ***值接收者*** 制作一个类型的副本，并将其传递给函数。函数栈现在*持有一个相等的对象*，但是在内存的不同位置。

***指针接收器*** 将类型的*地址传递给函数。函数堆栈有一个对原始对象的引用*。

一个简单的例子说明了区别。

```
package main

import (
    "fmt"
)

type Bike struct {
    Model string
    Size int
}

func ValueReceiver(b Bike) {
    fmt.Printf("VALUE :: The address of the received bike is: %p\n", &b)
    // Address of the object is different than the ones in main
    // Changing the object inside the scope here won't reflect to the caller
    b.Model = "BMW"
    fmt.Println("Inside ValueReceiver model: ", b.Model)
}

func PointerReceiver(b *Bike) {
    fmt.Printf("POINTER :: The address of the received bike is: %p\n", b)
    // Address of the object is the same as the ones in main
    b.Model = "BMW"
    fmt.Println("Inside PointerReceiver model: ", b.Model)
}

func main() {
    v := Bike{"Honda CBR", 650}
    p := &Bike{"Suzuki V-Storm", 650}

    fmt.Printf("Value object address in main: %p\nPointer object address in main: %p\n\n", &v, p)
    ValueReceiver(v)
    fmt.Println("Value model outside the function: ", v.Model)

    fmt.Println("")
    PointerReceiver(p)
    fmt.Println("Pointer model outside the function: ", p.Model)
}

// OUTPUT
Value object address in main: 0x40a0e0
Pointer object address in main: 0x40a0f0

VALUE :: The address of the received bike is: 0x40a100
Inside ValueReceiver model:  BMW
Value model outside the function:  Honda CBR

POINTER :: The address of the received bike is: 0x40a0f0
Inside PointerReceiver model:  BMW
Pointer model outside the function:  BMW 
```

Enter fullscreen mode Exit fullscreen mode

## 那么什么时候应该用什么呢？

不管您选择什么，保持结构方法的一致性是最佳实践。如果结构同时使用这两种类型，就很难跟踪哪个方法使用哪个类型，尤其是如果您不是编写代码的人。所以尽你最大的努力，他们都使用同一个接收器来保证一致性和 T2。

### 指针

*   如果你想和它的方法共享一个值

如果方法*改变了类型的状态*，你必须使用一个指针，否则它不会像预期的那样工作。值接收方的更改是复制对象的局部更改，并且只存在于函数范围内。

*   如果结构非常大(优化)

具有多个字段的大型结构每次需要传递时，复制它们的成本可能很高。如果你是这种情况，我认为你应该考虑把它分成小块。如果不可能，使用指针。
优化*惯用*增加**复杂度**。在你使用它之前一定要三思。

请注意，*指针*对于并发来说是不安全的；因此你需要自己使用同步机制来处理它，比如*通道，或者使用原子或者同步内置包*。

### 值

*   如果您不想共享一个值，请使用值接收器。

值接收器对于并发访问是**安全的。围棋最大的优势之一是*并发*，所以这是一个巨大的优势。
你永远不知道什么时候会需要它，如果你写了库代码，你可以肯定有人会在某个时候同时使用它。**

# 总结

对于像我这样的新来者来说，这是一个有趣的话题。这是一个新概念，我在开发 Go 之前没有处理过。在通过博客、文档、视频和强大的 Stackoverflow 对这个主题进行研究后，我总结出了一套要记住的“经验法则”:

1.  对所有方法使用相同的接收器类型。这并不总是可行的，但是试着去做。
2.  方法定义了一个类型的*行为*；如果该方法使用*状态* ( *更新/变异*)使用**指针**接收器。
3.  如果一个方法没有突变*状态*，使用**值**接收。
4.  函数对*值*进行操作；函数**不应该**依赖于类型的状态。

如果你想更深入地探索，这里有一个很好的资源列表:

*   [史蒂夫法兰克王国在 YouTube 上发表“围棋中的 7 个常见错误”演讲](https://www.youtube.com/watch?v=29LLRKIL_TI)
*   [Go 代码审查小组的意见](https://github.com/golang/go/wiki/CodeReviewComments#receiver-type)
*   [Gopher vs 面向对象](https://medium.com/gophersland/gopher-vs-object-oriented-golang-4fa62b88c701)
*   [T 和 T*有不同的方法组](https://golang.org/doc/faq#different_method_sets)