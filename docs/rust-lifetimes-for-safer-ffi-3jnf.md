# 更安全的 FFI 防锈寿命

> 原文：<https://dev.to/jeikabu/rust-lifetimes-for-safer-ffi-3jnf>

寿命是 Rust 的招牌特性之一，也是其安全保障的关键。我对它们的理解很大程度上是学术性的，直到我发现了一种将 FFI 转换为本机代码的情况，这种情况需要进一步研究。

## 寿命 101

如果你不熟悉生锈寿命，这里是基本概念；它们帮助编译器确保引用不会比它们所引用的对象存在更久:

```
// Type contains a reference to an integer with a lifetime of 'a
struct MyStruct<'a> (&'a i32);

 #[test]
 fn lifetime() {
     let inner = 42;
     // Create an instance of MyStruct with a reference to `inner` and a lifetime of 'a
     let outer = MyStruct(&inner);
 } 
```

Enter fullscreen mode Exit fullscreen mode

这里`outer`不应该比`inner`活得长，否则你会有一个“悬空引用”——一个指向不再存在的东西的指针。在有垃圾收集器的语言中，这通常不是问题；`inner`可以根据需要在堆中保留多长时间。在像 C/C++这样的语言中，必须注意(在编译器的帮助下)避免像返回一个指向堆栈的引用/指针、“释放后使用”以及其他错误这样的问题。

## 用例

