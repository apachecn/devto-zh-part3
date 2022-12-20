# 探索铁锈

> 原文：<https://dev.to/ivergara/exploring-rust-55gd>

有一段时间，我对 Rust 感兴趣，阅读文章和书籍，并尝试在它上面做一些小练习，但总的来说仍然相当肤浅。现在，在阅读了 Rust *科学计算系列文章的第二部分之后，我决定利用这个例子进行更多的探索并挑战自己。这篇文章很早就提到了以下内容

> 但是，当其他人提出一种新的数字类型，并带有合法的加法和乘法概念(例如复数)时，会发生什么呢？

来激励探索泛型，但是没有探索复数的场景。因此，我想知道如何在更复杂的类型中使用示例函数，即`generic_scalar_product`。例如复数。这意味着将`num_traits::Zero`特征实现到一个实现复数的类型中，因为该函数有这样的特征作为其参数的类型界限。

## 复数

(不)幸运的是，通过使用相关的机箱`num_complex`，这个案例很简单，它确实实现了具有所需特征`num_traits::Zero`的`Complex`类型。因此，从文章中提取代码并利用`num_complex`机箱，我们得到了下面的结果([操场](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=7e7c7e7e7f84b084f1aceb92b7a8757b) )

```
extern crate num_traits;
extern crate num_complex;

use num_complex::Complex64;

fn generic_scalar_product<T>(v: &[T], w: &[T]) -> T
where
    T: num_traits::Zero + std::ops::Mul<Output = T> + Copy
{
    let length = v.len(); 
    assert_eq!(length, w.len()); 

    // We initialise `sum` to a "zero" of type T
    // using the `zero` method provided by the `Zero` trait
    let mut sum = T::zero(); 
    for index in 0..length {
        sum = sum + v[index] * w[index];
    }
    sum
}

fn main() {
    // Complex
    let v: Vec<Complex64> = vec![Complex64 { re: 1.0, im: 0.0 }, Complex64 { re: 1.0, im: 0.0 }, Complex64 { re: 1.0, im: 0.0 }];
    let w: Vec<Complex64> = vec![Complex64 { re: 1.0, im: 0.0 }, Complex64 { re: 1.0, im: 0.0 }, Complex64 { re: 1.0, im: 0.0 }];
    assert_eq!(generic_scalar_product(&v, &w), Complex64 { re: 3.0, im: 0.0 });
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，这不是一个很大的挑战…对于一个 Rust 初学者(像我一样)来说，这仍然是一个很好的锻炼/热身，可以获得一些信心。但那时，我已经承诺要探索更多。这就引出了真正的挑战，如何将这个`generic_scalar_product`用于我们自己制造的类型。我们可以举个例子，四元数或者其他东西。

## (本地)自定义类型

我们可以采取两种途径来开发这个例子:*用 rust 编译器实现我们新类型的所有需要的特征，一部分一部分地进行*结对编程*，直到我们不再有编译错误。*编写已经实现了一般预期特征的类型，比如`Add`和`Mul`，就好像它是我们已经拥有的一段“成熟的”代码，我们希望与`generic_scalar_product`函数一起使用。

我将采取第二种选择，然后着手扩展它，使其与`generic_scalar_product`兼容。

我们的新类型将是`NAlgebra`，它将被实现为两个底层的`f64`类型。这是为了使我们的任务更容易，因为在这一点上不需要关心泛型，但这将是一个很好的下一个练习。

```
use std::ops::Add;
use std::ops::Mul;

#[derive(Debug)] // debug to be allowed to be printed
struct NAlgebra {
    a: f64,
    b: f64,
}

impl NAlgebra {
    fn new(x: f64, y: f64) -> NAlgebra {
        NAlgebra { a: x, b: y }
    }
}

impl Add for NAlgebra {
    type Output = NAlgebra;

    fn add(self, other: NAlgebra) -> NAlgebra {
        NAlgebra {
            a: self.a + other.a,
            b: self.b + other.b,
        }
    }
}

impl Mul for NAlgebra {
    type Output = Self;

    fn mul(self, rhs: Self) -> Self {
        let a = -self.a * rhs.a;
        let b = -self.b * rhs.b;
        NAlgebra::new(a, b)
    }
}

