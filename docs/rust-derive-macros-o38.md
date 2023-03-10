# Rust 派生宏

> 原文：<https://dev.to/jeikabu/rust-derive-macros-o38>

Rust 的宏与 C++的宏/模板的目的相似，都是在编译时运行的代码，它自己编写代码(元编程)。然而，相似之处也就到此为止了。有两种类型:

*   声明性宏(又名“示例宏”或`macro_rules!`宏)
*   程序宏。有三种类型:
    *   类似功能:例如`custom!()`
    *   衍生模式:例如`#[derive(CustomMode)]`(适用于`struct`和`enum`)
    *   属性:例如`#[CustomAttribute]`(几乎适用于任何事物)

这是关于**派生**模式程序宏的快速入门。

## 用例:到 NNG 的 FFI

我们试图解决的问题的快速总结。

### 问题

NNG 有一个简单明了的 C API。与 C++(或许多其他语言)中的函数重载不同，每种类型都有一个函数集合。`nng_socket`型有**获取**和**设置**选项功能:

```
int nng_getopt_bool(nng_socket s, const char *opt, bool *bvalp);
int nng_getopt_int(nng_socket s, const char *opt, int *ivalp);
// ...
int nng_setopt(nng_socket s, const char *opt, const void *val, size_t valsz);
int nng_setopt_bool(nng_socket s, const char *opt, int bval);
// ... 
```

Enter fullscreen mode Exit fullscreen mode

