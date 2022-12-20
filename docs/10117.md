# Rust 101 中的程序宏

> 原文：<https://dev.to/naufraghi/procedural-macro-in-rust-101-k3f>

<small>*封面图片由 [Ricardo Gomez Angel](https://unsplash.com/photos/3kzlCL3rj8A?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 Unsplash*T5 拍摄</small>

# Rust 101 中的程序宏

> 今天有了 Rust 1.51，就不需要宏了！([见下文](#modern-rust-raw-combinations-endraw-))

*如何选择一个功能并使其成为一个添加了超能力的宏*

很久以前，我需要获取一个项目列表，并返回所有夫妇的所有组合。

使用内置的 [itertools.combinations](https://docs.python.org/3/library/itertools.html#itertools.combinations) 可以轻松完成 Python 中的这项任务:

> **`itertools.combinations(iterable, r)`**
> 从输入的 iterable 中返回`r`长度的子序列元素。
> 组合按字典排序顺序发出。因此，如果输入 iterable 是排序的，组合元组将按排序的顺序产生。

这提供了 Python 中的基本实现(真正的实现是在 C 语言中并基于迭代器)。

```
def combinations(iterable, r):
    # combinations('ABCD', 2) --> AB AC AD BC BD CD
    # combinations(range(4), 3) --> 012 013 023 123
    pool = tuple(iterable)
    n = len(pool)
    if r > n:
        return
    indices = list(range(r))
    yield tuple(pool[i] for i in indices)
    while True:
        for i in reversed(range(r)):
            if indices[i] != i + n - r:
                break
        else:
            return
        indices[i] += 1
        for j in range(i+1, r):
            indices[j] = indices[j-1] + 1
        yield tuple(pool[i] for i in indices) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个函数，它接受一个列表和一个整数`r`，并返回一个长度为`r`的元组...嗯，在普通 Rust 中还不可能，但是让我们关注我的问题，我目前只需要情侣！

下面是 Rust 中的一个基本移植(足够通用，可以针对`r > 2`进行修改):

```
fn combinations2<T>(pool: &Vec<T>) -> Vec<[&T; 2]> {
    let r = 2; // <----- is the length of this ^
    let mut res = vec![];
    let n = pool.len();
    if r > n {
        return res;
    }
    let mut indices: Vec<_> = (0..r).collect();
    // we cannot `yield` (yet) in Rust, so we need an accumulator
    res.push([&pool[indices[0]], &pool[indices[1]]]);
    loop {
        // since this is not a generator like in Python, we cannot `return`, so the code
        // is slightly changed, `find` replaces the first `for` in Python.
        match (0..r).rev().find(|&i| indices[i] != (i + n - r)) {
            Some(i) => {
                indices[i] += 1;
                for j in (i + 1)..r {
                    indices[j] = indices[j - 1] + 1;
                }
                res.push([&pool[indices[0]], &pool[indices[1]]]);
            }
            None => break,
        }
    }
    res
} 
```

Enter fullscreen mode Exit fullscreen mode

几个月后，我试图通过 Rust 中的示例 <sup id="fnref1">[1](#fn1)</sup> 使用基本的*宏来实现一些东西，但是运气不好，获取单个整数并将其扩展为一个列表并不容易，代码负担太重，我不得不为`r`号<sup id="fnref3">[【3】](#fn3)</sup>选择一些最大值。*

所以我放弃了，直到更强大的过程宏达到了几乎稳定的地步！

但是这里有龙！`syn`、`proc_macro`、`proc_macro2`、`quote!`，WTF？

## 解析和引用

幸运的是，`syn` crate 仓库包含了一些非常有用的例子，特别是: [examples/lazy-static](https://github.com/dtolnay/syn/tree/master/examples/lazy-static) 是*在`functionlike!(...)`程序宏*中解析自定义语法的一个例子。 <sup id="fnref4">[4](#fn4)</sup>

但是让我们从 [`syn`板条箱](https://github.com/dtolnay/syn)开始，引用自述是:

> Syn 是一个解析库，用于将 Rust 令牌流解析为 Rust 源代码的语法树。

因此，给定一些类似 Rust 的代码，Syn 会给我们一个语法树，准备好用于语法树的所有基本类型，像`Expr`、`Ident`、`Token`、`Type`等等。
正如自述文件中提到的，Syn 为`proc_macro2`提供了一个 [Proc 宏垫片](https://github.com/dtolnay/syn#proc-macro-shim)，允许 Syn 支持回锈 1.15。

`proc_macro2`的一个小怪癖是，我们不允许从一个地方使用所有东西，因为:

> 一般来说，你所有的代码都应该针对`proc-macro2`而不是`proc-macro`编写。一个例外是程序性宏入口点的签名，这是语言使用`proc_macro::TokenStream.`所要求的

一旦我们解析了我们的输入，我们需要重新生成一些 Rust 代码，这里插入 [`quote`箱](https://github.com/dtolnay/quote)，它:

> ...提供`quote!`宏，用于将 Rust 语法树数据结构转换为源代码的标记。

我们的工作流程将是:

1.  为我们的*程序宏* <sup id="fnref5">[5](#fn5)</sup> 定义一些语法，比如会声明一个函数`comb3(values: &[T]) -> Vec<[T; 3]>`的`combinations!(comb3, 3)`，
2.  定义一个`struct`来存储宏调用解析后的部分:`comb3`和`3`，
3.  使用这些值来参数化专用代码`combinations2`，
4.  返回抽象的新函数并为编译器重新构建 Rust 代码，
5.  调用新的宏生成函数！

## 把碎片拼在一起

难题的第一部分是一个`struct`，它将包含宏定义中的用户值，在本例中，我们只有一个名称和一个数字:

```
struct  Combinations {
    name: syn::Ident,
    n: syn::LitInt,
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要某种方法来解析这个`struct`中的内容，这里引入了`Parse`特征一个由所有类型实现的*解析接口，可以从令牌流* :
中以默认方式解析

```
impl Parse for Combinations {
    fn parse(input: ParseStream) -> Result<Self> {
        let name = input.parse()?;
        input.parse::<syn::Token![,]>()?;
        let n = input.parse()?;
        Ok(Combinations { name, n })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

感谢 Rust 类型推理的强大功能，调用`input.parse()?`让我们将值解析成正确的类型，或者在解析失败时返回错误。
奇怪的行`input.parse::<syn::Token![,]>()?;`是一个[特殊的语法来解析简单的记号](https://docs.rs/syn/0.15/syn/token/index.html)。
现在我们已经解析了我们的输入，我们可以编写我们的超级极小宏，来测试一切是否按预期工作:

```
#[proc_macro]
pub fn minimal(input: TokenStream) -> TokenStream {
    let Combinations { name, n } = parse_macro_input!(input as Combinations);
    quote!{
        fn #name() -> i32 {
            #n
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

Ops，编译错误:/

```
 Compiling comb v0.1.0 (/home/naufraghi/Documents/src/circle-rs.git/comb)
error[E0308]: mismatched types
  --> comb/src/lib.rs:27:5
   |
25 |   pub fn minimal(input: TokenStream) -> TokenStream {
   |                                         ----------- expected `proc_macro::TokenStream` because of return type
26 |       let Combinations { name, n } = parse_macro_input!(input as Combinations);
27 | /     quote!{
28 | |         fn #name() -> i32 {
29 | |             #n
30 | |         }
31 | |     }
   | |_____^ expected struct `proc_macro::TokenStream`, found struct `proc_macro2::TokenStream`
   |
   = note: expected type `proc_macro::TokenStream`
              found type `proc_macro2::TokenStream` 
```

Enter fullscreen mode Exit fullscreen mode

记住，`syn`为除了`TokenStream`之外的所有东西提供了一个替代物，它应该是来自编译器接口的原始物。幸运的是，从一个`into()`到另一个的转换很简单。

```
#[proc_macro]
pub fn minimal(input: TokenStream) -> TokenStream {
    let Combinations { name, n } = parse_macro_input!(input as Combinations);
    (quote!{
        fn #name() -> i32 {
            #n
        }
    }).into()
} 
```

Enter fullscreen mode Exit fullscreen mode

如果没有额外的括号，您将会遇到另一个奇怪的编译错误，遗憾的是，甚至无法展开宏来查看到底发生了什么，...

```
 Compiling comb v0.1.0 (/home/naufraghi/Documents/src/circle-rs.git/comb)
error: expected expression, found `.`
  --> comb/src/lib.rs:31:6
   |
31 |     }.into()
   |      ^ expected expression
error[E0308]: mismatched types
  --> comb/src/lib.rs:27:5
   |
27 | /     quote!{
28 | |         fn #name() -> i32 {
29 | |             #n
30 | |         }
31 | |     }.into()
   | |_____^ expected (), found struct `proc_macro2::TokenStream`
   |
   = note: expected type `()`
              found type `proc_macro2::TokenStream` 
```

Enter fullscreen mode Exit fullscreen mode

难道我说的*扩大了*？是的，我们可以用`cargo expand`命令查看宏扩展代码，结果是:

```
$ cargo expand --bin comb
    Checking circle v0.1.0 (/home/naufraghi/Documents/src/circle-rs.git)
    Finished dev [unoptimized + debuginfo] target(s) in 0.13s
#![feature(prelude_import)]
#![no_std]
#[prelude_import]
use ::std::prelude::v1::*;
#[macro_use]
extern crate std as std;
fn mini3() -> i32 { 3 }    // <----- here is our expansion!
// ... 
```

Enter fullscreen mode Exit fullscreen mode

综上所述，我们有这样的管道:

```
TokenStream -> parse_macro_input!(...) -> quote!{...} -> TokenStream 
```

Enter fullscreen mode Exit fullscreen mode

在`quote!{}`内部，变量是用`#name`语法访问的。

### 额外异能

在这里，我们已经写了很多代码，以达到我们已经知道如何通过示例使用良好的旧*宏达到的点，但是我们有一个问题，如何将一个数字扩展成一个列表，因为这是这个级别的怪物:* 

```
res.push([&pool[indices[0]], &pool[indices[1], ...]]) 
```

Enter fullscreen mode Exit fullscreen mode

我们需要某种方法来积累一小段语法，`&pool[indices[#i]]`，并在最后将各部分连接成一个逗号分隔的列表。深究一下`syn`，我们需要的是 [`syn::punctuated`](https://docs.rs/syn/0.15/syn/punctuated/index.html) :

> 由标点符号分隔的语法树节点的标点序列。《铁锈》中的很多东西都是标点符号序列。
> 
> *   结构的字段是`Punctuated<Field, Token![,]>`。...

我们能做的是:

```
#[proc_macro]
pub fn punctuated(input: TokenStream) -> TokenStream {
    let Combinations { name, n } = parse_macro_input!(input as Combinations);
    let indices: syn::punctuated::Punctuated<_, syn::Token![,]> = (0..n.value() as i32)
        .map(|i| quote! { #i })  // <-- add the quoted variable, `i` as a token
        .collect();
    (quote! {
      fn #name() -> Vec<i32> {
        vec![#indices]  // put the inner part of the list into something
      }
    })
    .into()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们终于可以调用扩展成:
的宏`punctuated!(punct4, 4);`

```
fn punct4() -> Vec<i32> { <[_]>::into_vec(box [0i32, 1i32, 2i32, 3i32]) } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经有了所有的部分来使我们的*组合*函数通用于返回数组
的大小 *r*

```
#[proc_macro]
/// `combinations!(comb, N)` macro will define a function `comb` that takes a `&[T]` and returns
/// a vector of arrays of length _N_, `Vec<[&T; N]>`, covering all the combinations of _N_ elements
/// from the source vector.
pub fn combinations(input: TokenStream) -> TokenStream {
    let Combinations { name, n } = parse_macro_input!(input as Combinations);
    let pool_indices: syn::punctuated::Punctuated<_, syn::Token![,]> = (0..n.value() as usize)
        .map(|i| quote! { &pool[indices[#i]] })
        .collect();
    let comb = quote! {
        fn #name<T>(pool: &[T]) -> Vec<[&T; #n]> {
            let r = #n;
            let mut res = vec![];
            let n = pool.len();
            if r > n {
                return res;
            }
            let mut indices: Vec<_> = (0..r).collect();
            res.push([#pool_indices]);
            loop {
                match (0..r).rev().find(|&i| indices[i] != (i + n - r)) {
                    Some(i) => {
                        indices[i] += 1;
                        for j in (i + 1)..r {
                            indices[j] = indices[j - 1] + 1;
                        }
                        res.push([#pool_indices]);
                    },
                    None => break,
                }
            }
            res
        }
    };
    comb.into()
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们现在可以用一个简单的宏调用来定义一个具有所需大小的函数，将来我们也可以一次性创建并调用一个宏，但还不是 <sup id="fnref5">[5](#fn5)</sup> 。

最后注意，宏只能在另一个板条箱中使用，建议的布局在[惰性静态示例](https://github.com/dtolnay/syn/tree/master/examples/lazy-static)中突出显示。

> 感谢 [@dodomorandi](https://github.com/dodomorandi) 对第一稿的审阅和对更惯用的`combinations2`的建议，以及他基于`Iterator`的实现，感谢 [@lu-zero](https://github.com/lu-zero) 的审阅和对避免`syn`和寻找更简单方法的建议，任务我留给读者作为练习。

#### 基于迭代器的实现

```
#[proc_macro]
pub fn iter_combinations(input: TokenStream) -> TokenStream {
    let Combinations { name, n } = parse_macro_input!(input as Combinations);
    let pool_indices: syn::punctuated::Punctuated<_, syn::Token![,]> = (0..n.value() as usize)
        .map(|i| quote! { &self.pool[self.indices[#i]] })
        .collect();

    let initial_indices: syn::punctuated::Punctuated<_, syn::Token![,]> =
        (0..n.value() as usize).collect();

    let iter_name = proc_macro2::Ident::new(
        &format!("CombIndicesIter{}", n.value()),
        proc_macro2::Span::call_site(),
    );

    let comb = quote! {
        pub struct #iter_name<'a, T> {
            pool: &'a [T],
            indices: [usize; #n],
            started: bool,
        }

        impl<'a, T> Iterator for #iter_name<'a, T> {
            type Item = [&'a T; #n];

            fn next(&mut self) -> Option<Self::Item> {
                if !self.started {
                    self.started = true;
                    Some([#pool_indices])
                } else {
                    let n = self.pool.len();
                    (0..#n).rev().find(|&i| self.indices[i] != i + n - #n)
                        .map(|i| {
                                self.indices[i] += 1;
                                for j in (i + 1)..#n {
                                    self.indices[j] = self.indices[j - 1] + 1;
                                }
                                [#pool_indices]
                        })
                }
            }
        }

        fn #name<T>(pool: &[T]) -> impl Iterator<Item = [&T; #n]> {
            #iter_name {
                pool,
                indices: [#initial_indices],
                started: false,
            }
        }
    };
    comb.into()
} 
```

Enter fullscreen mode Exit fullscreen mode

# 【现代】锈`combinations()`

如今，有了 Rust 1.51，就不需要使用任何*宏了:

[铁锈操场](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=bc695779c048176475d69c99b5a22293)

```
use std::convert::TryInto;

fn combinations<T, const R: usize>(pool: &Vec<T>) -> Vec<[&T; R]> {
    let mut res = vec![];
    let n = pool.len();
    if R > n {
        return res;
    }
    // may have used `array-init` https://docs.rs/array-init/2.0.0/array_init/index.html
    macro_rules! into_array {
        ($it:expr) => {
            $it.collect::<Vec<_>>().as_slice().try_into().expect("should be of size R")
        };
    }
    let mut indices: [usize; R] = into_array!(0..R);
    let reversed: [usize; R] = into_array!((0..R).rev());
    res.push(into_array!(indices.iter().map(|i| &pool[*i])));
    loop {
        let idx = (|| {
            for i in reversed.iter() {
                if indices[*i] != (*i + n - R) {
                    return Some(*i);
                }
            }
            None
        })();
        if let Some(i) = idx {
            indices[i] += 1;
            for j in (i + 1)..R {
                indices[j] = indices[j - 1] + 1;
            }
            res.push(into_array!(indices.iter().map(|i| &pool[*i])));
        } else {
            break;
        }
    }
    res
}

fn main() {
    let numbers = vec![1i32,2,3,4];
    let combs: Vec<[_; 2]> = combinations(&numbers);
    // `let combs = combinations::<_, 2>(&numbers)` works, but IMHO the constraint
    // on the returned type is more explanatory and futureproof
    println!("{:?}", combs);
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

1.  超深度介绍由[Daniel keep @ github](https://github.com/DanielKeep):[rust 1.0 中宏的实用介绍](https://danielkeep.github.io/practical-intro-to-macros.html) [↩](#fnref1)

2.  我必须展开`2 -> 0, 1`，然后将其映射到`&pool[indices[0]], &pool[indices[1]]` [↩](#fnref2)

3.  再次由 DanielKeep，[锈宏的小本子:计数](https://danielkeep.github.io/tlborm/book/blk-counting.html)↩

4.  你可以试着避免使用`syn`,使用*宏和*宏的组合，但这是留给读者的练习。 [↩](#fnref4)

5.  遗憾的是，在表达式位置使用宏，比如`let res = combinations!(&data, 3)`，是在`#![feature(proc_macro_hygiene)]`之后的特性门控，并且仅限于夜间版本，所以我们使用另一种方法。 [↩](#fnref5)