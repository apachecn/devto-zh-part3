# 我无法停止思考这个编码问题

> 原文：<https://dev.to/protium/i-can-t-stop-thinking-in-this-coding-problem-10kj>

我在一次直播中遇到了这个问题:

将会有一场汽车比赛。汽车从 1 到 n 编号。只有当汽车的号码是他们喜欢的号码的一个因数时，司机才会得到一辆汽车。找出参与者的最大数量。

例如 n = 3，prefsNumbers = [9，1，5]，output: 2
`1 <= n <= 1000; 1 <= prefsNumbers[i]`

我有两种方法:

*   建立一个 n x m 的矩阵，为每辆车找到一个司机。每一行代表一辆汽车，如果汽车号码 *i* 是参与者 *j* 优选号码的因子，则每个单元格[i，j]为真。然后，对于每一行，查找是否至少有一个驱动程序。如果是，则参与者总数加 1。例如

```
 |9 1 5
--------- 
1 |1 1 1
2 |0 0 0
3 |1 0 0
Cars 1 and 3 have at least one possible driver. The output is 2 
```

Enter fullscreen mode Exit fullscreen mode

*   按照每个首选数字的因子的数量对这些首选数字进行排序，这样我将从质数开始，首先为那些很少有机会得到汽车的数字找到一辆汽车

两种方法都没有通过所有的测试用例。我错过了什么？

#### 已编辑:第一种方案描述有误