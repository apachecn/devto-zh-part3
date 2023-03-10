# 杀死 unwrap()

> 原文：<https://dev.to/dmerejkowsky/killing-unwrap-2k2e>

*最初发表于[我的博客](https://dmerej.info/blog/post/killing-unwrap/)。*

# 等等，什么？你想杀谁？

在 Rust 中，为了表示错误或缺少值，我们分别使用名为`Result`和`Option`的类型。

如果我们需要一个结果或者选项的*值*，我们可以这样写代码:

```
let maybe_bar = get_bar();
// bar is now an Option<String>
if maybe_bar.is_some() {
  let bar = bar.unwrap();
  // now bar is a String
} else {
  // handle the absence of bar
} 
```

Enter fullscreen mode Exit fullscreen mode

这工作得很好，但是有一个问题:如果在一些重构之后没有调用`if`语句，整个程序将会崩溃。

如果在测试中调用了`unwrap()`,这没问题，但是在生产代码中，最好是完全防止恐慌。

这就是原因。让我们看看怎么做。

# 例 1 -无处理

让我们回到我们的第一个例子:我们假设有一个来自外部机箱的`bar::return_opt()`函数并返回一个`Option<Bar>`，我们在`my_func`中调用它，这个函数也返回一个选项:

```
fn my_func() -> Option<Foo> {
  let opt = bar::return_opt();
  if opt.is_none() {
    return None;
  }
  let value = opt.unwrap();
  ...
  // doing something with `value` here
} 
```

Enter fullscreen mode Exit fullscreen mode

那么我们如何摆脱这里的`unwrap()`？简单，用*问号符* :

```
fn my_func() -> Option<Foo> {
  let value = bar::return_opt()?;
  // Done: the question mark will cause the function to
  // return None automatically if bar::return_opt() is None
  ...

  // We can use `value` here directly!
} 
```

Enter fullscreen mode Exit fullscreen mode

如果`my_func()`没有返回一个`Option`或者一个`Result`你就不能使用这个技术，但是一个`match`可以用来保持“提前返回”的模式:

```
fn my_func() {
  let value = match bar::return_opt() {
      None => return,
      Some(v) => v
  };
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意`match`表达式和`let`语句是如何组合的。好棒的铁锈！

# 例 2 -处理结果

先来看坏代码:

```
fn my_func() -> Result<Foo, MyError> {
  let res = bar::return_res();
  if res.is_err() {
    return Err(MyError::new(res.unwrap_err());
  }
  let value = res.unwrap();
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这里`bar::return_res()`函数返回一个`Result<BarError, Bar>`(其中`BarError`和`Bar`在一个外部机箱中定义)。`MyError`型在当前的板条箱里。

我不知道你怎么想，但我真的很讨厌第四行:`return Err(MyError::new(res.unwrap_err());`好拗口！

让我们看看重写它的一些方法。

## 使用从

一个解决方案是使用问号运算符:

```
fn my_func() -> Result<Foo, Error> {
  let value = bar::return_res()?;
} 
```

Enter fullscreen mode Exit fullscreen mode

代码当然不会编译，但是编译器会[告诉你怎么做](https://dmerej.info/blog/post/letting-the-compiler-tell-you-what-to-do/)，你只需要实现`From`特征:

```
impl From<BarError> for MyError {
    fn from(error: BarError) -> MyError {
        Error::new(&format!("bar error: {}", error))
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这很好，除非您需要添加一些上下文(例如，您可能有一个`IOError`，但没有导致它的文件名)。

## 使用 map_err

下面是`map_err`的动作:

```
fn my_func() -> Result<Foo, Error> {
  let res = bar::return_res():
  let some_context = ....;
  let value = res.map_err(|e| MyError::new(e, some_context))?;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然可以使用问号操作符，难看的`Err(MyError::new(...))`不见了，我们可以在自定义错误类型中提供一些额外的上下文。史诗般的胜利！

# 例 3 -转换为选项

这次我们调用一个返回`Error`的函数，我们想要一个`Option`。

还是那句话，先说“坏”的版本:

```
fn my_func() -> Result<Foo, MyError> {
  let res = bar::return_opt();
  if res.is_none() {
    retrun Err(MyError::new(....));
  }
  let res = res.unwrap();

  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

解决方案是使用`ok_or_else`，有点像我们之前使用`unwrap_err`的方式:

```
fn my_func() -> Result<Foo, MyError> {
  let value = bar::return_opt().ok_or((MyError::new(...))?;
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

# 例 4 -断言

有时，您可能希望捕捉代码中逻辑错误导致的错误。

例如:

```
let mystring = format!("{}: {}", spam, eggs);
// ... some code here
let index = mystring.find(':').unwrap(); 
```

Enter fullscreen mode Exit fullscreen mode

我们用`format()`构建了一个不可变的字符串，并在格式字符串中放了一个冒号。字符串不可能在最后一行的*和*中包含冒号，所以我们*知道*和`find`将返回一些东西。

我认为我们还是应该删除这里的`unwrap()`，用`expect()` :
让错误信息更清晰

```
let index = mystring.find(':').expect("my_string should contain a colon"); 
```

Enter fullscreen mode Exit fullscreen mode

# 在测试和主

*注:感谢 Jeikabu 对的[评论触发了本节](https://dev.to/jeikabu/comment/8kb4)*的添加:

我们再举一个例子。下面是测试中的代码:

```
struct Foo { ... };

fn setup_foo() -> Result<Foo, Error> {
    ...
}

fn frob_foo(foo: Foo) -> Result<(), Error> {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

传统上，你必须这样为`setup_foo()`和`frob_foo()`编写测试:

```
#[test]
fn test_foo {
  let foo = setup_foo().unwrap();
  frob_foo(foo).unwrap();
} 
```

Enter fullscreen mode Exit fullscreen mode

但是自从最近版本的 Rust 你可以写同样的测试这个 way[^1]:

```
#[test]
fn test_foo -> Result<(), MyError> {
  let foo = setup_foo()?;
  frob_foo(foo)
} 
```

Enter fullscreen mode Exit fullscreen mode

易读性的又一大胜利，你不同意吗？

顺便说一下，同样的技术可以用于`main()`函数(Rust 可执行文件的入口点):

```
// Old version
fn main() {
   let result = setup_foo().unwrap();
   ...
}

// New version:
fn main() -> Result<(), MyError> {
   let foo = setup_foo()?;
   ...
} 
```

Enter fullscreen mode Exit fullscreen mode

# 关闭思绪

当在你自己的代码中使用`Option`或`Result`类型时，花些时间阅读(和重读)文档。Rust 标准库为您提供了许多方法来解决手头的任务，有时您会找到一个完全符合您需要的函数，从而得到更短、更干净、更习惯的 Rust 代码。

如果你有更好的解决方案或其他例子，请告诉我！直到那时，快乐生锈:)