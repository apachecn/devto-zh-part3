# 如何在 Rust 中排序

> 原文：<https://dev.to/kdrakon/how-to-sequence-in-rust-4nhl>

嗯，我不会说就 Scala 等 FP 语言而言，它与*排序*完全相同，但它在 Rust 中为给定的集合类型做了工作；将类型为`F<G<A>>`的东西转化为`G<F<A>>`。

通常，我们希望`F`和`G`是可迭代和可应用的类型。在 Rust 的例子中，我从标准库中了解到的是，应该存在一个特征实现——`FromIterator`——它应该允许`G`像应用程序一样被对待。由于`collect()`已经在迭代`G<A>`的集合，所以对于`F`的`FromIterator`的实现来说，将第一个`FromIterator`的结果(即它们的集合)转化为`G<F<A>>`——这就是它所做的。

```
let opt = vec![Some(1), Some(2), Some(3)];
let sequenced = opt.into_iter().collect::<Option<Vec<i32>>>();
print!("{:?}", sequenced); // Some([1, 2, 3])

let opt = vec![Some(1), None, Some(3)];
let sequenced = opt.into_iter().collect::<Option<Vec<i32>>>();
print!("{:?}", sequenced); // None

let result = vec![Ok(1), Ok(2), Ok(3)];
let sequenced = result.into_iter().collect::<Result<Vec<i32>, &str>>();
print!("{:?}", sequenced); // Ok([1, 2, 3])

let result = vec![Ok(1), Err("oops"), Ok(3)];
let sequenced = result.into_iter().collect::<Result<Vec<i32>, &str>>();
print!("{:?}", sequenced); // Err("oops")

let result = vec![Ok(1), Ok(2), Ok(3)];
let sequenced = result.into_iter().map(|opt_i| opt_i.map(|i| i + 1)).collect::<Result<Vec<i32>, &str>>();
print!("{:?}", sequenced); // Ok([2, 3, 4]) 
```

*原贴为[要诀](https://gist.github.com/kdrakon/896fd22561483eb905165565b89db43d)。*