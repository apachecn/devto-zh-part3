# 代码 2018 的出现-第一天

> 原文：<https://dev.to/ballpointcarrot/advent-of-code-2018---day-1-2fko>

*最初发布于[https://www .ballpoint 胡萝卜. net/posts/advent-of-code-2018-1/](https://www.ballpointcarrot.net/posts/advent-of-code-2018-1/)——此外，作为我第一个发布:D 的开发者，必须获得“第一个帖子”积分*

在...的刺激下

1.  在这个空间中缺少帖子，
2.  向我的博客展示新的后端/主题的机会(我确实在没人注意的时候做了这个，并开始建立一个 RSS feeed！)，以及
3.  在发现[阿里·斯皮特尔](https://www.alispit.tel/#/)的这条推文后，

我决定做这个[【代码的来临】](https://adventofcode.com/)是要做的事情。首先，它给了我一个很好的练习机会。第二，它给了我一个在这里频繁发帖的理由(考虑到年份+缺乏内容，这可能不是一个坏主意)。最后，它帮助我构建了一些我不擅长的公共代码——即使它们是解决问题的方案。我将使用 GitHub gists 来发布我的解决方案，并将它们嵌入到这里。

### 问题第一

第一个问题总结如下:你是一个精灵，为了“拯救圣诞节”,穿越时空回到过去修复历史中的异常。然而，为了让时间旅行装置正常工作，它首先需要校准。

给定一系列“频率变化”，如`+1 -2 +3 -1`，将发生以下变化:

*   当前频率`0`，改变`+1`；结果频率`1`。
*   当前频率`1`，改变`-2`；结果频率`-1`。
*   当前频率`-1`，改变`+3`；结果频率`2`。
*   当前频率`2`，改变`+1`；结果频率`3`。

这提供了一个结果频率`3`。

有了这些规则，Code site 的出现让你登录(谢谢，用 Github 登录)并获得一个样例输入的链接。输入文件比我预期的要大，但是规则足够简单，可以产生这个小的 Clojure 片段:

```
(defn  calibrate  [input-file]  (let  [raw-input  (slurp  input-file)  freqs  (map  #(Integer.  %)  (clojure.string/split-lines  raw-input))]  (reduce  +  freqs))) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将文件读入一个字符串(使用`slurp`)，然后将每个数字(用换行符分隔)转换成一个 Java 整数。最后，通过`reduce`对所有内容进行汇总，以获得最终频率的全套调整。

### 问题二

在您的输入中，您现在需要跟踪活动值，并在第一次遇到相同值时查找两次。此外，这意味着您的列表可以循环；例如，以输入`+3, +3, +4, -2, -4`为例。当您第一次运行它时，您生成了中间值`3, 6, 10, 8 4`，并且在那个集合中没有匹配。所以，你取最后一个`4`偏移量，重新开始原来的列表，返回`7, 10, ...`。当您再次到达`10`时，您已经找到了您的“两次匹配”，并返回该值。现在，使用相同的测试输入，我们可以找到两次命中的第一个值。

Clojure 在这里利用了惰性无限序列的力量。使用 clojure [`cycle`](https://clojuredocs.org/clojure.core/cycle) 函数，频率列表可以无限重复。显然，我们不能急切地加载一个无限值的列表——没有足够的内存空间来这么做；然而，我们*能够*做的是让列表按需填充，只提供下一个
号，直到我们需要它。

我构建了一个函数来生成中间点的列表，并循环寻找解决方案::

```
(defn  frequency-adjustments  [freqs]  (reductions  +  (cycle  freqs)))  (defn  calibrate  [freqs]  (loop  [n  2]  (let  [steps  (take  n  (frequency-adjustments  freqs))  step-counts  (frequencies  (rest  steps))  repeats  (filter  (fn  [[k  v]]  (if  (=  v  2)  k))  step-counts)]  (if-not  (empty?  repeats)  (first  (keys  repeats))  (recur  (inc  n)))))) 
```

Enter fullscreen mode Exit fullscreen mode

我对事情的发展很满意。我去了一家 REPL，用一些样本测试了这个逻辑，似乎得到了我想要的答案。实际上，这很好，因为你可以有效地看到每个步骤是如何进行的新列表
，这对可视化分析很有帮助。然后，我用测试数据来启动它。

等待着。事实上有一段时间了。

事实证明，当你有足够多的输入时，试图一遍又一遍地构建这个列表对进行计算的系统来说有些费力。显然，这不是我想要的解决方案。在这一点上，我做了一点欺骗(嘿，这是互联网，我并不自豪)，并检查了[Reddit 帖子](https://www.reddit.com/r/adventofcode/comments/a20646/2018_day_1_solutions/)，人们在那里张贴解决方案，并遇到了一个单独的 Clojure
解决方案，由 [zqvt](https://www.reddit.com/r/adventofcode/comments/a20646/2018_day_1_solutions/eau9gjm/) (稍微
修改以适应我下面的输入参数):

```
(defn  calibrate2  [freqs]  (loop  [xs  (cycle  freqs)  seen  #{}  total  0]  (if  (contains?  seen  total)  total  (recur  (rest  xs)  (conj  seen  total)  (+  total  (first  xs)))))); 
```

Enter fullscreen mode Exit fullscreen mode

这将生成一组我们遇到的值，并在我们生成
时保持我们所处位置的运行总数。这里我甚至没有想到(更不用说想到可能)的关键是使用`rest`来生成
进一步的序列，而不包括已处理的项目。这很聪明，这个解决方案很快提供了一个
响应。

吸取的教训:

1.  我最初的想法是构建一组我在列表中看到的用`reductions`计算的值，但是我真的想尝试一些不用在循环之外维护状态的方法。这导致我每次都要重新生成列表，这使得它无法有效运行。我可能应该采用更简单的思考过程，因为这样会更快(我在解决方案上花费的时间也会更少)。
2.  很高兴再次张贴东西，实践总是有帮助的。
3.  诚实——坦白寻求解决方案是很痛苦的，但有时你必须忍气吞声。此外，在看到我的实现与我发现的实现之间令人痛苦的性能对比后，我感到非常愚蠢。但是我想强调一个成功的实现，一方面展示有好的方法来做这件事，另一方面给我自己一些东西来期待改进。我希望下次不需要拐杖。