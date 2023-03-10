# 机器学习的铁锈:SIMD、布拉斯和拉普卡

> 原文：<https://dev.to/erikinapeartree/rust-for-machine-learning-simd-blas-and-lapack-4dcg>

我喜欢铁锈。但是，在机器学习领域工作，日常使用 Rust 仍然很困难。这些天我 90%的编程都是用 Python 写的。

几个月前，我对基于 Rust 的机器学习产生了兴趣。但是，我发现的主要限制是缺少一个符合人体工程学的线性代数库。有纳格拉和 T2【ndarray】以及其他一些人。然而，我发现它们当时都不符合人体工程学，与编写低级别的 SIMD、BLAS 和 Lapack 代码相比也不够快(我在最近几周和几个月里学习了更多 ndarray)。

虽然不方便，但几个月后，我很高兴我不得不更进一步。Rust 非常适合编写高性能代码。Rust 里面写相当低级的数学运算的资源相当不错。使用 [blas-src](https://github.com/blas-lapack-rs/blas-src) 和 [lapack-src](https://github.com/blas-lapack-rs/lapack-src) ，以及 Rust 的内置 SIMD 函数，我们可以编写快速且令人惊讶的可移植 Rust 代码。你甚至可以在 GPU 上运行 Rust，至少使用相同的底层代码。

### 关于锈中的 SIMD

可以说，这三种方法中对机器学习最没用，但对日常任务仍有价值的是单指令多数据操作，也称为矢量化(在 Rust 文档中，这两种参考可以互换)。在现代 CPU 上，SIMD 通过将数字打包并并行相乘，使我们执行数学运算的速度比标准方法快八倍。让我们快速比较一下，看看 SIMD 在实践中是什么样子。

考虑用标准方法将两个标量相乘:

```
fn standard_128_f32(bench: &mut Bencher) {
    let a_values: [f32; 4] = [8.1239412, 931.20100, 5.531, 6.030100];
    let b_values: [f32; 4] = [9.0003, 20.202, 81325.20230, 195132.00999];
    bench.iter(|| {
        for _i in 1..1000000 {
            black_box([a_values[0] * b_values[0], a_values[1] * b_values[1],
                        a_values[2] * b_values[2], a_values[3] * b_values[3]]);
        }
    })
} 
```

还是在 SIMD:

```
fn simd_128_f32(bench: &mut Bencher) {
    unsafe{
        let a_values = _mm_set_ps(8.1239412, -931.20100, 5.531, -6.030100);
        let b_values = _mm_set_ps(9.0003, -20.202, 81325.20230, 195132.00999);
        bench.iter(|| {
            for _i in 1..1000000 {
                black_box(_mm_mul_ps(a_values, b_values));
            }
        });
    }
} 
```

标准方法在 670，925 ns /迭代中运行 100 万次乘法，并带有一个+/-。相比之下，SIMD 方法运行 315，533 ns /迭代，而+/-只有 32，635 ns。因此，速度提高了两倍多，但换来的是一点点不安全的代码。总的来说，我发现这在 SIMD 各职能部门中相当一致。大约 1.7 倍的速度一直是我对 SIMD 运营的标准，只要一切都转移到适当的数据结构中。

SIMD 在 Rust 中面临的最大挑战首先是将所有数据转移到所需的数据结构中——因此，如果您必须在`struct`、`vec`和`__mm`类型的数据结构之间转换所有数据，即使在数学密集型操作中，收益也会相对有限。考虑地球表面上两点之间的哈弗线距离的计算。可悲的是，性能提升将是有限的:我发现它们只在 8%的范围内。这是为什么呢？将数据移入和移出 SIMD 兼容结构以计算余弦函数的成本(由于 Rust 不支持英特尔固有函数`__mm_cos_pd`，必须在标准`vec`或其他数据结构上使用标准余弦函数来完成。

对于机器学习来说，了解 SIMD 是很有用的。对于非常低维的数据集，能够将数据组织成 4 的倍数可以显著提高性能。在评估 BLAS 和 Lapacke 时，将此作为基线也很有用。

### 什么是巴拉斯？在 Rust 里怎么用？

基本线性代数子程序(BLAS)是 1979 年作为一个 Fortran 库出现的低级例程，后来变得相当广泛。值得注意的是，虽然大多数平台都可以安装 BLAS，或者安装了某种形式的 BLAS(在 Mac 上，它与 Lapack 在 [*加速*](https://developer.apple.com/documentation/accelerate) 中合并)。BLAS 提供了非常标准的东西:点积之类的东西。然而，BLAS 的局限性在于它没有内置的功能来实现相反的功能。同样值得注意的是，虽然它非常快，但却不容易使用。

让我们用 BLAS 将两个矩阵相乘:

```
let mone = vec![
    2.0, 3.0,
    4.0, 5.0
];
let mtwo = vec![
    0.5, 1.0,
    1.5, 2.0
];
let mut result = vec![
    0.0, 0.0,
    0.0, 0.0,
];
unsafe {
    dgemm(b'N', b'N', 2, 2, 2, 1.0, &mone, 2, &mtwo, 2, 1.0, &result, 2);
} 
```

恭喜你！该乘法的结果现在存储在`result`中。我想我们中很少有人会把这个过程归类为特别愉快的。没看上去那么糟。前两个`b’N'`是我们如何指示 BLAS 的字符串类型 API 不要转置`mone`或`mtwo`。我们指定 m、n 和 k 维度总共是六个维度(都是两个)。接下来，我们提供与`mone`和`mtwo` ( `1.0`)相乘的标量。最后，我们提供了对输出变量的引用。

在经历了这种不便和依赖的痛苦之后，我们终于完成了一件了不起的事情。我们的代码现在运行速度又快了 2-4 倍，并且我们现在通过 NVIDIA 的 [cuBLAS](https://developer.nvidia.com/cublas) 无需进一步努力就可以获得 GPU 支持。

所以，太好了！让我们用这个做点什么。计算普通最小二乘法怎么样？不幸的是，我们可以接近——但我们不能完全到达那里。我们不能轻易地在 BLAS 中执行相反的操作。我们会继续向拉普卡前进。

### 关于拉普拉克的一切都在生锈

Lapack 很棒:它为许多对机器学习至关重要的操作提供了高性能的功能。不利的一面是，我们确实有点偏离常规，文档也很少。英特尔提供了关于这一主题的最佳文档，并且在能够[找到相关功能](https://software.intel.com/en-us/articles/intel-mkl-function-finding-advisor)方面击败了谷歌。而且，如果你认为 BLAS 有点笨拙，Lapack 在这方面更胜一筹。

因此，让我们执行一个混合 BLAS 和 Lapack 普通最小二乘(OLS)回归。我们会做一个相当标准的设置。

```
let (rows, cols) = (4, 1);
let scale_by = 1.0;
let xs = vec![
    2.0, 
    3.0,
    4.0, 
    5.0
];
let ys = vec![
    1.0,
  2.0,
    2.3,
    0.4
];
let mut xtx = vec![
    0.0
];

let mut xty = vec![
    0.0,
];

let mut result = vec![0.0]; 
```

然后，我们将开始做一些简单的 BLAS 乘法——我们将计算 X 转置 X 和 X 转置 Y

```
unsafe {
    dgemm(b'Y', b'N', cols, cols, rows, scale_by, &xs, cols, &xs, rows, scale_by, &result, rows);
    dgemm(b'Y', b'N', cols, cols, rows, scale_by, &xs, cols, &ys, rows, scale_by, &xty, rows);
} 
```

然后，我们需要首先使用 Lapack 来计算 LU 分解，以便得到 X 转置 X 的逆

```
// IPIV will be the pivot indices for the LU decomposition, with minimum dimensions of (m, n)
let mut ipiv = vec![
    0.0
];

// Then we'll need the inverse of xtx
unsafe {
    // In production, check status after each step
    let mut status = 0;
    // First we need to generate the LU decomposition of the XTX matrix
    dgetrf_(1, 1, &xtx, 1, &ipiv, status);
} 
```

一旦我们有了 LU 分解，并且值被存储在枢纽索引中，我们就可以计算它的倒数。

```
unsafe{
    // Then we can generate the inverse of the matrix from the LU decomposition
    let mut workspace = vec![0.0];
    dgetri_(1, &xtx, 1, ipiv, workspace, 1, status);
}
// XTX has now been overwritten by its inverse 
```

最后，我们将返回 BLAS 进行总结，并获得我们的结果

```
// Back to BLAS
unsafe{
    dgemm(b'Y', b'N', 1, 1, 4, scale_by, &xtx, 1, &xty, 4, scale_by, &result, 4);
} 
```

我们的结果现在很好地存储在名为`result`的`vec`中。虽然这个过程可能不简单，但是它很快，并且为如何执行底层操作提供了一个很好的基础。任何编程语言中的大多数高性能矩阵库最终都依赖于 Lapack 和/或 BLAS，了解我们的库的基础是很好的。

### 我们为什么要在乎？

性能很重要。摩尔定律正在终结，所以如果我们想继续繁荣的机器学习生态系统，我们需要学会优化我们的机器学习。与 Python 调用 C 相比，使用这种方法可以获得 4-10 倍的性能提升。这大约是摩尔定律在高端的六年改进。

Rust 也很好调试，严格类型鼓励执行数据工程时的良好实践。我敦促你:开始尝试将 Rust 用于你的机器学习。现在会有点沮丧，但在未来几年里会有回报的。如果你想了解更多关于 BLAS 和 Lapack 的知识，老实说，外面没有太多——如果你看到了什么，请发给我——我会更新这篇文章。