# 我喜欢 Rust 中的东西:不变性

> 原文：<https://dev.to/jonstodle/things-i-enjoy-in-rust-immutability-b9g>

**我喜欢 Rust 里的东西:**

1.  [枚举](https://dev.to/jonstodle/things-i-enjoy-in-rust-enums-4cdl)
2.  [错误处理](https://dev.to/jonstodle/things-i-enjoy-in-rust-error-handling-2jkh)
3.  **不变性**
4.  [Impl](https://dev.to/jonstodle/things-i-enjoy-in-rust-impl-1doj)
5.  [宏](https://dev.to/jonstodle/things-i-enjoy-in-rust-macros-21fp)

不变性是函数式编程语言中经常听到的一个术语。在大多数类 C 语言中，不变性通常是事后才想到的，并且通常以一种有点笨拙的方式实现。

在 Rust 中，不变性是默认的。如果您分配了一个新值...

```
let myValue = 42;
myValue = 43; // <-- Not allowed 
```

Enter fullscreen mode Exit fullscreen mode

...它是不可改变的。

要启用不变性，您必须显式地指定它:

```
let mut myValue = 42;
myValue = 43; // <-- Allowed 
```

Enter fullscreen mode Exit fullscreen mode

Rust 还使得处理不可变的结构变得容易。在不可变性不是主要特性的语言中，当处理不可变的类或结构时，这是经常缺少的东西。我们来看一个例子:

```
// Define a structure
struct Point {
    x: i32,
    y: i32,
};

// Create an instance
let point = Point {
    x: 2,
    y: 4,
};

// Change a value inside the structure
let point = Point {
    x: 4,
    ...point,
}; 
```

Enter fullscreen mode Exit fullscreen mode

等等，什么！？

Rust 让我们创建一个与以前的值同名的值。第二个值*遮蔽了第一个定义*，有效地隐藏了它。这使我们能够对同一事物重复使用相同的名称，而不必想出 3 种方法来命名它。

这也使得用一个更新的结构替换一个不可变的结构变得容易，而不需要改变名字:

```
// Instead of this:
let point = Point { x: 2, y: 4 };
let updated_point = Point { x: 4, ...point };

// We do this:
let point = Point { x: 2, y: 4 };
let point = Point { x: 4, ...point }; 
```

Enter fullscreen mode Exit fullscreen mode

Rust 还有一个 spread 操作符，它用现有结构实例中的值填充结构中的其余值。我们在新结构中定义的值具有更高的优先级。结构的新实例中未赋值的任何值都将用第一个结构中的相应值填充。

不变性作为缺省值是我真正喜欢的。