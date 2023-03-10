# Rust TryFrom 和条件编译

> 原文：<https://dev.to/jeikabu/rust-tryfrom-and-conditional-compilation-46fe>

[Rust 1.34](https://blog.rust-lang.org/2019/04/11/Rust-1.34.0.html) 包括(早该有的) [TryFrom](https://doc.rust-lang.org/std/convert/trait.TryFrom.html) 性状的稳定化。它执行类似于来自特征的[的类型转换，但是可能会失败。](https://doc.rust-lang.org/std/convert/trait.From.html)

我们希望使用 TryFrom，同时仍然支持可能不可用的早期工具链。本质上，我们需要基于 Rust 版本的条件编译。这目前还没有公开为可用于构建脚本的[变量，尽管这在未来可能会改变(参见货物问题](https://doc.rust-lang.org/cargo/reference/environment-variables.html) [2903](https://github.com/rust-lang/cargo/issues/2903) 和 [4408](https://github.com/rust-lang/cargo/issues/4408) )。同时，有两种流行的板条箱提供这种功能:

*   [版本 _ 检查](https://crates.io/crates/version_check) ( [Github](https://github.com/SergioBenitez/version_check) )
*   [rustc_verison](https://crates.io/crates/rustc_version)

我们将使用 version_check 来公开基于 Rust 版本的功能。

## 有条件编译

在`Cargo.toml` :

```
[build-dependencies]
version_check = "0.1" 
```

Enter fullscreen mode Exit fullscreen mode

在`build.rs` :

```
// Check if version of rustc is >= 1.34
match version_check::is_min_version("1.34.0") {
    Some((true, _version)) => println!("cargo:rustc-cfg=try_from"),
    _ => {}
} 
```

Enter fullscreen mode Exit fullscreen mode

`println!("cargo:rustc-cfg=try_from")`是由 cargo 解释的[，将作为`--cfg try_from`传递给 rustc 编译器。](https://doc.rust-lang.org/cargo/reference/build-scripts.html#outputs-of-the-build-script)

然后，在`lib.rs`(或板条箱的其他地方):

```
/// The error type returned when unable to convert an integer to an enum value.
#[derive(Debug, Copy, Clone, PartialEq, Eq)]
#[cfg(try_from)]
pub struct EnumFromIntError(pub i32);

impl nng_stat_type_enum {
    /// Converts value returned by [nng_stat_type](https://nanomsg.github.io/nng/man/v1.1.0/nng_stat_type.3) into `nng_stat_type_enum`.
    pub fn try_convert_from(value: i32) -> Option<Self> {
        use crate::nng_stat_type_enum::*;
        match value {
            value if value == NNG_STAT_SCOPE as i32 => Some(NNG_STAT_SCOPE),
            //...
            _ => None,
        }
    }
}

#[cfg(try_from)]
impl TryFrom<i32> for nng_stat_type_enum {
    type Error = EnumFromIntError;
    fn try_from(value: i32) -> Result<Self, Self::Error> {
        nng_stat_type_enum::try_convert_from(value).ok_or(EnumFromIntError(value))
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`nng_stat_type_enum::try_convert_from()`可用于任何版本的 Rust。

`#[cfg(try_from)]`是一个[条件编译属性](https://doc.rust-lang.org/reference/conditional-compilation.html)，它在指定`--cfg try_from`时启用 source(即在本例中是 Rust 1.34+)。这与 C/C++预处理器的`#if` / `#ifdef`非常相似。

注意与[货物“特征”](https://doc.rust-lang.org/cargo/reference/manifest.html#the-features-section)的相似性:

|  | “普通”cfg | 特征 |
| --- | --- | --- |
| 货物选项 |  | `cargo build --features try_from` |
| `Cargo.toml` |  | `package_name = { version = "1.1.1", features = ["try_from"] }` |
| 构建脚本 | `println!("cargo:rustc-cfg=try_from")` | `println!("cargo:rustc-cfg=feature=\"try_from\"")` |
| rustc 选项 | `--cfg try_from` | `--cfg 'feature="try_from"'` |
| 源属性 | `#[cfg(try_from)]` | `#[cfg(feature = "try_from")]` |

## 行动中的尝试

在`i32 -> nng_stat_type_enum`方法中使用 TryFrom 的例子:

```
/// See [nng_stat_type](https://nanomsg.github.io/nng/man/v1.1.0/nng_stat_type.3).
pub fn stat_type(&self) -> result::Result<nng_stat_type_enum, EnumFromIntError> {
    unsafe {
        let val: i32 = nng_stat_type(self.nng_stat());
        nng_stat_type_enum::try_from(val)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

或者，使用 [TryInto](https://doc.rust-lang.org/std/convert/trait.TryInto.html) 隐式调用`try_from()` :

```
/// See [nng_stat_type](https://nanomsg.github.io/nng/man/v1.1.0/nng_stat_type.3).
pub fn stat_type(&self) -> result::Result<nng_stat_type_enum, EnumFromIntError> {
    unsafe {
        let val: i32 = nng_stat_type(self.nng_stat());
        val.try_into()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

几乎没有什么惊天动地的，甚至可能有点无聊。但是，当失败不是“例外”时，有一种标准化的方法来转换类型是很好的。