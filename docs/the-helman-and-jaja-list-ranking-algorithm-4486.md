# Helman 和 JáJá List 排序算法

> 原文：<https://dev.to/downey/the-helman-and-jaja-list-ranking-algorithm-4486>

***注:**我最初在我的[个人博客](https://downey.io/blog/helman-jaja-list-ranking-explained/)上发表了这篇文章，但是我想在这里开始分享一些[omsc](http://www.omscs.gatech.edu/)的相关内容。我认为这篇文章是一个很好的开始！* ☺️

在佐治亚理工学院[高性能计算简介](https://www.omscs.gatech.edu/cse-6220-intro-hpc)课程的一个实验室中，我们不得不探索[列表排序](https://en.wikipedia.org/wiki/List_ranking)的并行计算问题。Vuduc 教授的这个[讲座](https://www.youtube.com/watch?v=0m1VOexMhlw)比我解释得更好，但是列表排序本质上只是遍历一个链表，并给每个节点分配一个“等级”,或者到列表头部的距离。列表排序的串行实现是琐碎的，并且可以通过遍历列表在 *O(n)* 时间内完成。并行实现有点棘手。在我们的实验室中，我们研究并实现了两个这样的算法:Wyllie 的算法和 Helman/JáJá的算法。我发现 Wyllie 的算法很容易搜索，但 Helman 和 JáJá的算法就不同了。感觉在学术论文之外缺乏对算法的简单解释，所以这篇文章是我试图纠正这一点。

## 数组池表示链表

为了并行操作和访问链表，我们需要重新考虑它的表示。它不能简单地是一个松散的节点集合，其中包含指向彼此的指针，因为没有办法发现/访问不按顺序排列的节点。相反，我们需要用数组来表示链表。Vuduc 教授称之为[数组池](https://www.youtube.com/watch?v=M4Zsh5OuB5Y)表示。起初我发现这种命名令人困惑，因为它让我想到了由一堆数组组成的[对象池](https://en.wikipedia.org/wiki/Object_pool_pattern)。不过，不是那样的。它是一个列表节点池，恰好由一个数组表示。不管名称如何，使用数组池，让我们用一块连续的内存来表示一个链表。考虑下面的阵列:

[![](img/3ec514bb37ec04aaa8c708266033b7c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PY3ngcyl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p2jtxsmuncbp146jyolb.png)

`Successors`数组表示链表中每个节点的“下一个”指针，每个“下一个”指针对应于数组中的一个索引。`List Ranks`数组表示链表中给定索引处每个节点的“值”。或者换句话说，`Successors[0]`和`List Ranks[0]`都是指同一个逻辑链表节点，它们只是包含了节点的不同属性。

为了更容易形象化，在上面的例子中，每个节点都用它的列表排名作为它的值。然而，实际上，节点的值可以是任何值。对于下面的算法描述，我们将假设我们的实现使用数组池来表示列表。

## 词汇

我将始终使用以下变量:

*   ***P* -** 处理器数量
*   ***n* -** 列表中的节点数
*   ***s* -** 子列表数

## 威利的榜单排名算法

Wyllie 的算法采用分而治之的方法，通过一种称为指针跳转的技术不断地将列表分成更小的子列表。[这个讲座](https://youtu.be/WWst31ORiDI)很好地解释了算法，网上有很多资源，所以我不会讲太多细节。在[功和跨度分析](https://en.wikipedia.org/wiki/Analysis_of_parallel_algorithms)方面，Wyllie 的算法跨度为 *O(log <sup>2</sup> n)* ，功为 *O(nlogn)* 。正如你所看到的，它必须比串行算法做更多的工作，所以你需要有一个大的值`n`和大量的可用处理器来使 Wyllie 的方法有价值。这就是 Helman 和 JáJá算法的用武之地。

## Helman 和 JáJá的列表排序算法

Helman 和 JáJá在他们的论文[中描述了他们对跨度为 *O(n/P)* 的列表排序问题的方法和 *O(n)* 的工作【为对称多处理器设计实用有效的算法](https://www.cc.gatech.edu/~bader/COURSES/UNM/ece638-Fall2004/papers/HJ99.pdf)(有关算法性能分析的更多信息，我推荐查看[这篇论文](https://pdfs.semanticscholar.org/f95a/7864e95184180a6b55357149b3712b5aa73f.pdf))。然而，我发现他们的论文相当密集，并意识到在网上缺乏对此的平易近人的解释。这里是我一步一步解释它的尝试。

### 将列表分割成子列表

通过随机选择至少 *P - 1* **子列表头**以及列表的真实头来创建 *s* 子列表，将链表分割成子列表。理论上，这将允许我们在 *P* 处理器之间划分每个子列表的顺序列表排序。然而，由于我们是随机选择子列表头的，因此有可能工作分配不均。因此，在实践中，我实际上看到了性能的提高，方法是选择大于 P 的值，并允许 OpenMP 在处理器之间进行负载平衡。在下图中，除了列表的真实标题(索引 2)之外，我们还随机选择了 2 个子列表标题(索引 6 和 8):

[![](img/eb19010a23bcf18e5927e01f511ae8da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D-khtFCM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rb9zi9xrf4b0t3m8e55l.png)

### 并行遍历每个子列表，计算部分列表排名

现在，并行地，我们可以遍历每个子列表——当到达另一个子列表的开头时停止。在下图中，假设绿色、蓝色和橙色遍历发生在不同的处理器上。

[![](img/6068e91c78585e3ec5c6b448eaa38669.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j2M31dBX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qhvmljmdhwd5ntxyqv4m.png)

在遍历这些子列表时，我们依次计算每个处理器上的子列表的列表排名。完成后，我们应该在我们的`List Ranking`数组中有这些部分排名。每当我们到达一个子列表的末尾时，我们记录当前子列表的总排名和下一个子列表头的索引，用于下一步。请参见下图，了解我们的`List Ranking`数组的当前状态:

[![](img/95296112698e3a9530aea5deca5f32b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bNhTAqGK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k7dmbbdowdfofanoiikg.png)

### 依次计算子列表头的列表排名

如前所述，我们应该已经记录了每个子列表的总排名以及下一个子列表头部的索引。我们可以使用这些数据来依次计算每个子列表的起始等级偏移量。

[![](img/cc3d3e1bfa26351d36999a9837555311.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GnnFk4M9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e9pvb60ic35ph0kukt5e.png)

如上图所示，我们可以按照正确的顺序遍历子列表的头部，因为我们记录了每个子列表的后续索引。因为我们也记录了每个子列表的总排名，所以我们可以使用一个[前缀 sum](https://en.wikipedia.org/wiki/Prefix_sum) 操作来累加这些值。

### 并行应用子列表头等级偏移

现在，并行地，我们可以将我们为每个子列表头计算的等级偏移应用于每个子列表中的所有节点。下图展示了这可能是什么样子:

[![](img/d1e64f6e85d30c2b5134899f4091e2e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q-8tTLCF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/59dghionaem3dj31g7nw.png)

如果您已经做了一些基本的簿记工作来跟踪一个节点属于哪个子列表，那么这可以通过一个简单的`parallel for`循环一次遍历所有元素的大规模并行操作来完成。正如您在上图中看到的，这导致了包含链表的最终列表排名值的`List Ranks`数组。

## 其他资源

希望这个解释对你有所帮助。如果您仍然感到困惑，以下资源在实施过程中对我有所帮助。

1.  [为对称多处理器设计实用有效的算法(摘自 Helman 和 Jájá论文)](https://www.cc.gatech.edu/~bader/COURSES/UNM/ece638-Fall2004/papers/HJ99.pdf)
2.  在 SMP 和 MTA 共享内存系统上比较列表排序和连通分量算法的性能
3.  [列出多核系统排名](https://eprints.ucm.es/11387/1/HugoDocument.pdf)

如果你对这篇文章有任何建议或反馈，请随时[发帖](https://github.com/tcdowney/downey-io-jekyll/issues)。永远记住，要有远大的梦想！

[![](img/aa6bbbcb8e1a158d043f1d6e7f0ed238.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tbp0Npj9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t7l5idv7lm7iuy0xv0jd.jpg)