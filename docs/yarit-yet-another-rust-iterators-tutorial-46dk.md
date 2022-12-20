# YARIT:又一个 Rust 迭代器教程

> 原文：<https://dev.to/dandyvica/yarit-yet-another-rust-iterators-tutorial-46dk>

好吧，这不是最令人兴奋的标题，但是在本文中，我将讨论 Rust 迭代器。它们是编写习惯性 Rust 代码的关键。

为了完全理解下面的文章，你应该对特征有一个基本的了解，因为它们是迭代器的一部分，并且要知道 Rust 的所有权系统。

迭代器非常简单，但有时很难理解。特别是在理解 *iter()* 和 *iter_mut()* ad-hoc 方法和 *into_iter()* trait 方法的区别时。

## 迭代器:TL；速度三角形定位法(dead reckoning)

对于那些急着要 Rust 迭代器精髓的人来说。

### 迭代器

*   Rust 迭代器是一个值(结构，枚举，...)实现了*迭代器*特征，这可以归结为编写 *next()* 方法。一旦完成，这个值就变成可迭代的了
*   这个*迭代器::next()* 方法或者由 *for-in* 构造或者由其他被称为*适配器*的*迭代器*特征方法显式调用，或者在幕后调用
*   *迭代器::next()* 返回一个*选项< T >* 类型:
    *   如果返回 *None* ，迭代停止。如果 *None* 永远不会返回，那么迭代器是无限的
    *   如果返回 *Some(T)* ，迭代继续
*   一旦*迭代器::next()* 被实现，其他的 trait 方法就可以免费获得(例如: *take()* )

### 适配器

*   Rust 迭代器适配器(或只是一个适配器)是来自*迭代器*特征的方法，它接受一个迭代器并返回另一个迭代器(例如: *take()* )或单个值(例如: *count()* ， *nth()* ，...)
*   在这些方法中， *collect()* 最为重要，因为它将迭代器转换为集合

> 值得注意的是，迭代器适配器使用 *self* 作为它们的第一个参数，通过值接受它们的输入，而不是通过引用或可变引用。这意味着它们的输入值一旦被使用就会被移动

### *成迭代器*特质

*   这个特征定义了一个将值转换成迭代器的方法: *into_iter()*
*   此方法按值消耗*自身*:

```
fn into_iter(self) -> Self::IntoIter; 
```

Enter fullscreen mode Exit fullscreen mode

这意味着当与一个值(而不是引用)一起使用时，该值将被移动并永远消失。

*   该方法在 for-in 构造的*过程中隐式使用:*

```
// loop through a vector
let v = vec![0,1,2,3];

// standard loop
for i in v {
    // blah blah
}

// this desugars as
let mut iter = IntoIterator::into_iter(v);
loop {
    match iter.next() {
        Some(i) => { 
            // blah blah 
        },
        None => break,
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   但是也可以明确地与适配器一起使用:

```
let v1 = vec![0,1,2,3];

// after that, v1 is moved and not longer accessible
assert_eq!(v1.into_iter().count(), 4);

let mut v2 = vec![0,1,2,3];

// that way, it's possible to re-use v2, but clumsy and not very elegant
assert_eq!((&v2).into_iter().count(), 4);
assert_eq!((&v2).into_iter().nth(1).unwrap(), &1);
assert_eq!((&mut v2).into_iter().nth(1).unwrap(), &mut 1); 
```

Enter fullscreen mode Exit fullscreen mode

> 一旦*迭代器*实现，*进入迭代器*自动实现。

### 锈标收藏

*   所有 Rust 标准集合都实现了 *into_iter()* ，但是根据它是应用于引用还是值，返回不同的类型:

| 可变类型 | 类型由*返回到 _iter()* | 由 *next()* 返回的类型 |
| --- | --- | --- |
| *Vec<T>T1】* | std::iter::IntoIterator | T |
| *&Vec<T>T1】* | std::slice::Iter | &T |
| *&mut Vec<T>T1】* | std::slice::IterMut | 但是 t(m) |

*   为了摆脱之前笨拙的语法，Rust 集合通常会提出特别的方法 *iter()* 通过引用进行迭代，以及 *iter_mut()* 通过可变引用进行迭代。这导致了以下更清晰的语法:

```
// much more elegant and easy to read syntax
assert_eq!(v2.iter().count(), 4);
assert_eq!(v2.iter().nth(1).unwrap(), &1);
assert_eq!(v2.iter_mut().nth(1).unwrap(), &mut 1); 
```

Enter fullscreen mode Exit fullscreen mode

### *from 迭代器* trait

*FromIterator* 特征用于将迭代器转换为用户定义的集合，并由*迭代器::collect()* 方法调用。

## 实现自己的迭代器

现在您已经阅读了概述，是时候看看如何在您自己的数据结构上实现迭代器的实际例子了。

让我们写一个斐波纳契数列，它可以是无限的，也可以是有限的，这取决于它是如何创建的:

```
// Simplified version of a Fibonacci sequence: F0 and F1 are not returned
// Keep track of n-1 and n values
#[derive(Clone)]
struct Fibonacci {
    fib_n_1: u64,
    fib_n: u64,
    limit: Option<u64>,     // maximum Fibonacci number, None if infinite
}

