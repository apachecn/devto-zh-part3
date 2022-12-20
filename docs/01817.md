# 在 stdsimd 中支持 Altivec 快速说明

> 原文：<https://dev.to/luzero/supporting-altivec-in-stdsimd-quick-notes-889>

我已经在前[写了博客，现在我正试图完成它，每天几个指令。](https://blogs.gentoo.org/lu_zero/2018/07/02/altivec-and-vsx-in-rust-part-1/)

## 我们如何支持 Altivec 内部函数

Altivec 的内部函数有点多态:`vec_add(a, b) -> c`适用于大量的`a`和`b`(以及`c`)的组合。

我们希望在 rust 中也支持这一点，这意味着我们必须添加大量的特征和样板(大量的)来产生它。

幸运的是，我们可以利用`llvm`低级内部函数，所以问题的大部分是将低级连接到高级抽象，这里是一个例子。

```
pub trait VectorAdd<Other> {
    type Return;
    vec_add(self, b: Other) -> Self::Return;
}

impl VectorAdd<vector_signed_char> for vector_bool_char {
    type Result = vector_signed_char;
    #[inline]
    #[target_feature(enable = "altivec")]
    unsafe fn vec_add(self, other: vector_signed_char) -> Self::Result {
        vec_add_bc_sc(self, other)
    }
}
... 
```

然后最终你实现了通用函数`vec_add` :

```
#[inline]
#[target_feature(enable = "altivec")]
pub unsafe fn vec_add<T, U>(a: T, b: U) -> <T as sealed::VectorAdd<U>>::Result
where
    T: sealed::VectorAdd<U>,
{
    a.vec_add(b)
} 
```

这是一项非常无聊的工作，包括:

*   为指令写一个简单的 C 测试用例
*   将其输入到`clang`以产生一个`.ll`文件和一个`.s`文件
*   写下`llvm`-内部固有的存根
*   编写一个`assert_instr`包装的测试函数
*   写一个包装特征
*   为所有必须支持的类型组合编写实现
*   编写公共泛型函数
*   写一个或多个测试来确保你没有弄糟前面所有的步骤。

## 电流限制

无论是 **rust** 还是 **C** 都没有很好的方式将函数参数描述为*纯文本*。

Altivec 有大量的内部函数，真的需要**对其进行明确和严格的说明，因为其中一个参数是嵌入在编码指令中的 5 位文字。**

在 *rust* 中，我们有[rustc _ args _ required _ const](https://github.com/rust-lang/rust/pull/48018)将参数标记为编译时常量。

遗憾的是，信息没有被[转发](https://github.com/rust-lang/rust/issues/61180)，所以在这个问题没有解决之前，所有这些指令(一些相对边缘的指令如 *vec_ctf* 一些有用的指令如*vec _ splat _ { type }*immediates)都没有一个合理的方法来实现。

今晚就到此为止，一旦我有更多的东西要写，我会很快再次编辑这篇文章并发表在我的另一个[博客](https://blogs.gentoo.org/lu_zero)上。

即将发布: [cargo-c](https://github.com/lu-zero/cargo-c) 开发笔记和更多关于 [crav1e](https://github.com/lu-zero/crav1e) 和 [rav1e](https://github.com/lu-zero/rav1e) 即将发布第一张发布快照的信息。