# 如何在 JavaScript 中正确洗牌

> 原文：<https://dev.to/niinpatel/how-to-correctly-shuffle-an-array-in-javascript-19dl>

大约 9 年前，TechCrunch 发表了这个故事。微软在 browserchoice.eu 上有一个浏览器投票屏幕，微软应该以随机的顺序向用户展示所有不同的浏览器选择。

<figure>

[![microsoft ballot screen on browserchoice.eu](img/5ffe0f2a57f2edcde276534bef3e75ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TTA_KdiO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i64jopjl0j8fanpjt5mp.png)

<figcaption>"randomly" sorted</figcaption>

</figure>

只不过，这绝不是随机的。大约有 50%的时间，ie 浏览器出现在最后一个位置，而 Chrome 最有可能出现在前三名。怎么回事？这是他们用来随机洗牌的代码:

```
array.sort(function (a, b) { return 0.5 — Math.random() }) 
```

乍一看，这似乎是一个合理的解决方案。事实上，如果你在谷歌上搜索“随机洗牌 javascript ”,这段代码会出现在最上面。这段代码使用 javascript 的 sort 函数和一个定制的比较器。这个比较器的数值在 0.5 和-0.5 之间。

然而，这种排序算法的一个问题是它不起作用。如果你想要一个随机洗牌，其中每一个排列都是同样可能的。这种算法在这方面非常失败。[这里有一个关于这个算法结果的有趣的统计分析](http://www.robweir.com/blog/2010/02/microsoft-random-browser-ballot.html)。

除了是错误的，它也是非常低效的。正则排序函数的时间复杂度为 O(n.logn)。但是在这种情况下，排序函数的排序非常不一致。因此，根据用于排序的算法，这可能会进入无限循环或在几次交换后停止。

### 正确的方法——费希尔-耶茨算法

打乱一组值是计算机科学中最古老的问题之一。自 1938 年以来，最流行的解决方案已经为人所知。这是费雪-耶茨洗牌。

与 Microsoft shuffle 不同，这种算法实际上是随机打乱数组，并且具有 O(n)时间复杂度，假设您有一个复杂度为 O(1)的随机数生成器。

最初的费希尔·耶茨算法，描述于 1938 年，大概是这样的:

> 1.  Write down the numbers from 1 to n.
> 2.  Choose a random number k between 1 and the remaining numbers (inclusive) that have not been crossed out.
> 3.  Starting from the low-end number, cross out the k-th number that has not been crossed out, and write it down at the end of a separate list. Repeat from step 2 until all the numbers are crossed out.
> 4.  第三步写下的数字序列现在是原始数字的随机排列。来源: [https://en。维基百科。org/wiki/Fisher % E2 % 80% 93 Yates _ shuffle # Fisher _ and _ Yates ' _ original _ method](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle#Fisher_and_Yates'_original_method)

这个算法的现代版本(来自唐纳德·克努特的《计算机编程的艺术》一书)是这样的:

```
 — To shuffle an array a of n elements (indices 0..n-1):
for i from n−1 downto 1 do
 j ← random integer such that 0 ≤ j ≤ i
 exchange a[j] and a[i] 
```

在 javascript 中，它会被实现为:

```
for(let i = array.length — 1; i > 0; i--){
  const j = Math.floor(Math.random() * array.length)
  const temp = array[i]
  array[i] = array[j]
  array[j] = temp
} 
```

### 最后的想法

当试图解决问题时，寻找屡试不爽的方法。

我们程序员站在巨人的肩膀上。在过去的 70 多年里，这些算法得到了完善和改进。您自己的实现比这更好的机会微乎其微。