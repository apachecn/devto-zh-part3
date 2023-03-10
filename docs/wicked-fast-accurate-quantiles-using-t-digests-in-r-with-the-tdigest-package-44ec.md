# 通过{tdigest}包在 R 中使用“t-Digests ”,获得快速、准确的分位数

> 原文：<https://dev.to/hrbrmstr/wicked-fast-accurate-quantiles-using-t-digests-in-r-with-the-tdigest-package-44ec>

[@ted_dunning](http://twitter.com/ted_dunning) [最近更新了](https://twitter.com/ted_dunning/status/1073802202639036416?s=20)他在 2013 年创建的 t-Digest 算法。这个“t-digest”是什么？从根本上说，它是一种概率数据结构，用于估计任何百分比的分布式/流式数据。Ted 在这个简短的视频中非常优雅地解释了这个问题:

所述视频也有完整的文字记录。

T-digests 已经被纳入许多“大数据”分析生态系统有一段时间了，但我还没有看到它们的任何 R 包(如果你知道一些的话，请在评论中参考任何一个),所以我将 ajwerner 的一个低级实现库[打包到一个小的 R 包中，这个包很无聊，但被恰当地命名为`tdigest`:](https://github.com/ajwerner/tdigestc)

*   [来源帽子](https://git.sr.ht/~hrbrmstr/tdigest)
*   [GitLab](https://gitlab.com/hrbrmstr/tdigest)
*   CINC(CINC 回购公司有适用于 Windows 和 macOS 的 R 3.5 二进制软件包。)
*   [到](https://github.com/hrbrmstr/tdigest)

有用于低级累加器和分位数/值提取器的包装器，以及用于创建 t-digest 对象并从中检索分位数的矢量化函数(包括用于`stats::quantile()`的`tdigest` S3 方法)。

这:

```
install.packages("tdigest", repos="https://cinc.rud.is/") 
```

Enter fullscreen mode Exit fullscreen mode

将从源代码或二进制文件安装到您的系统上。

### 基本操作

低级接口在“流”操作中更有用(即随着时间积累输入):

```
set.seed(2019-04-03)

td <- td_create()

for (i in 1:100000) {
  td_add(td, sample(100, 1), 1)
}

quantile(td)
## [1] 1.00000 25.62222 53.09883 74.75522 100.00000 
```

Enter fullscreen mode Exit fullscreen mode

### 更多的 R 类 Ops

矢量化是 R 中游戏的名称，我们可以使用`tdigest()`以矢量化的方式工作:

```
set.seed(2019-04-03)

x <- sample(100, 1000000, replace=TRUE)

td <- tdigest(x)

quantile(td)
## [1] 1.00000 25.91914 50.79468 74.76439 100.00000 
```

Enter fullscreen mode Exit fullscreen mode

### 需要速度

t-digest 算法是为流操作和速度而设计的。真漂亮，织补得快:

```
microbenchmark::microbenchmark(
  tdigest = tquantile(td, c(0, 0.01, 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 0.99, 1)),
  r_quantile = quantile(x, c(0, 0.01, 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 0.99, 1))
)
## Unit: microseconds
## expr min lq mean median uq max neval
## tdigest 22.81 26.6525 48.70123 53.355 63.31 151.29 100
## r_quantile 57675.34 59118.4070 62992.56817 60488.932 64731.23 160130.50 100 
```

Enter fullscreen mode Exit fullscreen mode

注意“准确”和“精确”不是一回事，所以 R 中的常规分位数运算将接近 t-digest 计算的结果，但并不总是完全相同。

### 鳍

这是一个快速(但完整)的包装，可以使用一些轮胎踢。我想给 C 实现添加序列化，这样我就可以在 R 端启用[反]序列化，因为这将(在我看来)使 t-digest 操作在 R-context 中更有用，尤其是因为你可以合并两个不同的 t-digest。

像往常一样，在你想要的地方写代码/PR，并提出任何想要的功能/增强的问题。

此外，无论是谁开始使用包名的大括号符号(例如{ggplot2}):太棒了！