# 日常编码问题#3

> 原文：<https://dev.to/cwetanow/daily-coding-problem-3-73i>

有很长一段时间，我发现下班回家后我的时间变得越来越少。直到几天前，我偶然发现了[日常编码问题](https://www.dailycodingproblem.com/) (DCP)，并决定尝试一下。
代码是用 C#写的。

## 它是如何工作的？

DCP 的人会给你一个顶级公司每天都会问的问题，让你去解决。高级会员还提供了验证您的解决方案的机会。

# 问题二

这个问题是 Google 问的。

给定二叉树的根，实现 serialize(root)和 deserialize(s)，serialize(root)将树序列化为字符串，deserialize(s)将字符串反序列化回树。

例如，给定以下节点类

类节点:def init(self，val，left=None，right = None):self . val = val self . left = left self . right = right 下面的测试应该通过:

node = Node('root '，Node('left '，Node('left.left ')，Node(' right '))assert deserialize(serialize(Node)). left . left . val = = ' left . left '

# 我的解决方案

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Task03
{
    public class Program
    {
        private const string Empty_Marker = "1";

        public static void Main(string[] args)
        {
            var node = new Node("root", new Node("left", new Node("left.left")), new Node("right"));

            var serialized = Serialize(node);

            Console.WriteLine(serialized);
            node = Deserialize(serialized);

            Console.WriteLine(node.Left.Left.Value);
        }

        public static string Serialize(Node node)
        {
            if (node == null)
            {
                return Empty_Marker + '-';
            }

            var builder = new StringBuilder();

            builder.Append($"{node.Value}-");

            builder.Append(Serialize(node.Left));
            builder.Append(Serialize(node.Right));

            return builder.ToString();
        }

        public static Node Deserialize(string serializedNode)
        {
            var nodes = serializedNode
                .Split('-', StringSplitOptions.RemoveEmptyEntries)
                .ToArray();

            var queue = new Queue<string>(nodes);

            var node = DeserializeNode(queue);

            return node;
        }

        private static Node DeserializeNode(Queue<string> nodes)
        {
            if (nodes.Peek() != null)
            {
                var nextNode = nodes.Dequeue();

                if (nextNode == Empty_Marker)
                {
                    return null;
                }

                var node = new Node(nextNode);

                node.Left = DeserializeNode(nodes);

                node.Right = DeserializeNode(nodes);

                return node;
            }

            return null;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

# 解释

对于序列化，我们首先遍历树深度，并将当前节点的值添加到序列化的字符串中。当我们到达一个空节点时(基本上它的父节点没有子节点)，我们放置一个`Empty_Marker`来表示这里没有节点。假设在任何节点的值中不使用符号`Empty_Marker`和'-'，这是可行的。

对于反序列化，我们分割给定的字符串并将结果放入一个队列中。然后首先构建树的深度，通过队列的第一个元素并在到达`Empty_Marker`时停止

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