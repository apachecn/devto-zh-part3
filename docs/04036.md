# 铁锈必知板条箱

> 原文：<https://dev.to/cad97/rust-must-know-crates-5ad8>

这是我经常使用的铁锈板条箱收藏。不是每个项目都需要使用它们，但是大多数项目知道它们的存在会更好。板条箱的排列非常松散，从更普遍的用途到更特殊的用途。

随着我的偏好和生态系统的变化，这个列表会保持一定的更新。这显然不是一个完整的大箱子列表，而只是对程序员可用的 Rust 实用程序的大生态系统的介绍。

# 货物外挂

## [货物-编辑](https://lib.rs/crates/cargo-edit)

> 该工具扩展了 Cargo，允许您通过从命令行修改 Cargo.toml 文件来添加、删除和升级依赖项。
> 
> 当前可用的子命令:
> 
> *   货物添加
> *   货运室
> *   货物升级

## [货物过时](https://lib.rs/crates/cargo-outdated)

> 用于显示 Rust 依赖关系何时过期的 cargo 子命令。

## [货船树](https://lib.rs/crates/cargo-tree)

> 一个 Cargo 子命令，以树状格式可视化一个板条箱的依赖图。

## [货物-更新](https://lib.rs/crates/cargo-update)

> Cargo 子命令，用于检查已安装的可执行文件并对其应用更新。

## [【货物扩展】](https://lib.rs/crates/cargo-expand)

> 打印出宏扩展和`#[derive]`扩展应用于当前板条箱的结果。

## [货舱](https://lib.rs/crates/cargo-modules)

> 一个货物插件，用于显示板条箱模块的概览。

## [货物-篷布](https://lib.rs/crates/cargo-tarpaulin)

> 防水布是为货物构建系统设计的代码覆盖报告工具，以一种用于覆盖船上货物的防水布命名。目前，防水布提供工作线覆盖，但仍处于早期开发阶段，因此可能包含一些错误。

## [货物-审计](https://crates.io/crates/cargo-audit)

> 审计 Cargo.lock，查找向 RustSec 咨询数据库报告的存在安全漏洞的板条箱。

## [货船](https://lib.rs/crates/cargo-crev)

> 一个帮助 Rust 用户检查他们使用的板条箱，并与社区分享的工具。它作为一个推荐系统，帮助识别质量差的产品，防止许多攻击媒介，并通过鼓励持续的同行评审文化，旨在推动 Rust 生态系统的质量更高。

## [货运克隆](https://lib.rs/crates/cargo-clone)

> 获取一个生锈箱子的源代码。

很可能在某个时候被添加到货物中。

# 派生

## [衍生-更多](https://lib.rs/crates/derive_more)

> Rust 有许多为其基本类型实现的内置特性，比如`Add`、`Not`或`From`。然而，当将这些类型包装在您自己的结构或枚举中时，您会丢失这些特征的实现，并且需要重新创建它们。当您自己的结构非常简单时，例如使用普遍推荐的 newtype 模式(例如`MyInt(i32)`)时，这尤其令人恼火。
> 
> 本库试图消除这些烦恼和相应的样板代码。它允许你为结构体和枚举派生出许多常用的特征。

派生于:

