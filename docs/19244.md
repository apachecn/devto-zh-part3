# AoC 第 2 天:库存管理系统

> 原文：<https://dev.to/rpalo/aoc-day-2-inventory-management-system-537i>

好的，第一天棒极了，我对人们发布的所有解决方案感到非常兴奋。我学到了很多！我们在 DEV 排行榜上已经有了 20 人，这意味着还有 180 人可以加入，请使用代码 224198-25048a19！

第二天开始！

* * *

代码问世的第二天，我很确定谷歌已经厌倦了我每隔 15 秒就问它一个问题“我如何在 Rust 中做 X”

今天的挑战涉及库存系统。箱子上的 id 是一堆乱七八糟的字母，我们有一仓库的箱子要检查。第一部分要求我们分析每个 ID 中字母的出现频率。第二部分进入[海明距离](https://en.wikipedia.org/wiki/Hamming_distance)，这是指导[练习](https://exercism.io)后熟悉的场景。

我让两个部分都工作了，甚至第二部分也运行得很快，但我对双循环([【o(n^2】)](https://dev.to/basecspodcast/s1e6--a-friendly-intro-to-big-o-notation))运行时的算法不满意。有人想出什么更有效的方法吗？

我要像其他酷孩子一样在评论里贴出我的解决方案。

其他人怎么样？