我一直在为一个 C 库做 Rust 包装器， [nng](https://github.com/nanomsg/nng) 。几个月前，我还在争论运行时统计数据。

[`nng_stats_get()`](https://nanomsg.github.io/nng/man/v1.1.0/nng_stats_get.3) 返回运行时统计数据的快照，作为一棵树，可以用 [`nng_stat_child()`](https://nanomsg.github.io/nng/man/v1.1.0/nng_stat_child.3) 和 [`nng_stat_next()`](https://nanomsg.github.io/nng/man/v1.1.0/nng_stat_next.3) 遍历。不再需要时， [`nng_stats_free()`](https://nanomsg.github.io/nng/man/v1.1.0/nng_stats_free.3) 释放与快照关联的内存，使整个树失效。

一个简单的铁锈包装:

```
// Root of statistics tree
pub struct NngStatRoot {
    node: *mut nng_stat,
}

impl NngStatRoot {
    // Create a snapshot of statistics
    pub fn create() -> Option<NngStatRoot> {
        unsafe {
            // Get snapshot as pointer to memory allocated by C library
            let mut node: *mut nng_stat = std::ptr::null_mut();
            let res = nng_stats_get(&mut node);
            if res == 0 {
                Some(NngStatRoot { node })
            } else {
                None
            }
        }
    }
    // Get first "child" node of tree
    pub fn child(&self) -> Option<NngStatChild> {
        unsafe {
            let node = nng_stat_child(self.node);
            NngStatChild::new(node)
        }
    }
}

// When root goes out of scope free the memory
impl Drop for NngStatRoot {
    fn drop(&mut self) {
        unsafe {
            nng_stats_free(self.node)
        }
    }
}

// A "child"; any non-root node of tree
pub struct NngStatChild {
    node: *mut nng_stat,
}

impl NngStatChild {
    // Create a child
    pub fn new(node: *mut nng_stat) -> Option<NngStatChild> {
        if node.is_null() {
            None
        } else {
            Some(NngStatChild { node })
        }
    }
    // Get sibling of this node
    pub fn next(&self) -> Option<NngStatChild> {
        unsafe {
            let node = nng_stat_next(self.node);
            NngStatChild::new(node)
        }
    }
    // Get first "child" of this node
    pub fn child(&self) -> Option<NngStatChild> {
        unsafe {
            let node = nng_stat_child(self.node);
            NngStatChild::new(node)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这可以如下使用:

```
fn stats() {
    let root = NngStatRoot::create().unwrap();
    if let Some(child) = root.child() {
        if let Some(sibling) = child.next() {
            // Do something
        }
    }
} // root dropped here calling nng_stats_free() 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，下面的代码也“有效”(它可以编译，也可能运行)，但是会导致“未定义的行为”:

```
fn naughty_code() {
    let mut naughty_child: Option<_> = None;
    {
        let root = NngStatRoot::create().unwrap();
        naughty_child = root.child();
    } // root dropped here calling nng_stats_free()

    if let Some(child) = naughty_child {
        if let Some(naughty_sibling) = child.next() {
            debug!("Oh no!");
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

问题是`naughty_child`允许指向统计快照的指针比`root`活得长，并且在`nng_stats_free()`被调用后被访问。

## 解决使用寿命

我很确定这是一辈子的工作。

一旦你给了一个结构体一个生命周期，它就会“感染”它所接触的一切

```
pub struct NngStatChild<'root> {
    node: *mut nng_stat,
}

impl<'root> NngStatChild<'root> {
    pub fn new(node: *mut nng_stat) -> Option<NngStatChild<'root>> {
        //...
    }
//... 
```

Enter fullscreen mode Exit fullscreen mode

特别要注意`impl<'root>`。没有那个你得到:

```
error[E0261]: use of undeclared lifetime name `'root`
  --> runng/tests/tests/stream_tests.rs:77:18
   |
77 | impl NngStatChild<'root> {
   | ^^^^^ undeclared lifetime 
```

Enter fullscreen mode Exit fullscreen mode

将生命应用到任何地方后，你最终会得到:

```
error[E0392]: parameter `'root` is never used
  --> runng/tests/tests/stream_tests.rs:73:24
   |
73 | pub struct NngStatChild<'root> {
   | ^^^^^ unused type parameter
   |
   = help: consider removing `'root` or using a marker such as `std::marker::PhantomData` 
```

Enter fullscreen mode Exit fullscreen mode

寿命`'root`未使用。它不能应用于指针:

```
pub struct NngStatChild<'root> {
    // NB: doesn't compile
    node: *'root mut nng_stat,
} 
```

Enter fullscreen mode Exit fullscreen mode

生存期不上指针，只上引用(`&` ):

```
pub struct NngStatChild<'root> {
    node: &'root mut nng_stat,
} 
```

Enter fullscreen mode Exit fullscreen mode

切换到参考有两个问题:

1.  需要**多次**的强制转换，因为本地方法采用指针(`*`)
2.  需要对结构实例上的`mut`更加小心

这个有用的编译器消息暗示了另一个解决方案， [`std::marker::PhantomData`](https://doc.rust-lang.org/std/marker/struct.PhantomData.html) ，它允许我们的结构“表现得像”它拥有一个引用:

```
pub struct NngStatChild<'root> {
    node: *mut nng_stat,
    _phantom: marker::PhantomData<&'root nng_stat>,
}

impl<'root> NngStatChild<'root> {
    pub fn new(node: *mut nng_stat) -> Option<NngStatChild<'root>> {
        if node.is_null() {
            None
        } else {
            Some(NngStatChild {
                node,
                // Initialize the phantom
                _phantom: marker::PhantomData,
            })
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

酷的是`PhantomData`是一款[零码型](https://doc.rust-lang.org/nomicon/exotic-sizes.html#zero-sized-types-zsts)；它没有运行时成本(既不是 CPU 也不是内存)，它只存在于编译时:

```
pub struct Phantom<'root> {
    _phantom: marker::PhantomData<&'root nng_stat>,
}

#[test]
fn check_size() {
    assert_eq!(0, std::mem::size_of::<Phantom>());
} 
```

Enter fullscreen mode Exit fullscreen mode

一个需要特别注意的地方是我们的`next()`方法:

```
impl<'root> NngStatChild<'root> {
    //...

    // NB: The explicit lifetime on the return value is key!
    pub fn next(&self) -> Option<NngStatChild<'root>> {
        unsafe {
            let node = nng_stat_next(self.node);
            NngStatChild::new(node)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们需要一个显式的生存期，因为没有它，[生存期椭圆](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html#lifetime-elision)规则将分配与`&self`相同的生存期。这意味着兄弟姐妹的寿命在某种程度上是相关的，但重要的是根的寿命。

让我们重温一下我们的顽皮代码:

```
fn naughty_code() {
    let mut naughty_child: Option<_> = None;
    {
        let root = NngStatRoot::create().unwrap();
        naughty_child = root.child();
    } // root dropped here calling nng_stats_free()

    if let Some(child) = naughty_child {
        if let Some(naughty_sibling) = child.next() {
            debug!("Oh no!");
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们构建它时，编译器会让我们知道我们做错了:

```
error[E0597]: `root` does not live long enough
  --> runng/tests/tests/stats_tests.rs:37:25
   |
37 | naughty_child = root.child();
   | ^^^^ borrowed value does not live long enough
38 | } // root dropped here calling nng_stats_free()
   | - `root` dropped here while still borrowed
39 | 
40 | if let Some(child) = naughty_child {
   | ------------- borrow later used here 
```

Enter fullscreen mode Exit fullscreen mode

危机解除，感谢编译器！

## 鳍

如果你读到这里，Rust 可能是你喜欢的，如果你还没有看的话，你应该看一看。

还有一些事情我不清楚。例如，我不确定我是否理解为什么`next()`需要显式的生存期(这在实现迭代器时确实是个问题)，而`child()`不需要。

完整源代码在 [github](https://github.com/jeikabu/runng/blob/master/runng/src/stats.rs) 上。

延伸阅读:

*   Rust 编程语言(本书)用了两章来讲述生命周期:
    *   第 10 章“用生存期验证引用”
    *   [第十九章](https://doc.rust-lang.org/book/ch19-02-advanced-lifetimes.html)“高级生命周期”
*   Rustonomicon 有一个很棒的关于 PhantomData 的[部分，我希望在我开始工作之前就能找到它](https://doc.rust-lang.org/nomicon/phantom-data.html)