# 日常编码问题#2

> 原文：<https://dev.to/cwetanow/daily-coding-problem-2-21pj>

看到第一个帖子获得了一些人气，这里是第二个问题

有很长一段时间，我发现下班回家后我的时间变得越来越少。直到几天前，我偶然发现了[日常编码问题](https://www.dailycodingproblem.com/) (DCP)，并决定尝试一下。
代码是用 C#写的。

## 它是如何工作的？

DCP 的人会给你一个顶级公司每天都会问的问题，让你去解决。高级会员还提供了验证您的解决方案的机会。

# 问题二

这个问题是优步问的。

给定一个整数数组，返回一个新数组，使得新数组中索引 I 处的每个元素都是原始数组中除 I 处的元素之外的所有数字的乘积。

例如，如果我们的输入是[1，2，3，4，5]，那么预期的输出将是[120，60，40，30，24]。如果我们的输入是[3，2，1]，那么预期的输出将是[2，3，6]。

追问:不能用除法怎么办？

# 我的解决方案

```
using System;
using System.Linq;

namespace Task02
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var input = Console.ReadLine()
                .Split(' ')
                .Select(int.Parse)
                .ToArray();

            var result = new int[input.Length];

            for (int i = 0; i < result.Length; i++)
            {
                result[i] = 1;

                for (int j = 0; j < result.Length; j++)
                {
                    if (j != i)
                    {
                        result[i] *= input[j];
                    }
                }
            }

            Console.WriteLine(string.Join(' ', result));
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 解释

在这里我想不出任何比天真的解决方案更好的了，它在 O(n^2)时间和你永远不喜欢平方复杂性。
基本上对于结果数组中的每一项，foreach 输入数组并检查索引是否与该项不同。如果是，将该项目的当前值乘以当前输入项

我会每天努力解决日常问题，但有时生活会妨碍我:)
解决方案会发布在我的 github 账户上

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ cwetanow ](https://github.com/cwetanow) / [日常编码问题](https://github.com/cwetanow/DailyCodingProblem)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 每日编码问题 [![Build Status](img/b5837f05653334b156e36dc624c372b3.png)](https://travis-ci.org/cwetanow/DailyCodingProblem)

这个存储库包含了[日常编码问题](https://www.dailycodingproblem.com/)任务的解决方案

</article>

[View on GitHub](https://github.com/cwetanow/DailyCodingProblem)

.

如果我应该继续张贴我的解决方案，请随意留下赞，并在评论中让我知道。

如果你也有什么更好的解决方案，一定要分享出来，这样我们可以互相学习。