[`nng_dialer`型](https://nanomsg.github.io/nng/man/v1.1.0/nng_dialer_getopt.3) :
也有类似的功能

```
int nng_dialer_getopt_bool(nng_dialer d, const char *opt, bool *bvalp);
int nng_dialer_getopt_int(nng_dialer d, const char *opt, int *ivalp);
// ...
int nng_dialer_setopt(nng_dialer d, const char *opt, const void *val,
    size_t valsz);
int nng_dialer_setopt_bool(nng_dialer d, const char *opt, bool bval);
// ... 
```

Enter fullscreen mode Exit fullscreen mode

同样的还有 [`nng_listener`](https://nanomsg.github.io/nng/man/v1.1.0/nng_listener_getopt.3) 和 [`nng_pipe`](https://nanomsg.github.io/nng/man/v1.1.0/nng_pipe_getopt.3) 。

将 bindgen 生成的函数封装在我们的 runng-sys 机箱中很容易:

```
impl GetOpts for NngSocket {
    fn getopt_bool(&self, option: NngOption) -> NngResult<bool> {
        unsafe {
            let mut value: bool = Default::default();
            NngFail::succeed(nng_getopt_bool(self.socket, option.as_cptr(), &mut value), value)
        }
    }
    fn getopt_int(&self, option: NngOption) -> NngResult<i32> {
        unsafe {
            let mut value: i32 = Default::default();
            NngFail::succeed(nng_getopt_int(self.socket, option.as_cptr(), &mut value), value)
        }
    }
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们已经使用助手来处理锅炉板:

*   `NngFail::succeed()`将 C 返回值转换为 Rust 值(`int -> Result<T>`)
*   `.as_cptr()`把一个`enum`(带一个`&'static [u8]`)变成一个 C `const char *`命名选项(`NngOption -> *const i8`

我们开始为`nng_listener`、`nng_dialer`等复制大量的代码。但是说实话，复制粘贴代码是最可疑的[代码气味](https://en.wikipedia.org/wiki/Code_smell)之一。

### 解

使用 Rust 的派生模式宏，我们想写类似于:

```
#[derive(NngGetOpts,NngSetOpts)]
#[prefix = "nng_"]
#[nng_member = "socket"]
pub struct NngSocket {
    socket: nng_socket,
} 
```

Enter fullscreen mode Exit fullscreen mode

这将把`prefix`和`nng_member`值代入`#PREFIXgetopt_bool(self.#NNG_MEMBER, ..`以形成`nng_getopt_bool(self.socket, ..`。有效的源代码字符串替换，最终结果类似于:

```
impl GetOpts for NngSocket {
    fn getopt_bool(&self, option: NngOption) -> NngResult<bool> {
        nng_getopt_bool(self.socket, ..)
    }
    // ...
}
impl SetOpts for NngSocket {
    fn setopt_bool(&mut self, option: NngOption, value: bool) -> NngReturn {
        nng_setopt_bool(self.socket, ..)
    }
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 派生模式程序宏

用`cargo new --lib runng_derive`和`Cargo.toml` :
启动一个新的库模块

```
[package]
name = "runng_derive"
# ...

[lib]
# 1
proc-macro = true

[dependencies]
# 2
syn = "0.15"
quote = "0.6" 
```

Enter fullscreen mode Exit fullscreen mode

注意事项:

1.  `proc-macro = true`否则需要 get:

    ```
    error: the `#[proc_macro_derive]` attribute is only usable with crates of the `proc-macro` crate type 
    ```

2.  syn 和[引用](https://crates.io/crates/quote)板条箱为我们做所有真正的工作

在`lib.rs` :

```
#![recursion_limit="128"] // 1

extern crate proc_macro;
extern crate syn;
#[macro_use] // 2
extern crate quote;

use proc_macro::TokenStream;
use syn::{
    Lit,
    Meta,
    MetaNameValue,
};

// 3
#[proc_macro_derive(NngGetOpts, attributes(prefix, nng_member))]
pub fn derive_nng_get_opts(tokens: TokenStream) -> TokenStream {
    derive_nng_opts(tokens, gen_get_impl)
} 
```

Enter fullscreen mode Exit fullscreen mode

注意事项:

1.  `recursion_limit`在失败后被编译器推荐:

    ```
    error: recursion limit reached while expanding the macro 'stringify' 
    ```

2.  将`quote`板条箱的宏纳入范围(注意:[Rust 2018](https://rust-lang-nursery.github.io/edition-guide/rust-2018/macros/macro-changes.html)中不需要)

3.  `#[proc_macro_derive]`定义了我们的`#[derive(..)]`宏。其中，`attributes(..)`定义了我们的[“派生模式助手属性”](https://doc.rust-lang.org/reference/procedural-macros.html#derive-mode-helper-attributes)(即`#[prefix]`和`#[nng_member]`)。

这个函数的输入是这个属性附加到的任何代码的标记流。输出是我们想要生成的新代码。

首先提取我们的`#[prefix ..]`和`#[nng_member ..]`属性:

```
fn derive_nng_opts<F>(tokens: TokenStream, gen_impl: F) -> TokenStream
    where F: Fn(&syn::Ident, String, String) -> TokenStream
{
    // Parse TokenStream into AST
    let ast: syn::DeriveInput = syn::parse(tokens).unwrap();
    let mut prefix: Option<String> = None;
    let mut member: Option<String> = None;
    // Iterate over the struct's #[...] attributes
    for option in ast.attrs.into_iter() {
        let option = option.parse_meta().unwrap();
        match option {
            // Match '#[ident = lit]' attributes. Match guard makes it '#[prefix = lit]'
            Meta::NameValue(MetaNameValue{ref ident, ref lit, ..}) if ident == "prefix" => {
                if let Lit::Str(lit) = lit {
                    prefix = Some(lit.value());
                }
            },
            // ...
        }
    }
    gen_impl(&ast.ident, prefix.unwrap(), member.unwrap())
} 
```

Enter fullscreen mode Exit fullscreen mode

生成代码:

```
fn gen_get_impl(name: &syn::Ident, prefix: String, member: String) -> TokenStream {
    // Create function identifier like `nng_getopt_bool`
    let getopt_bool = prefix.clone() + "getopt_bool";
    let getopt_bool = syn::Ident::new(&getopt_bool, syn::export::Span::call_site());
    // ...

    let member = syn::Ident::new(&member, syn::export::Span::call_site());

    // Generate the `impl`
    let gen = quote! {
        impl GetOpts for #name {
            fn getopt_bool(&self, option: NngOption) -> NngResult<bool> {
                unsafe {
                    let mut value: bool = Default::default();
                    NngFail::succeed( #getopt_bool (self.#member, option.as_cptr(), &mut value), value)
                }
            }
        }
        // ...
    };
    gen.into()
} 
```

Enter fullscreen mode Exit fullscreen mode

[`quote`宏](https://docs.rs/quote/0.6.10/quote/macro.quote.html)负责获取我们提供的源代码，代入以`#`为前缀的值，然后生成一个`TokenStream`从我们的宏返回。

## 调试

**(由`syn`机箱的作者)打印出宏展开的结果，方便调试:** 

```
# Nightly toolchain must be installed (it doesn't need to be default)
rustup toolchain install nightly
# Install `cargo expand`
cargo install cargo-expand
# rustfmt for formatting output
rustup component add rustfmt-preview
# Pygments to colorize output
sudo pip install Pygments
# Output macro expansion
cargo expand 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/5f4d7b1b3b2988cc2d49ad4c5aaa8998.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tA9Gh-HE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/cargo_expand.png)

### 举例

需要注意的一点是`#XYZ`变量的类型。没有呼叫`syn::Ident::new()` :

```
let getopt_bool = prefix.clone() + "getopt_bool";
    //...
    fn getopt_bool(&self, option: NngOption) -> NngResult<bool> {
        unsafe {
            let mut value: bool = Default::default();
            NngFail::succeed( #getopt_bool ( /* ... */ )
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

`cargo check` :

```
error[E0618]: expected function, found `&'static str` 
```

Enter fullscreen mode Exit fullscreen mode

`cargo expand` :

```
// ...
fn getopt_bool(&self, option: NngOption) -> NngResult<bool> {
    unsafe {
        let mut value: bool = Default::default();
        NngFail::succeed("nng_getopt_bool"(self.socket,
                                            option.as_cptr(),
                                            &mut value), value)
    }
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

它在我们需要`nng_getopt_bool(...)`的时候产生了`"nng_getopt_bool"(...)`。给它一个`String`，它会马上给你一个回复！

`cargo expand`转储整个模块的输出，这可能比您想要的更多。它可以被限制为用`cargo expand --test name_of_the_test`进行一次测试，还有[和其他一些有用的选项](https://github.com/dtolnay/cargo-expand#options)。

## 更乐于助人的好帮手

用`#[nng_member = "socket"]`显式命名字段是不可靠的。我们真正想要的是根据属性的位置提取字段的名称:

```
#[derive(NngGetOpts,NngSetOpts)]
#[prefix = "nng_"]
pub struct NngSocket {
    #[nng_member]
    socket: nng_socket,
} 
```

Enter fullscreen mode Exit fullscreen mode

遍历代表源代码的`enums`和`struct`的层次结构非常容易:

```
fn get_nng_member(ast: &syn::DeriveInput) -> Option<syn::Ident> {
    match ast.data {
        syn::Data::Struct(ref data_struct) => {
            match data_struct.fields {
                // Structure with named fields (as opposed to tuple-like struct or unit struct)
                // E.g. struct Point { x: f64, y: f64 }
                syn::Fields::Named(ref fields_named) => {
                    // Iterate over the fields: `x`, `y`, ..
                    for field in fields_named.named.iter() {
                        // Get attributes `#[..]` on each field
                        for attr in field.attrs.iter() {
                            // Parse the attribute
                            let meta = attr.parse_meta().unwrap();
                            match meta {
                                // Matches attribute with single word like `#[nng_member]` (as opposed to `#[derive(NngGetOps)]` or `#[nng_member = "socket"]`)
                                Meta::Word(ref ident) if ident == "nng_member" =>
                                    // Return name of field with `#[nng_member]` attribute
                                    return field.ident.clone(),
                                _ => (),
                            }
                        }
                    }
                },
                _ => (),
            }
        },
        _ => panic!("Must be a struct"),
    }

    None
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常基本的实现；没有错误处理，不支持嵌套字段上的`#[nng_member]`等等。但是，因为这个宏只是在我们的板条箱内部用于教育目的，所以现在可能还可以。

所有这些都与[非常相似，这是 F#](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/discriminated-unions#using-discriminated-unions-for-tree-data-structures) 中有区别的联合的一种常见用法(可能也是所有其他有函数倾向的语言)。支持更多的特性和健壮的处理可能会变成递归的练习。

## 资源

关于宏和阅读顺序的更多信息，我推荐:

1.  [Rust 编程语言](https://doc.rust-lang.org/book/2018-edition/appendix-04-macros.html) -基本介绍
2.  [参考文献](https://doc.rust-lang.org/reference/procedural-macros.html) -基本介绍
3.  [“使用宏 1.1 创建枚举迭代器”](https://cbreeden.github.io/Macros11/)—`derive`宏的例子
4.  [“调试 Rust 新定制衍生系统”](https://quodlibetor.github.io/posts/debugging-rusts-new-custom-derive-system/) -开发/调试概述
5.  [`syn`例句](https://github.com/dtolnay/syn/tree/master/examples)
6.  [syn](https://docs.rs/syn) - `syn`板条箱文件。当使用 AST 时，这需要写入`derive_nng_opts()`。
7.  (可选)[锈宏的小本子](https://danielkeep.github.io/tlborm/book/README.html) -封面`macro_rules!`
    *   [“AST 中的宏”](https://danielkeep.github.io/tlborm/book/mbe-syn-macros-in-the-ast.html)

最后一项，LBRM，实际上是我首先读到的东西之一，但可能是最糟糕的开始。如果没有其他内容，请阅读“AST”部分的[“宏”。这是对 Rust 中宏的一个很好的概述，有助于将我的心理模型(字符串替换)变成现实(编译器使用的中间形式)。](https://danielkeep.github.io/tlborm/book/mbe-syn-macros-in-the-ast.html)

## )；//故事结束

我们在这里仅仅触及了表面。一方面，我对`syn::Ident::new(.., syn::export::Span::call_site())`的盲目使用值得调查——这仅仅是第一件有效的事情。这个`syn`箱子里有很多东西。

我想重温[锈宏](https://danielkeep.github.io/tlborm/book/README.html)的小书，并尝试`macro_rules!`。**