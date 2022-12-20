# 在 rust 中删除 root 权限

> 原文：<https://dev.to/graysonarts/dropping-root-privileges-in-rust-2bog>

*原贴于[不锈钢. io](https://stainless.io/post/code/rust/drop_root/)T3】*

我在 Rust 写`fingerd`守护进程的时候(为什么？因为我可以)，我花了一点时间才弄明白的一件事是，在我绑定到端口 79 之后，如何放弃 root 特权。

安全性的原则之一是使用最少的特权来完成您需要的功能。对于在特权端口(即端口< 1024), you need to be root long enough to bind the socket to the port, and then you have no need for root. It's called the **上运行的 fingerd 和大多数网络守护进程，最低特权原则**。

在 C 和 C++的世界里，你有标准的 C 库，它提供了`setuid(2)`来改变当前运行进程的`uid`，所以这就是我们需要使用的。但是从可用性的角度来看，我希望用户能够指定运行时的用户名，而`setuid(2)`使用的是`uid`而不是用户名，所以我们也需要使用`getpwnam(3)`来使用用户名的密码条目。

为了实现这一点，我创建了两个函数，然后将它们组合在一起，创建了第三个函数，它从 root 用户到一个指定的用户。一旦你理解了 ffi 的全部魔力，它们就变得相当简单了。这篇博客希望能帮助你避免担心 ffi 的魔力。我不能决定我是否想把它变成一个箱子，但也许可以留下评论让我知道。

首先，让我们从用户名中获取`uid`。

```
use std::io::Result;

fn get_uid(username : &str) -> Result<uid_t> {
    let p : &passwd = unsafe {
        let cstr = CString::new(username).expect("Unable to pass username to underlying C library");
        let p = getpwnam(cstr.as_ptr());
        if p.is_null() {
            return Err(Error::from_raw_os_error(ENOENT));
        }
        &*p
    };

    Ok(p.pw_uid)
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，有一些不安全的代码可以调用`getpwname(3)`。我们将用户名`&str`转换成一个`CString`，这样我们就可以将它传递给`getpwnam(3)`。然后我们调用系统调用，检查以确保它不为空，然后将其作为不安全块外部的引用返回。一旦我们得到了它，那么我们就获得了`pw_uid`,如果我们返回任何东西，它肯定会包含一个值。

```
fn shed(uid : uid_t) -> Result<()> {
    match unsafe { setuid(uid) } {
        0 => Ok(()),
        -1 => Err(Error::last_os_error()),
        n => unreachable!("setuid returned {}", n)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将`setuid(2)`包装在一个不安全的变量中，并将整数返回代码转换成一个`std::io::Result`。

```
pub fn drop_root(username : &str) -> Result<()> {
    get_uid(username).and_then(shed)
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们通过`.and_then`组合子将它们链接在一起，然后使用它，我们做`drop_root("rhay")?`。我在主函数中的`TcpListener::bind(&addr).unwrap();`之后立即这样做。既然`main()`现在可以还一个`Result`，`?`的作用很大。

我希望这篇文章为你节省了一点时间。当我在做这个的时候，我找不到任何在 Rust 中明确调用这个通用安全模式的参考资料。