# 线性时间的乐趣:我最喜欢的算法

> 原文：<https://dev.to/healeycodes/any-of-y-all-have-a-favorite-algorithm-here-s-mine-3kki>

### 一种线性时间多数表决算法

我喜欢这个算法，因为它令人惊讶的*和*平易近人。我第一次看到它是在一个 LeetCode 讨论帖上，它让每个人都大吃一惊。有些人很生气。 [169。多数元素](https://leetcode.com/problems/majority-element/)。我很想在评论中听到你最喜欢的算法！

这个问题在竞争性的编码网站上很常见，有一个解决方案在 1980 年就被发现了，但是直到 1991 年才发表，因为它强调了 Fortran 和机械验证。

**问题:**给定一张多数票的名单( *n* /2 + 1)，宣布领先者。最常出现的投票。有可能在线性时间 *O(n)* 和常数空间 *O(1)* 得到结果。

示例:

`[0, 1, 1, 0, 1, 0, 1, 1] => 1 is the majority element`

`['a', 'b', 'a', 'c', 'b', 'c', 'a', 'a'] => 'a' is the majority here`

一个简单的解决方案可能是这样的。我们将使用字典来跟踪所有的投票，并存储我们看到的最高票数。

```
def majority_vote(votes):
    leader = None
    max_votes = 0
    candidates = dict()

    for i in votes:
        # if seen before
        if i in candidates:
            # count their vote
            candidates[i] += 1
            # and check if they're leading
            if candidates[i] > max_votes:
                leader = i
                max_votes = candidates[i]
        else:
            candidates[i] = 1

    return leader 
```

以上利用线性空间 *O(n)* 在线性时间 *O(n)* 内实现了一个正确解。我们可以做得更好。一次通过，**没有**计算每一个元素。

罗伯特·s·博耶(Robert S. Boyer)和 J·斯特罗瑟·摩尔(J Strother Moore)于 1980 年在 SRI International 的计算机科学实验室发现了一种快速多数表决算法。他们正在协助一位从事容错工作的同事。

在他们幽默的论文中，他们想象了一个挤满选民的会议中心，举着他们所选候选人名字的标语牌。每个投票者代表列表的一个索引。

> 假设接着发生了一场地板大战

他们认为投票人可能会同时击倒对方，只支持对方。混乱过后，剩下的选民将代表大多数。

> 这里有一个不流血的方法，主席可以模拟配对阶段。

他们的算法通过移除数据结构(字典)改进了我们的简单解决方案。在这里从 Fortran 转换成 Python。

```
def majority_vote_improved(votes):
    # after one vote, we have a leader
    leader = votes[0]
    count = 1

    for i in range(1, len(votes)):
        # the lead may grow
        if votes[i] == leader:
            count += 1
        else:
            # or shrink
            count -= 1

        # and they may be replaced
        if count == 0:
            leader = votes[i]
            count = 1

    return leader 
```

[![a graph of this algorithm](img/010e23ecefd302423bbcca0d12602a43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5sL2AI4E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ewcdmpc5598nvostej6z.png)

因此，大多数是在线性时间 *O(n)* 和恒定空间 *O(1)* 中发现的。

在摩尔的[网站](https://www.cs.utexas.edu/~moore/best-ideas/mjrty/index.html)上查看一步一步的演练。更多关于[维基百科](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)的信息。

> 指定 MJRTY 和获得 61 个验证条件的全部工作需要大约 20 个工时。[...]大约 55 分钟的计算机时间来证明 66 个定理的最终列表。

<small>mjr ty——一种快速多数表决算法，与 R.S. Boyer。在 R.S. Boyer(编辑。)，自动推理:纪念伍迪·布莱索的论文，自动推理系列，Kluwer 学术出版社，荷兰多德雷赫特，1991 年，第 105-117 页。</small>

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。