*   [T2`From`](https://jeltef.github.io/derive_more/derive_more/from.html)
*   [T2`Into`](https://jeltef.github.io/derive_more/derive_more/into.html)
*   [T2`FromStr`](https://jeltef.github.io/derive_more/derive_more/from_str.html)
*   [T2`TryInto`](https://jeltef.github.io/derive_more/derive_more/try_into.html)
*   [`Display`——喜欢](https://jeltef.github.io/derive_more/derive_more/display.html)
*   [`Index`](https://jeltef.github.io/derive_more/derive_more/index_op.html) ( [`Mut`](https://jeltef.github.io/derive_more/derive_more/index_mut.html) )
*   [`Deref`](https://jeltef.github.io/derive_more/derive_more/deref.html) ( [`Mut`](https://jeltef.github.io/derive_more/derive_more/deref_mut.html) )
*   [`Add`](https://jeltef.github.io/derive_more/derive_more/add.html) ( [`Assign`](https://jeltef.github.io/derive_more/derive_more/add_assign.html) )
*   [`Mul`](https://jeltef.github.io/derive_more/derive_more/mul.html) ( [`Assign`](https://jeltef.github.io/derive_more/derive_more/mul_assign.html) )
*   [T2`Not`](https://jeltef.github.io/derive_more/derive_more/not.html)
*   还有更多！

## [智能默认](https://lib.rs/crates/smart-default)

> 使用定制的默认值自动实现`Default`特征的定制派生。

## [派生-构建器](https://lib.rs/crates/derive_builder)

> Rust 宏自动实现任意结构的构建器模式。一个简单的`#[derive(Builder)]`将为你的结构`Foo`生成一个`FooBuilder`，包含所有的 setter-方法和一个 build 方法。

## [乱弹](https://lib.rs/crates/strum)

> 一组宏和特征，用于处理更容易生锈的枚举和字符串。

派生于:

*   [T2`Display`](https://github.com/Peternator7/strum#Display)
*   [T2`AsRefStr`](https://github.com/Peternator7/strum#AsRefStr)
*   [T2`IntoStaticStr`](https://github.com/Peternator7/strum#IntoStaticStr)
*   [T2`EnumIter`](https://github.com/Peternator7/strum#enumiter)
*   [T2`EnumCount`](https://github.com/Peternator7/strum#enumcount)
*   还有更多！

# 元编程(宏)

## [syn](https://lib.rs/crates/syn)

> Syn 是一个解析库，用于将 Rust 令牌流解析为 Rust 源代码的语法树。

## [程序宏 2](https://libs.rs/crates/proc_macro2)

> 编译器的`proc_macro`箱的程序宏 API 周围的运行时兼容包装器。

## [proc-quote](https://lib.rs/crates/proc-quote)

> 将`quote!`宏作为程序宏，代替原来的`quote!`宏，用`macro_rules!`实现。
> 
> `quote!`宏将 Rust 语法树数据结构转化为源代码的标记。

## [粘贴](https://lib.rs/crates/paste)

> 一种在宏中粘贴标识符的灵活方式，包括使用粘贴的标识符来定义新项。

# 测试

## [insta](https://lib.rs/crates/insta)

> 快照测试(有时也称为批准测试)是根据参考值(快照)断言值的测试。这类似于`assert_eq!`让您将一个值与一个参考值进行比较，但与简单的字符串断言不同，快照测试让您针对复杂的值进行测试，并附带全面的工具来检查更改。

## [trybuild](https://lib.rs/crates/trybuild)

> Trybuild 是一个测试工具，用于在一组测试用例上调用 rustc，并断言任何产生的错误消息都是预期的。

## [准则](https://lib.rs/crates/criterion)

> 通过快速、准确地检测和测量性能改进或退化，即使是很小的改进或退化，帮助您快速编写代码。知道每个变化如何影响代码的性能，您就可以放心地进行优化。

# 其他库

## [正则](https://lib.rs/crates/regex)

> 用于解析、编译和执行正则表达式的 Rust 库。它的语法类似于 Perl 风格的正则表达式，但是缺少一些特性，比如环视和反向引用。作为交换，所有搜索都根据正则表达式和搜索文本的大小以线性时间执行。

## [懒-静](https://lib.rs/crates/lazy_static)

> 在 Rust 中声明延迟求值的静态的宏。
> 
> 使用这个宏，有可能静态地要求代码在运行时被执行以便被初始化。这包括任何需要堆分配的东西，比如向量或哈希映射，以及任何需要计算非常数函数调用的东西。

## [人造丝](https://lib.rs/crates/rayon)

> Rayon 是 Rust 的数据并行库。它非常轻量级，可以很容易地将顺序计算转换成并行计算。它还保证了数据竞争的自由。

## [塞尔德](https://lib.rs/crates/serde)

> Serde 是一个有效和通用地序列化和反序列化 Rust 数据结构的框架。
> 
> Serde 生态系统由知道如何序列化和反序列化自身的数据结构以及知道如何序列化和反序列化其他内容的数据格式组成。Serde 提供了这两个组相互交互的层，允许使用任何支持的数据格式序列化和反序列化任何支持的数据结构。

### [类型标记](https://lib.rs/crates/typetag)

> Serde 可序列化和可反序列化的特征对象。
> 
> 这个 crate 提供了一个宏，用于无痛序列化`&dyn Trait` trait 对象和序列化+反序列化`Box<dyn Trait>` trait 对象。

## [横梁](https://lib.rs/crates/crossbeam)

> 一套用于并发编程的工具。

## [Itertools](https://lib.rs/crates/itertools)

> 额外的迭代器适配器、函数和宏。

## [兰德](https://lib.rs/crates/rand)

> 用于随机数生成的 Rust 库。
> 
> Rand 提供了一些工具来生成随机数，将它们转换成有用的类型和分布，以及一些与随机性相关的算法。

## [日志](https://lib.rs/crates/log)

> Rust 库提供了一个轻量级的日志门面。

## [沃克迪尔](https://lib.rs/crates/walkdir)

> 一个跨平台的 Rust 库，用于有效地递归遍历目录。附带对以下符号链接的支持，控制打开的文件描述符的数量，以及修剪目录树中条目的有效机制。

## [目录](https://lib.rs/crates/directories)

> 一个小型中级库，通过利用由 Linux 上的 XDG 基本/用户目录规范、Windows 上的已知文件夹 API 和 macOS 上的标准目录指南定义的机制，为 Linux、Windows 和 macOS 上的配置、缓存和其他数据提供特定于平台的标准目录位置。

## [tempfile](https://lib.rs/crates/tempfile)

> Rust 的一个安全的、跨平台的临时文件库。除了创建临时文件之外，这个库还允许用户安全地打开对同一个临时文件的多个独立引用(对于消费者/生产者模式很有用，但安全地实现却非常困难)。

## [小水](https://lib.rs/crates/num)

> Rust 的数字类型和特征的集合，包括 bigint、complex、rational、范围迭代器、泛型整数等等！

## [t1](#structopt)[结构图](https://lib.rs/crates/structopt)

> 通过定义结构来解析命令行参数。它结合了 clap 和 custom derive。

## [ScopeGuard](https://lib.rs/crates/scopeguard)

> Rust crate 是一个方便的 RAII scope guard，当它超出范围时，它将运行给定的闭包，即使代码在混乱之间(假设展开混乱)。

## [复赛](https://lib.rs/crates/ref-cast)

> 将`&T`安全地强制转换为`&U`，其中结构`U`包含类型`T`的单个字段。

## [选择-rustc](https://lib.rs/crates/select-rustc)

> 根据 rustc 编译器版本进行条件编译的宏，类似于`#[cfg(...)]`和`#[cfg_attr(...)]`。