# 纯文本的力量

> 原文：<https://dev.to/korenmiklos/the-power-of-plain-text-1gb4>

我有时会对存储数据的二进制文件格式感到兴奋。几年前，它是 HDF5。现在[阿帕奇实木复合地板](https://parquet.apache.org/)看起来很有前景。但是我的大部分数据工作，尤其是当我与他人分享时，都是以简单的纯文本形式存储的。

> 我相信可移植性和易于探索在任何时候都胜过严格的符合模式的数据库。

无论是 CSV、JSON 还是 YAML，我喜欢快速浏览数据的感觉。

```
head -n100 data.csv
wc -l data.csv 
```

是我经常使用的命令。没有什么比一份优秀的 YAML 文档更具可读性了。

当然，性能有时是一个问题。如果您经常读写几千万行，您可能不想使用纯文本。但是在我们的大多数用例中，一个数据产品可能被它的开发者一天读写几次，然后与几个读一两次的用户共享。方便分享和发现比节省一些字节更重要。你可以随时随地。(从来没有 rar 或者 7z 之类的。你真的指望我装个 app 就为了看你的数据？)

[![](img/f066a1c75dcac02c7571374e2dbb6c70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kxOD5PpG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ni9gxpezx7dpno6wjfdx.jpeg)

除了大小(大)和速度(慢)，CSV 文件还有三个问题:

1.  没有标准定义。所有的字符串都应该用引号括起来吗？引号内的引号会发生什么？永远不要编写自己的 csv 解析器。会有你没有想到的[特例](https://chriswarrick.com/blog/2017/04/07/csv-is-not-a-standard/)。使用像 [Python3 csv](https://docs.python.org/3/library/csv.html) 或[熊猫](https://pandas.pydata.org/)这样的标准库。
2.  字符编码。和所有纯文本文件一样，你必须意识到没有纯文本这种东西。你的文件只是一个字节序列，你必须告诉你的计算机[你的字节是什么意思](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)。在我们的日常工作中，转换到 UTF-8 是头等大事。
3.  没有模式。这是一个很头疼的问题。该列是字符串吗？约会吗？我一直在纠结前导零和奇怪的日期格式。(但我也会对这些专有数据格式感到困惑。任何编程语言都不可能记住日期/时间函数。)我在 [Cerberus](http://docs.python-cerberus.org/en/stable/) 中试验过模式验证，看起来很酷，但是我们还没有采用任何正式的东西。

那么，尽管存在这些问题，为什么我还是喜欢纯文本数据呢？我相信可移植性和易于探索在任何时候都胜过严格的符合模式的数据库。(请注意，我不是在银行工作。或者医疗保健。)看看你的数据是什么，玩玩吧。