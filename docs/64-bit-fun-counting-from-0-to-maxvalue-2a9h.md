# 64 位乐趣。从 0 计数到最大值

> 原文：<https://dev.to/codeguppy/64-bit-fun-counting-from-0-to-maxvalue-2a9h>

64 位计算在几十年前是超前的。除了大型机和其他专用计算机之外，最近的所有个人计算机都有数据路径宽度(寄存器等)。)8 位或 16 位。32 位的引入是个人计算机领域的一次重大革命。

如今，几乎每个 CPU 都支持 64 位操作。即使是低功率的移动处理器现在也是 64 位。在 64 位架构中，CPU 可以使用 64 位寄存器处理/存储更多信息，并寻址更多内存。

## 64 位有多大？

因为这是一篇有趣的文章，我不会深入 64 位算法的数学，也不会深入 64 位架构的计算机科学。相反，我将建议在汇编语言或任何支持 64 位数据类型的编程语言中做一个小的比较测试:

从 0 计数到变量中可以存储的最大值。第一次使用 32 位宽的变量，第二次使用 64 位宽的变量。试着估计一下结果。

在 C#中，对于 32 位变量(...请忽略一个问题):

```
for (uint i = 0; i < uint.MaxValue; i++)
{
    Process(i);
} 
```

uint。MaxValue 是将变量的所有位设置为 1 时获得的数字。它基本上等于 2^32–1 = 4294967295。这大约是 42 亿。

64 位变量情况下的测试代码类似:

```
for (ulong i = 0; i < ulong.MaxValue; i++)
{
    Process(i);
} 
```

乌龙。MaxValue 是本次 2^64–1 = 18446744073709551615(20 位数)。

以每秒大约 10 亿次 Process()操作的速度，第一个程序将在以下时间完成其工作:

*   2^32 / 1000000000 = 4.29 秒

在相同的处理速度下，第二个程序将在以下时间内完成其工作:

*   2^64/1000000000/3600/24/365 = 585 年！

那是相当不同的！！！谁有时间等 585 年来完成它的工作！

有趣的是，每秒 10 亿次运算是非常具有侵略性的！对于一个更现实的场景，每秒执行一百万到几亿次操作，执行一次操作需要几千甚至几十万年的时间！！！

对于大多数人来说，上面的练习是一个有趣的 64 位能力的可视化。但这个练习也显示了计算机科学工程师在处理算法时所面临的现实。一个执行得不好的算法(例如搜索或计算问题)，采用暴力方法，很可能会毁掉你的软件。

## 其他大数字

概括一下，记住 64 位寄存器/变量中存储的最大数是 2^64–1 = 18446744073709551615(一个 20 位数)。虽然这个数字看起来很大，但是与 1*googol*10^100(1 后面跟着 100 个零)相比，这个数字仍然很小！

而且连大 googol 都比 70 小！(70 阶乘，即 1 * 2 *...* 70).哇！这才真正体现了乘法的强大！

您知道吗:googol 完全在 *double* 数据类型(IEEE 754 浮点)的范围内——也是 64 位结构。T2 double T3 如何存储如此大的数字将在以后的文章中呈现。

我希望你玩得开心！

> 更多有趣的 JavaScript 活动请查看[https://codeguppy.com](https://codeguppy.com)