fn main() {
    let v: Vec<NAlgebra> = vec![NAlgebra { a: 1.0, b: 0.0 }, NAlgebra { a: 1.0, b: 0.0 }, NAlgebra { a: 1.0, b: 0.0 }];
    let w: Vec<NAlgebra> = vec![NAlgebra { a: 1.0, b: 0.0 }, NAlgebra { a: 1.0, b: 0.0 }, NAlgebra { a: 1.0, b: 0.0 }];
    assert_eq!(generic_scalar_product(&v, &w), NAlgebra { a: -3.0, b: 0.0 });
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的测试范围内和为了我们的例子的目的，它是有效的。现在有趣的部分开始了。我们需要为我们的`generic_scalar_product`函数实现相关的特征，即`num_traits::Zero`。我们可以查看 [`Zero`特质](https://docs.rs/num-traits/0.2.6/num_traits/identities/trait.Zero.html)的文档，我们看到下面的

```
pub trait Zero: Sized + Add<Self, Output = Self> {
    fn zero() -> Self;
    fn is_zero(&self) -> bool;
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，要实现的方法是`fn zero() -> Self`和`fn is_zero(&self) -> bool`。这可以通过以下方式轻松实现(有关帮助/灵感，请参见`num_complex`机箱中的[代码](https://docs.rs/num-complex/0.1.40/src/num_complex/lib.rs.html#764-774)，以便在`Complex`上实现)

```
// implementing the Zero trait for our custom type
impl num_traits::Zero for NAlgebra {
    fn zero() -> Self {
        NAlgebra::new(Zero::zero(), Zero::zero())
    }
    fn is_zero(&self) -> bool {
        self.a.is_zero() && self.b.is_zero()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们假设`NAlgebra`中的字段`a`和`b`由`num_traits`箱支持，这是因为我们的底层数据是`f64`。因此，整个代码如下所示([操场](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=13523cd71628685ee333627637005c61) )

```
extern crate num_traits;

use num_traits::Zero;  // importing trait Zero into local scope

use std::ops::{Add,Mul};

#[derive(Debug, PartialEq, Copy, Clone)]
struct NAlgebra {
    a: f64,
    b: f64,
}

impl NAlgebra {
    // Another static method, taking two arguments:
    fn new(x: f64, y: f64) -> NAlgebra {
        NAlgebra { a: x, b: y }
    }
}

impl Add for NAlgebra {
    type Output = NAlgebra;

    fn add(self, other: NAlgebra) -> NAlgebra {
        NAlgebra {
            a: self.a + other.a,
            b: self.b + other.b,
        }
    }
}

impl Mul for NAlgebra {
    type Output = Self;

    fn mul(self, rhs: Self) -> Self {
        let a = self.a * rhs.a;
        let b = self.b * rhs.b;
        NAlgebra::new(a, b)
    }
}

impl Zero for NAlgebra {
    fn zero() -> Self {
        NAlgebra::new(Zero::zero(), Zero::zero())
    }
    fn is_zero(&self) -> bool {
        self.a.is_zero() && self.b.is_zero()
    }
}

fn generic_scalar_product<T>(v: &[T], w: &[T]) -> T
where
    T: num_traits::Zero + std::ops::Mul<Output = T> + Copy
{
    let length = v.len(); 
    assert_eq!(length, w.len()); 

    // We initialise `sum` to a "zero" of type T
    // using the `zero` method provided by the `Zero` trait
    let mut sum = T::zero(); 
    for index in 0..length {
        sum = sum + v[index] * w[index];
    }
    sum
}

fn main() {
    // NAlgebra
    let v: Vec<NAlgebra> = vec![NAlgebra { a: 1.0, b: 0.0 }, NAlgebra { a: 1.0, b: 0.0 }, NAlgebra { a: 1.0, b: 0.0 }];
    let w: Vec<NAlgebra> = vec![NAlgebra { a: 1.0, b: 0.0 }, NAlgebra { a: 1.0, b: 0.0 }, NAlgebra { a: 1.0, b: 0.0 }];
    assert_eq!(generic_scalar_product(&v, &w), NAlgebra { a: 3.0, b: 0.0 });
} 
```

Enter fullscreen mode Exit fullscreen mode

除了必须在`NAlgebra`上实现`Zero`特征之外，由于`sum = sum + v[index] * w[index];`行，该结构还需要用`Clone+Copy`注释来扩展 algo。编译器很好地提供了这一点，虽然我理解为什么需要`Copy`，但我不确定为什么必须指定`Copy`和`Clone`。这是我以前在其他例子中也遇到过的事情，应该给予更多的思考。

还有很多方法可以继续研究这个小例子，从中获取更多的学习知识。我会列举几个想到的:1。将`NAlgebra`的定义一般化以支持通用底层类型将是一个有趣的下一步。2.拿一个类似[四元数](https://docs.rs/quaternion/0.3.1/quaternion/)的外置机箱，从外面延伸出来就可以用在`generic_scalar_product`里了。3.改进这个例子的结构，把所有`NAlgebra`的东西放在同一个板条箱里它自己的模块里。4.使用 Rust 的测试功能对`NAlgebra`及其后来添加的行为进行适当的测试。

我认为前两个有一些重叠，而后两个可以一起解决。

所以，继续…探索和发挥这些想法吧！

* * *

## 奖金

在这次旅行的最后，有一件事让我有点困扰。`generic_scalar_product`的返回值是`T`。作为一个例子很好，并且避免给初学者读者增加错误处理的负担，但是我相信尽早展示正确的错误处理是非常重要的。这意味着，函数的返回应该更像是`Result<T,Err>`，用一个有错误的早期返回代替`assert_eq!(length, w.len());`。为了简单起见，返回的错误将只是一个字符串，因此类型将是`Result<T,String>`

很快参加了这最后一个诡辩([操场](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=bc2d0efa591b0a0edd64bba21267a325) )

```
extern crate num_traits;

fn generic_scalar_product<T>(v: &[T], w: &[T]) -> Result<T,String>
where
    T: num_traits::Zero + std::ops::Mul<Output = T> + Copy
{
    let length = v.len(); 

    if length != w.len() {
        return Err("The vectors have different lengths!".to_string());
    }

    // We initialise `sum` to a "zero" of type T
    // using the `zero` method provided by the `Zero` trait
    let mut sum = T::zero(); 
    for index in 0..length {
        sum = sum + v[index] * w[index];
    }
    Ok(sum)
}

fn main() {
    // Unsigned integers
    let x: Vec<u32> = vec![1, 1, 1];
    let y: Vec<u32> = vec![1, 0, 1];
    assert_eq!(generic_scalar_product(&x, &y).unwrap(), 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，结尾的`unwrap()`可能看起来不太理想，但是`main()`函数作为一个测试套件，我们正在手工制作输入。在这种情况下，对我来说，打开结果似乎完全没问题。