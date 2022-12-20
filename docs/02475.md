# 如何在避免冲突的同时使用多个 Trait 实现

> 原文：<https://dev.to/kdrakon/how-to-use-more-than-one-trait-implementation-while-avoiding-conflicts-epk>

# 问题

假设您最初有这样的代码:

```
struct DeserializeError(String);
trait Deserialize<T> {
    fn into_type(self) -> Result<T, DeserializeError>
}

struct Foo {}
impl Deserialize<Foo> for Vec<u8> {
    fn into_type(self) -> Result<Foo, DeserializeError> {
        unimplemented!() // Vec<u8> -> Foo
    }
}

struct Bar {}
impl Deserialize<Bar> for Vec<u8> {
    fn into_type(self) -> Result<Bar, DeserializeError> {
        unimplemented!() // Vec<u8> -> Bar
    }
} 
```

稍后，您需要编写一个通用的`Deserialize`实现，它需要使用`Deserialize<Foo>`、`Deserialize<Bar>`，以及您的代码库中存在的任何其他实现。

我遇到的问题是在我最初尝试做以下事情时:

```
struct Wrapper<T> { foo: Foo, t: T }

// first 4 bytes are Foo, the rest will make up T
impl<T> Deserialize<Wrapper<T>> for Vec<u8> {
    fn into_type(self) -> Result<Wrapper<T>, DeserializeError> {
        self[0..=3].to_vec().into_type().and_then(|foo: Foo| {
            self[4..].to_vec().into_type().map(|some_t: T| {
                Wrapper { foo, t: some_t }
            })
        })
    }
} 
```

不幸的是，您会得到以下错误:

```
|     self[4..].to_vec().into_type().map(|some_t: T| {
|                                    ^^^^^^^^^^^^^^^^^^ the trait `Deserialize<T>` 
|     is not implemented for `std::vec::Vec<u8>` 
```

编译器很好地提出了以下建议:

```
consider adding a `where std::vec::Vec<u8>: Deserialize<T>` bound 
```

好，让我们试试:

```
impl<T> Deserialize<Wrapper<T>> for Vec<u8>
    where Vec<u8>: Deserialize<T> {
    fn into_type(self) -> Result<Wrapper<T>, DeserializeError> {
        self[0..=3].to_vec().into_type().and_then(|foo: Foo| {
            self[4..].to_vec().into_type().map(|some_t: T| {
                Wrapper { foo, t: some_t }
            })
        })
    }
} 
```

但是现在我们得到了下面的编译错误:

```
 = note: expected type `Foo`
              found type `T` 
```

为什么？因为`where`绑定已经声明了该范围内的`Vec<u8>`必须实现`Deserialize<T>`，并且根据我的最佳猜测，也已经明确地将该实现强制到范围内。所以我的问题是，虽然我没有冲突的特征`Vec<u8>`的*实现*，但是我有冲突的特征*使用*。

# 解

我花了一段时间才找到这个问题的答案，但答案就在《Rust 编程语言》的第二版中。在[高级特征](https://doc.rust-lang.org/book/ch19-03-advanced-traits.html)部分，关键是子部分“*完全限定的消歧语法:调用同名方法*”。

虽然这一节是关于明确说明使用同名的多个特征中的哪一个方法(冲突的方法名，而不是特征名)，但它在这里确实做到了。

```
impl<T> Deserialize<Response<T>> for Vec<u8>
    where Vec<u8>: Deserialize<T> {
    fn into_type(self) -> Result<Wrapper<T>, DeserializeError> {
        Deserialize::<Foo>::into_type(self[0..=3].to_vec()).and_then(|foo: Foo| {
            Deserialize::<T>::into_type(self[4..].to_vec()).map(|some_t: T| {
                Wrapper { foo, t: some_t }
            })
        })
    }
} 
```

我仍然保留了`where`绑定，这样实现就在编译器的范围内，但是通过使用完全限定的语法——包括泛型类型——我告诉了编译器我实际上想要使用哪个实现。当然，它可以编译。从 Scala 开发人员的角度来看，这有点像暂时跳过隐式类转换，手动调用您想要的实现。

*原贴为[要诀](https://gist.github.com/kdrakon/ff44b55ea0baa012382b9590362d5fef)。*