# 日常编码问题#1

> 原文：<https://dev.to/cwetanow/daily-coding-problem-1-23e0>

有很长一段时间，我发现下班回家后我的时间变得越来越少。直到几天前，我偶然发现了[日常编码问题](https://www.dailycodingproblem.com/) (DCP)，并决定尝试一下。
代码是用 C#写的。

## 它是如何工作的？

DCP 的人会给你一个顶级公司每天都会问的问题，让你去解决。高级会员还提供了验证您的解决方案的机会。

# 问题 1

这个问题是最近谷歌问的。

给定一个数字列表和一个数字 k，返回列表中任意两个数字的总和是否为 k。

例如，给定[10，15，3，7]和 17 的 k，返回 true，因为 10 + 7 是 17。

额外收获:你能一次完成吗？

# 我的解决方案

```
using System;
using System.Collections.Generic;
using System.Linq;

namespace Problem01
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var numbers = Console.ReadLine()
                .Split(' ')
                .Select(int.Parse)
                .ToList();

            var k = int.Parse(Console.ReadLine());

            var result = TwoNumbersEqual(numbers, k);
            Console.WriteLine(result);
        }

        public static bool TwoNumbersEqual(List<int> numbers, int k)
        {
            var set = new HashSet<int>();

            foreach (var number in numbers)
            {
                var remaining = k - number;

                if (set.Contains(remaining))
                {
                    return true;
                }

                set.Add(number);
            }

            return false;
        }
    }
} 
```

# 解释

简单的解决方案是做两个嵌套的 for 循环，并检查是否有任何组合给出了我们想要的`k`。
但是因为我想要奖金标签，经过一番思考，我意识到你可以对输入数组进行 foreach，并对每一项进行检查，找出它和`k`之间的区别。在您发现差异之后，您可以检查您之前是否已经见过这个数字，并且`HashSet`数据结构允许不断查找(因为它是一个散列表)。

我会每天努力解决日常问题，但有时生活会妨碍我:)
解决方案会发布在我的 github 账户上

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ cwetanow ](https://github.com/cwetanow) / [日常编码问题](https://github.com/cwetanow/DailyCodingProblem)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 每日编码问题 [![Build Status](img/bc1bd3ac4f7a7b8202a2b2dcaa092a9d.png)](https://travis-ci.org/cwetanow/DailyCodingProblem)

这个存储库包含了[日常编码问题](https://www.dailycodingproblem.com/)任务的解决方案

</article>

[View on GitHub](https://github.com/cwetanow/DailyCodingProblem)

.

如果我应该继续张贴我的解决方案，请随意留下赞，并在评论中让我知道。

如果你也有什么更好的解决方案，一定要分享出来，这样我们可以互相学习。