// Fibonacci sequence is well known
impl Fibonacci {
    fn new(limit: Option<u64>) -> Fibonacci {
        Fibonacci {
            fib_n_1: 0,
            fib_n: 1,
            limit: limit, 
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以直接实现*迭代器* trait:

```
// only implements Iterator and not IntoIterator
impl Iterator for Fibonacci {
    type Item = u64;

    fn next(&mut self) -> Option<Self::Item> {
        let next_fib = self.fib_n + self.fib_n_1;

        self.fib_n_1 = self.fib_n;
        self.fib_n = next_fib;

        // infinite sequence?
        if self.limit.is_none() {
            Some(next_fib)             
        }
        else {
            // don't overflow the limit
            if next_fib > self.limit.unwrap() {
                None
            }
            // ok we can loop
            else {
                Some(next_fib)  
            }
        }
    }   
} 
```

Enter fullscreen mode Exit fullscreen mode

并播放和迭代斐波那契:

```
// this is an infinite sequence
let inf_fibo = Fibonacci::new(None);

// we can use adapter on our struct, but as take(), it consumes fibo which is moved and gone. That's why it's cloned
let first_values: Vec<_> = inf_fibo.take(11).collect();
assert_eq!(first_values, vec![1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144]);

// this is an finite sequence
let fin_fibo = Fibonacci::new(Some(100_000));

// our Fibonacci starts from n=2: 24 numbers < 100_000 (from F2 to F26)
assert_eq!(fin_fibo.clone().count(), 24);

// F25 = 75025
assert_eq!(fin_fibo.clone().nth(23).unwrap(), 75025);

// another way, enumerate() yields a tuple
let f25 = fin_fibo.clone().enumerate().nth(23).unwrap();
assert_eq!(f25.0, 23);
assert_eq!(f25.1, 75025);

// F25 is also the last one
assert_eq!(fin_fibo.clone().last().unwrap(), 75025);

// get even Fibonacci numbers
let even_fibo: Vec<_> = fin_fibo.clone().filter(|fib| fib%2 == 0).collect();
assert_eq!(even_fibo, vec![2, 8, 34, 144, 610, 2584, 10946, 46368]); 
```

Enter fullscreen mode Exit fullscreen mode

现在，前面的结构非常简单，它的迭代器正在消耗它的数据。用户自定义集合怎么样？

让我们试一试。

## 用户定义集合上的迭代器

让我们想象一个用户定义的集合，它将一个短语分割成单个的单词，将这些单词存储到一个向量中:

```
// a structure holding &str, just a wrapper on &str vector
struct Words<'a> {
    words: Vec<&'a str>,
}

// just split input text into invidual words
impl<'a> Words<'a> {
    fn new(init: &str) -> Words {
        Words {
            words: init.split(" ").collect(),
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

实现*迭代器*特征在这里没有意义，因为它只有在与适配器结合使用时才可用一次。诀窍是实现 *IntoIterator* 特征，它将*单词*转换成一个中间结构助手。然后，后一种结构必须实现迭代器特性*才能是可迭代的。*

但是请记住，无论是否需要使用集合元素，都有 3 个实现需要编写:一个用于使用迭代器，一个用于非使用迭代器，一个用于可变的非使用迭代器。

首先，实现消费迭代器:

```
// structure helper for consuming iterator.
struct IntoIteratorHelper<'a> {
    iter: ::std::vec::IntoIter<&'a str>,
}

// implement the IntoIterator trait for a consuming iterator. Iteration will
// consume the Words structure 
impl<'a> IntoIterator for Words<'a> {
    type Item = &'a str;
    type IntoIter = IntoIteratorHelper<'a>;

    // note that into_iter() is consuming self
    fn into_iter(self) -> Self::IntoIter {
        IntoIteratorHelper {
            iter: self.words.into_iter(),
        }
    }
}

// now, implements Iterator trait for the helper struct, to be used by adapters
impl<'a> Iterator for IntoIteratorHelper<'a> {
    type Item = &'a str;

    // just return the str reference
    fn next(&mut self) -> Option<Self::Item> {
            self.iter.next()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，非消耗迭代器:

```
// structure helper for non-consuming iterator.
struct IterHelper<'a> {
    iter: ::std::slice::Iter<'a, &'a str>,
}

// implement the IntoIterator trait for a non-consuming iterator. Iteration will
// borrow the Words structure 
impl<'a> IntoIterator for &'a Words<'a> {
    type Item = &'a &'a str;
    type IntoIter = IterHelper<'a>;

    // note that into_iter() is consuming self
    fn into_iter(self) -> Self::IntoIter {
        IterHelper {
            iter: self.words.iter(),
        }
    }
}

// now, implements Iterator trait for the helper struct, to be used by adapters
impl<'a> Iterator for IterHelper<'a> {
    type Item = &'a &'a str;

    // just return the str reference
    fn next(&mut self) -> Option<Self::Item> {
            self.iter.next()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后是可变的非消耗迭代器:

```
// structure helper for mutable non-consuming iterator.
struct IterMutHelper<'a> {
    iter: ::std::slice::IterMut<'a, &'a str>,
}

// implement the IntoIterator trait for a mutable non-consuming iterator. Iteration will
// mutably borrow the Words structure 
impl<'a> IntoIterator for &'a mut Words<'a> {
    type Item = &'a mut &'a str;
    type IntoIter = IterMutHelper<'a>;

    // note that into_iter() is consuming self
    fn into_iter(self) -> Self::IntoIter {
        IterMutHelper {
            iter: self.words.iter_mut(),
        }
    }
}

// now, implements Iterator trait for the helper struct, to be used by adapters
impl<'a> Iterator for IterMutHelper<'a> {
    type Item = &'a mut &'a str;

    // just return the str reference
    fn next(&mut self) -> Option<Self::Item> {
            self.iter.next()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以实现 *FromIterator* 特征，这样就相当全面了:

```
use std::iter::FromIterator;

// implement FromIterator
impl<'a> FromIterator<&'a str> for Words<'a> {
    fn from_iter<T>(iter: T) -> Self
    where
        T: IntoIterator<Item = &'a str> {

        // create and return Words structure
        Words {
            words: iter.into_iter().collect(),
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了给出最终的顶点，我们应该为*单词* :
实现 *iter()* 和 *iter_mut()* 方法

```
// just split input text into invidual words
impl<'a> Words<'a> {
    fn iter(&'a self) -> IterHelper<'a> {
        self.into_iter()
    }

    fn iter_mut(&'a mut self) -> IterMutHelper<'a> {
        self.into_iter()
    }    
} 
```

Enter fullscreen mode Exit fullscreen mode

这是大量样板代码，但是可以使用特别的宏来减少。我将在另一篇文章中讨论 Rust 宏。现在我们可以在集合中使用所有的迭代器框架:

```
let mut uw = Words::new("Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.");

assert_eq!((&uw).into_iter().count(), 19);

// loop through &uw
for w in &uw {
    println!("{}", w);
}

// user iter()
let upper: Vec<_> = uw.iter().map(|w| w.to_uppercase()).collect();
assert_eq!(upper[0], "LOREM");

// we can modify elements now
for w in &mut uw {
    println!("{}", w);
}

// FromIterator tryout
let lipsum_short: Words = vec!["Lorem", "ipsum", "dolor", "sit", "amet"].iter().map(|w| *w).collect();
assert_eq!(lipsum_short.words, vec!["Lorem", "ipsum", "dolor", "sit", "amet"]); 
```

Enter fullscreen mode Exit fullscreen mode

本文中有很多代码，但是所有代码都是用最新的 Rust 编译器 1.34.1 版编译的。

> bert sz 在 Unsplash 上拍摄的照片