# 大 STL 算法教程:简介

> 原文：<https://dev.to/sandordargo/the-big-stl-algorithms-tutorial-introduction-2kph>

我已经写了很多关于 C++ 11 引入的特性以及它如何改变了我对这门语言的看法的文章。我最喜欢的功能可能是 lambda 表达式。我不喜欢它们纯粹的存在，这不是为了艺术而艺术，但是使用不同的 STL 算法真的很有帮助。如果你想快速了解所有的 105，看看[fluentcpp.com](https://www.fluentcpp.com/)的主人[的这个视频](https://www.youtube.com/watch?v=2olsGf6JIkU)。

由于 lambdas 和 STL 算法的结合，我写 C++代码的方式发生了很大的变化。然而，我知道我没有很多，我想提高我的 STL 知识。最好的学习方法是什么？要么通过做，要么通过教。我已经在做了，所以现在我开始写一系列关于 STL 算法的文章。

我还不知道，我会多久写一次，一篇文章会涉及多少，但是我写的每两篇技术文章都是关于 STL 算法的——发表的顺序是另一个问题。

我们开始吧！

我们将要讨论的算法基本上是一组函数，我们可以很好地与 STL 容器一起使用，另一个共同点是它们都可以在头文件中找到。

根据[cplusplus.com](http://www.cplusplus.com/reference/algorithm/)，我们可以将他们分为 8 类，再加上其他:

*   非修改序列操作(例如 all_if、any_of、find)
*   修改序列操作(例如，copy、copy_if、transform)
*   分区(例如 partition，is_partition)
*   排序(例如，sort，is_sorted)
*   二分搜索法(例如，二进制搜索、下界、上界)
*   合并(例如 Merge，set_union)
*   堆(例如，push_heap、pop_heap)
*   最小值/最大值(例如最小值、最大值...)
*   其他人

我会在一篇文章中展示一些组，比如 min/max，但是其他一些更大的组，比如*非修改序列操作*我会分解成更小的块。

敬请期待！

*本文最初发表在[我的博客上。](http://sandordargo.com/blog/2019/01/30/stl-algos-intro)T3】*