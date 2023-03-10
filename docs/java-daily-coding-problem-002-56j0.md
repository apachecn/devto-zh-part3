# Java 日常编码问题#002

> 原文：<https://dev.to/awwsmm/java-daily-coding-problem-002-56j0>

[日常编码问题](https://dailycodingproblem.com/)是一个网站，它会每天给你的收件箱发送一个编程挑战。我想向初学者展示如何使用 Java 解决这些问题，所以这将是我的一系列解决方案中的一个。欢迎在评论中把它们挑出来！

### 问题

> 给定一个整数数组，返回一个新数组，使得新数组中索引`i`处的每个元素都是原始数组中除了`i`处的数字之外的所有数字的乘积。
> 
> 例如，如果我们的输入是`[1, 2, 3, 4, 5]`，那么期望的输出将是`[120, 60, 40, 30, 24]`。如果我们的输入是`[3, 2, 1]`，那么预期的输出将是`[2, 3, 6]`。
> 
> 追问:不能用除法怎么办？

### 策略

*剧透！*不要看下面，除非你想看我的解决方案！

* * *

在最坏的情况下，返回的长度为`N`的数组的每个元素都将是`N-1`其他数的乘积，所以这将是一个`O(N` <sup>2</sup> `)`解。

相反，如果我们首先将`given`数组的所有元素相乘(`O(N)`，然后，对于返回数组的每个元素，将乘积除以`given`数组中该索引处的元素，我们将得到一个`O(N) + O(N) = O(N)`解。

### 代码

最直接的方法是首先将所有数组元素相乘，然后除以索引`i`处的元素，得到返回数组
的索引`i`处的元素

```
 public static int[] codingProblem002 (int[] given) {

    int product = 1;

    for (int element : given) 
      product *= element;

    final int len = given.length;
    int[] retval = new int[len];

    for (int i = 0; i < len; ++i)
      retval[i] = product / given[i];

    return retval;
  } 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案需要`N`次乘法，`N`次除法，以及长度为`N`的原始数组的至少三次遍历。当我们计算`product` :
时，我们可以稍微重新安排解决方案并初始化返回数组`given`

```
 public static int[] codingProblem002 (int[] given) {

    int product = 1;
    final int len = given.length;
    int[] retval = new int[len];

    for (int element : given) {
      product *= element;
      retval[i] = element;
    }

    for (int i = 0; i < len; ++i) 
      retval[i] = product / retval[i];

    return retval;
  } 
```

Enter fullscreen mode Exit fullscreen mode

但是这仍然需要在第一个`for`循环中遍历`given`数组，以及`revtal`数组，然后在第二个`for`循环中再次遍历`retval`数组。我发现自己现在非常需要 Java 中的指针。

* * *

为了不使用除法解决这个问题，我们可以迭代`given`数组`N`次，将返回数组中的每个元素(除了第`i`个)乘以`given`数组的第`i`个元素:

```
 public static int[] codingProblem002 (int[] given) {

    final int len = given.length;
    int[] retval = new int[len];

    // initialise all elements of `retval` to 1
    Arrays.fill(retval, 1);

    for (int i = 0; i < len; ++i) {
      for (int j = 0; j < len; ++j) {
        if (j == i) continue;
        retval[j] *= given[i];
      }
    }

    return retval;
  } 
```

Enter fullscreen mode Exit fullscreen mode

这是一个`O(N` <sup>2</sup> `)`的解决方案。

我对我的解决方案不是很满意，如果有人能展示一种更有效的方法来完成这个编码挑战，我会很高兴的！

* * *

我的日常编码问题解决方案的所有代码都可以在[github.com/awwsmm/daily](https://github.com/awwsmm/daily)获得。

建议？请在评论中告诉我。