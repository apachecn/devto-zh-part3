# 向量:Rust 中的线性容器

> 原文：<https://dev.to/dandyvica/lists-arrays-vectors-linear-containers-in-python-ruby-and-rust-part-3-19jc>

在本文中，我将重点讨论 Rust。我将使用截至今天的最新编译器版本:

```
rustc 1.34.1 (fc50f328b 2019-04-24) 
```

Enter fullscreen mode Exit fullscreen mode

我将只介绍 Rust 的一些基本或更高级的特性，但不会像 trait 对象那样高级。你可以在这里找到我以前关于 Python 和 Ruby 的文章:

*   [Python 列表](https://dev.to/dandyvica/lists-arrays-vectors-linear-containers-in-python-ruby-and-rust-17mn)
*   [红宝石数组](https://dev.to/dandyvica/lists-arrays-vectors-linear-containers-in-python-ruby-and-rust-17kb)

Rust 向量可以包含任意数量的相同类型的元素(取决于内存)。Rust 中的向量是堆分配的。

在声明包含向量的变量时，可以使用类型推断或精确元素的类型:

```
// Vec<T> is a built-in type. No need to import
// uninitialized vector of unsigned 64-bit integers
let v1: Vec<u64>;      

// initialized empty vector of unsigned 64-bit integers
let v2: Vec<u64> = Vec::new();  

// initialized vector of unsigned 64-bit integers. Uses the vec! built-in macro. Note the mut modifier because I'll that vector later on. Otherwise, variable is immutable.
let mut digits = vec![0u64,1,2,3,4,5,6,7,8,9];  

// initialized vector of 10 unsigned 64-bit integers equal to 1
let all_1 = vec![1u64;10]; 
```

Enter fullscreen mode Exit fullscreen mode

当然，你可以创建一个向量的向量:

```
let binomial_coefficients = vec![
    vec![1u16],
    vec![1u16,1],
    vec![1u16,2,1],
    vec![1u16,3,3,1],
    vec![1u16,4,6,4,1],
    vec![1u16,5,10,10,5,1]
]; 
```

Enter fullscreen mode Exit fullscreen mode

向量的元素个数由 *len()* 方法:
给出

```
// use of the assert_eq! macro which fails in case of error
assert_eq!(digits.len(), 10);
assert_eq!(binomial_coefficients.len(), 6); 
```

Enter fullscreen mode Exit fullscreen mode

您可以将函数存储在一个向量中:

```
// in that case, you need to provide the type of vector elements
let mut trigo: Vec<fn(f64) -> f64> = vec![f64::sin, f64::cos, f64::tan];

// or store closures (a.k.a. lambdas)
let powers: Vec<fn(u64) -> u64> = vec![
    |x: u64| x.pow(2),
    |x: u64| x.pow(3),
    |x: u64| x.pow(4),
]; 
```

Enter fullscreen mode Exit fullscreen mode

## 访问元素

访问向量元素和往常一样:

```
// get first element reference
let first_binomial = &binomial_coefficients[0];

// need to clone if you really want a copy
let first_binomial_cloned = binomial_coefficients[0].clone();

// no negative indexes but you can implement the Index/IndexMut traits for your structs
use std::ops::Index;

struct BinomialCoefficient {
    coeff: Vec<Vec<u16>>,
}

impl Index<isize> for BinomialCoefficient {
    type Output = Vec<u16>;

    fn index(&self, i: isize) -> &Vec<u16> {
        if i >= 0 {
            &self.coeff[i as usize]            
        }
        else {
            &self.coeff[self.coeff.len()-i.abs() as usize]
        }

    }
}

// Use clone to re-use the binomial_coefficients variable afterwards, otherwise it's moved and gone. 
let my_binomials = BinomialCoefficient { coeff: binomial_coefficients.clone() };
assert_eq!(my_binomials[-1], vec![1u16,5,10,10,5,1]); 
```

Enter fullscreen mode Exit fullscreen mode

子向量可能使用索引范围:

```
let first3_binomials = &binomial_coefficients[0..3];
assert_eq!(first3_binomials.len(), 3);
let first4_binomials = &binomial_coefficients[0..=3];
assert_eq!(first4_binomials.len(), 4); 
```

Enter fullscreen mode Exit fullscreen mode

注意打开范围`..`和关闭范围`..=`之间的差异。

## 矢量运算

*   添加元素

```
digits.push(10);
assert_eq!(digits, vec![0,1,2,3,4,5,6,7,8,9,10]); 
```

Enter fullscreen mode Exit fullscreen mode

*   通过索引删除元素

```
digits.remove(10);
assert_eq!(digits, vec![0,1,2,3,4,5,6,7,8,9]); 
```

Enter fullscreen mode Exit fullscreen mode

*   连接载体

```
digits = vec![0,1,2,3,4];
digits.append(&mut vec![5,6]);
digits.append(&mut vec![7,8,9]);
assert_eq!(digits, vec![0,1,2,3,4,5,6,7,8,9]); 
```

Enter fullscreen mode Exit fullscreen mode

*   测试元素成员资格

```
// need to use the reference on element (&)
if digits.contains(&9) {
    println!("9 is a digit ! Such a surprise ;-)");
} 
```

Enter fullscreen mode Exit fullscreen mode

## 循环遍历一个矢量

使用 for-in 构造，但这取决于您稍后想要如何使用 vector:

```
// use reference: digits is borrowed (in the ownership sense of Rust) and
// so is usable afterwards
for d in &digits {
    println!("{}", d);
}

// use mutable reference: allows to modify elements
for d in &mut digits {
    *d += 1u64;
    println!("{}", d);
}

// beware: digits elements are modified due to the previous loop
assert_eq!(digits, vec![1,2,3,4,5,6,7,8,9,10]);

//  digits ownership is moved due to the underlying construct implementation. digits is gone
for d in digits {
    println!("{}", d);
} 
```

Enter fullscreen mode Exit fullscreen mode

但是一种更面向函数的方式是使用 *for_each* 方法:

```
// reset digits
digits = vec![0u64,1,2,3,4,5,6,7,8,9];

// need to add the iter() because digits is not an iterator 
// (i.e. doesn't implement the Iterator trait )
digits.iter().for_each( |d| println!("{}", d) ); 
```

Enter fullscreen mode Exit fullscreen mode

要在循环时获取元素索引，请使用 *enumerate()* 方法:

```
// enumerate() yields a tuple
digits.iter().enumerate().for_each( |(i,d)| 
    println!("{} is the {}-th digit", d, i)
); 
```

Enter fullscreen mode Exit fullscreen mode

# 更高级的用法

## 向量上的一些有用函数

```
// note the type specification (<u64>) for hint the compiler about type
assert_eq!(digits.iter().sum::<u64>(), 45);

// min & max return an Option<>, need to unwrap()
assert_eq!(digits.iter().max().unwrap(), &9);
assert_eq!(digits.iter().min().unwrap(), &0);
assert_eq!(Vec::<u64>::new().iter().min(), None);

// build lipsum vector using split() and collect()
// beware: str references are returned
let lipsum: Vec<&str> = "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.".split(" ").collect();
assert_eq!(lipsum.iter().max_by_key(|w| w.len()).unwrap(), &"consectetur"); 
```

Enter fullscreen mode Exit fullscreen mode

*zip()* 内置的 vector 方法将几个 vector 组合起来创建一个结果 vector，这个结果 vector 是通过取每个源 vector 的第 I 个元素创建的:

```
let a = vec![0,1,2,3];
let b = vec![4,5,6,7];

// collect() is used to create a vector from the iterator
let zipped: Vec<_> = a.iter().zip(b.iter()).collect();
assert_eq!(zipped, vec![(&0,&4), (&1,&5), (&2,&6), (&3,&7)]); 
```

Enter fullscreen mode Exit fullscreen mode

## 没有矢量理解

和 Ruby 一样，Rust 也没有 Python 中的列表理解那样简洁的语法。
但与 rust 类似，像 *map()* 或 *filter()* 这样的高阶函数也来帮忙了。这样可以达到同样的效果:

```
// extract words ending with 't'
let end_with_t: Vec<_> = lipsum.iter().filter( |w| w.ends_with("t") ).collect();
assert_eq!(end_with_t, vec![&"sit", &"incididunt", &"ut", &"et"]);

// convert to uppercase
let upper: Vec<_> = lipsum.iter().map( |w| w.to_uppercase() ).collect();
assert_eq!(upper[0], "LOREM");

// get only words of length 5 (including commas)
let words5: Vec<_> = lipsum.iter().filter( |w| w.len() == 5 ).collect();
assert_eq!(words5, vec![&"Lorem", &"ipsum", &"dolor", &"amet,", &"elit,", &"magna"]);

// trigo is already declared earlier...
trigo = vec![f64::sin, f64::cos, f64::tan];

let values: Vec<_> = trigo.iter().map( |f| f(std::f64::consts::PI/4f64)).collect();
// cannot check exact equality for floats. Uses this trick
assert!(f64::abs(values[0] - f64::sqrt(2f64)/2f64) < 10E-6f64); 
```

Enter fullscreen mode Exit fullscreen mode

## 在可迭代对象上使用 *collect()* 方法

类似于 Python 中内置的 *list()* 函数或者 Ruby 中的 *to_a* ，Rust 附带了 *collect()* 函数，该函数从一个 iterable:
创建一个向量

```
// this creates a vector of a-z chars
let mut a_to_z: Vec<_> = "abcdefghijklmnopqrstuvwxyz".chars().collect();
assert_eq!(a_to_z.len(), 26);

// create digits and the first 100 even numbers. Note the closed range 0..=9 notation. No need to use *iter()* because a range is implements the Iterator trait
digits = (0..=9).collect();
digits = vec![0,1,2,3,4,5,6,7,8,9];

let even: Vec<_> = (0..100).step_by(2).collect();
assert_eq!(even.last().unwrap(), &98); 
```

Enter fullscreen mode Exit fullscreen mode

这也适用于用户定义的迭代器:

```
// Keep track of n-1 and n values
struct Fibonacci {
    fib_n_1: u64,
    fib_n: u64,
}

// Fibonacci sequence is well known
impl Fibonacci {
    fn new() -> Fibonacci {
        Fibonacci {
            // use of max_value() to handle fib_0 and fib_1
            fib_n_1: u64::max_value(),
            fib_n: u64::max_value(),
        }
    }
}

// only implements Iterator and not IntoIterator
impl Iterator for Fibonacci {
    type Item = u64;

    fn next(&mut self) -> Option<Self::Item> {
        let next_fib: u64;

        // also handle fib_0
        if self.fib_n_1 == u64::max_value() {
            next_fib = 0;

            self.fib_n_1 = 1;
        }
        // handle fib_1
        else if self.fib_n == u64::max_value() {
            next_fib = 1;    

            self.fib_n_1 = 0;
            self.fib_n = 1;            
        }
        else {
            // Fibonacci sequence is well known
            next_fib = self.fib_n + self.fib_n_1;

            self.fib_n_1 = self.fib_n;
            self.fib_n = next_fib;
        }

        Some(next_fib)
    }
}

let fibo = Fibonacci::new();

// use take() adapter because the iterator is infinite
let first_values: Vec<_> = fibo.take(11).collect();
assert_eq!(first_values, vec![0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55]); 
```

Enter fullscreen mode Exit fullscreen mode

# 作用于矢量

使用函数式编程内置函数，您可以从一个向量中提取值，或者从源向量中获取另一个向量。

### *【地图()*或*收藏()*

使用 *map()* 内置函数，可以获得向量上的映射图像。如果你把一个矢量看作一组数学元素，*map()通过所考虑的函数给出图像集。* 

```
a_to_z = "abcdefghijklmnopqrstuvwxyz".chars().collect();
let A_to_Z: Vec<_> = a_to_z.iter().map( |c| c.to_uppercase() ).collect();

// map() uses a block which can be more advanced
let greek = vec!['α', 'β', 'γ', 'δ', 'ε', 'ζ', 'η', 'θ', 'ι', 'κ', 'λ', 'μ', 'ν', 'ξ', 'ο', 'π', 'ρ', 'σ', 'τ', 'υ', 'φ', 'χ', 'ψ', 'ω'];
let translated: Vec<_> = greek.iter().map( |g| 
    match g {
        'α' => 'A',
        'β' => 'B',
        'γ' => 'C',
        // and so on
        _ => 'X',
    }
).collect();
assert_eq!(&translated[0..4], &['A', 'B', 'C', 'X']); 
```

Enter fullscreen mode Exit fullscreen mode

当然，作为第一个参数传递的 map 函数可以是任何函数，任何有一个参数的闭包都是可能的:

```
digits = (0..=9).collect();
let tenths: Vec<_> = digits.iter().map( |x| x*10 ).collect();
assert_eq!(tenths, vec![0, 10, 20, 30, 40, 50, 60, 70, 80, 90]); 
```

Enter fullscreen mode Exit fullscreen mode

甚至是用户自定义函数:

```
// contrived example
fn square(x: u64) -> u64 {
    x*x
}

// calculate the first 9 perfect squares
let squares: Vec<_> = digits.iter().map( |x| square(*x) ).collect();
assert_eq!(squares, vec![0, 1, 4, 9, 16, 25, 36, 49, 64, 81]); 
```

Enter fullscreen mode Exit fullscreen mode

*   *过滤器()*

这个内置函数用于使用一些标准从向量中筛选元素。保留的元素是作为参数提供给 *filter()* 的函数返回 *true* 的元素。

```
// extract even numbers
let even: Vec<_> = digits.iter().filter( |n| *n%2 == 0 ).collect();
assert_eq!(even, vec![&0, &2, &4, &6, &8]);

// extract words less than 4 chars
let words4: Vec<_> = lipsum.iter().filter( |w| w.len() < 4 ).collect();
assert_eq!(words4, vec![&"sit", &"sed", &"do", &"ut", &"et"]); 
```

Enter fullscreen mode Exit fullscreen mode

*   *折叠()*

参考我之前关于 Python 的 *reduce()* 或 Ruby 的 *inject()* 方法的文章，获得关于 *fold()* 函数的一些细节。

例子:

```
// sum of first 10 digits
assert_eq!(digits.iter().fold(0, |x, y| x + y), 45);

fn nested(coeff: &[u64], z: u64) -> u64 {
    coeff.iter().fold(0, |x, y| z*x + y)
}
assert_eq!(nested(&[1,5,10,10,5,1], 1), 32);

// easy computation of the nested square root which converges to the golden ratio
let golden = (1.0+f64::sqrt(5.0))/2.0;

let mut approx_golden = vec![1f64;100].iter().fold(1f64, |x,y| f64::sqrt(x+y));
assert!(f64::abs(approx_golden - golden) < 10E-6f64);

approx_golden = vec![1f64;100].iter().fold(1f64, |x,y| y+1f64/x);
assert!(f64::abs(approx_golden - golden) < 10E-6f64); 
```

Enter fullscreen mode Exit fullscreen mode

注意:为了确保每个示例都能编译，我编写了一个简单的 Python 脚本来提取代码示例，并将它们编译成一个 Rust 源文件来执行。

这就结束了我用 Python、Ruby 和 Rust 编写的三重线性系列。希望你喜欢。随意评论。

> 苏珊·尹在 Unsplash 上的照片*