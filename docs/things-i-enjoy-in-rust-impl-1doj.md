# 我在 Rust 中喜欢的东西:Impl

> 原文：<https://dev.to/jonstodle/things-i-enjoy-in-rust-impl-1doj>

**我喜欢 Rust 里的东西:**

1.  [枚举](https://dev.to/jonstodle/things-i-enjoy-in-rust-enums-4cdl)
2.  [错误处理](https://dev.to/jonstodle/things-i-enjoy-in-rust-error-handling-2jkh)
3.  [不变性](https://dev.to/jonstodle/things-i-enjoy-in-rust-immutability-b9g)
4.  **Impl**
5.  [宏](https://dev.to/jonstodle/things-i-enjoy-in-rust-macros-21fp)

Rust 没有类和方法。相反，它使用结构和关联函数。要将一个函数与一个结构相关联，你可以使用`impl`关键字:

```
struct Point {
    x: isize,
    y: isize,
}

impl Point {
    fn new(x: isize, y: isize) -> Point {
        Point { x, y }
    }

    fn move_laterally(&mut self, amount: isize) {
        self.x + amount;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这两个功能是为`Point`实现的，现在它们被认为是`Point`的关联功能。

第一个函数`new`，通过获取一个`x`和一个`y`坐标来创建一个`Point`的新实例。这个函数很像其他语言中的`static`方法或类函数。你这样用:

```
let point = Point::new(0, 0); 
```

Enter fullscreen mode Exit fullscreen mode

第二个函数`move_laterally`引用了`self`，这意味着它可以用作`Point`实例上的方法。这很像其他语言中的实例方法:

```
let mut point = Point::new(0, 0);
point.move_laterally(10); 
```

Enter fullscreen mode Exit fullscreen mode

我真正喜欢`impl`的一点是，任何人都可以为一个结构实现 function 即使是没有在同一个项目中定义的结构。想做一个 helper 函数来反转字符串？

```
impl String {
    fn reverse(&self) -> String {
        self.chars().into_iter()
            .rev()
            .collect()
    }
}

let text: String = "Allonsy".to_string();
let reverse = text.reverse(); // "ysnollA" 
```

Enter fullscreen mode Exit fullscreen mode

`String`是在标准库中定义的，但是你仍然可以用你自己的功能扩展它。整洁！

您甚至可以为项目中未定义的结构实现*特征* (Rust 几乎相当于 C#、Java 或 Typescript 等语言中的*接口*)。更整洁！

我喜欢这种灵活性。