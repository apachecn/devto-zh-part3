# Java 日常编码问题#001

> 原文：<https://dev.to/awwsmm/java-daily-coding-problem-001-59pg>

[日常编码问题](https://dailycodingproblem.com/)是一个网站，它会每天给你的收件箱发送一个编程挑战。我想向初学者展示如何使用 Java 解决这些问题，所以这将是我的一系列解决方案中的一个。欢迎在评论中把它们挑出来！

### 问题

> 给定一个数字列表和一个数字 k，返回列表中任意两个数字的总和是否为 k。
> 
> 例如，给定[10，15，3，7]和 17 的 k，返回 true，因为 10 + 7 是 17。
> 
> 额外收获:你能一次完成吗？

### 策略

这个问题要求列表中的每一个元素都要与其他元素至少检查一次。在最坏的情况下，我们必须检查:

*   2 个元件 1 对
*   3 对 3 个元素
*   4 个元件 6 对
*   等等。

这些是[三角数字](https://en.wikipedia.org/wiki/Triangular_number)。第`N`个三角形数`T(N)`由下式给出:

```
 T(N) = N * (N + 1) / 2 
```

Enter fullscreen mode Exit fullscreen mode

所以该算法最坏情况下的时间复杂度为`N` <sup>2</sup> 。假设列表有`N`个元素...

*   应对照第二至第`N`个要素检查第一个要素
*   应对照第三至第`N`个要素检查第二个要素
*   等等。

一旦找到加起来等于`k`的一对，我们就可以退出。我们不需要找到*所有的*对，我们只需要找到加到`k`的第一个*对。问题陈述了这一对不应该被返回，只是`true`或`false`。*

### 代码

我要做的第一件事是构建一个双`for`循环，其中外部循环遍历列表中的所有元素(除了最后一个)，内部循环遍历外部循环中当前元素之后的所有元素*。假设给定的列表叫做`given`* 

```
int[] given = new int[]{ ... };
int k = ...;

int len = given.length;

for (int outer = 0; outer < (len - 1); ++outer) {
  for (int inner = outer + 1; inner < len; ++inner) {

    // ... logic here

  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们就不会对两个元素进行两次比较。

剩下唯一要做的就是检查索引`outer`和`inner`处的元素是否加到了`k`中。如果他们这样做了，我们就完成了——我们可以返回`true`。否则，我们检查下一对。如果我们遍历了所有对，但没有找到任何和为`k`的对，我们返回`false` :

```
int[] given = new int[]{ ... };

int len = given.length;

public boolean codingProblem001 (int[] array, int k) {

  for (int outer = 0; outer < (len - 1); ++outer) {
    for (int inner = outer + 1; inner < len; ++inner) {

      if (given[outer] + given[inner] == k)
        return true;

    }
  }

  return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们检查一下这个是否有效:

```
$ jshell
|  Welcome to JShell -- Version 9.0.4
|  For an introduction type: /help intro

jshell> /open DCP001.java

jshell> int[] given = new int[]{ 10, 15, 3, 7 }
given ==> int[4] { 10, 15, 3, 7 }

jshell> int k = 17
k ==> 17

jshell> DCP001.codingProblem001(given, k)
$4 ==> true

jshell> DCP001.codingProblem001(given, k+1)
$5 ==> true

jshell> DCP001.codingProblem001(given, k+2)
$6 ==> false 
```

Enter fullscreen mode Exit fullscreen mode

结果`$4`是`true`，因为 10 和 7 加起来是`k`。`$5`也是`true`，因为 15 加 3 等于`k+1`。但是`$6`是`false`，因为`given`没有两个元素加到`k+2`上。

* * *

我的日常编码问题解决方案的所有代码都可以在[github.com/awwsmm/daily](https://github.com/awwsmm/daily)获得。

建议？请在评论中告诉我。