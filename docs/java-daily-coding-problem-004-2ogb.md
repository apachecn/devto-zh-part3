# Java 日常编码问题#004

> 原文：<https://dev.to/awwsmm/java-daily-coding-problem-004-2ogb>

[日常编码问题](https://dailycodingproblem.com/)是一个网站，它会每天给你的收件箱发送一个编程挑战。我想向初学者展示如何使用 Java 解决这些问题，所以这将是我的一系列解决方案中的一个。欢迎在评论中把它们挑出来！

### 问题

> 给定一个整数数组，求线性时间和常数空间中第一个缺失的正整数。换句话说，找到数组中不存在的最小正整数。该数组也可以包含重复项和负数。
> 
> 比如输入`[3, 4, -1, 1]`要给`2`。输入`[1, 2, 0]`应该给`3`。
> 
> 您可以就地修改输入数组。

### 策略

*剧透！*不要看下面，除非你想看我的解决方案！

* * *

我对这个问题的解释是，我们在寻找最小的整数值，大于 0，它不出现在数组中。因此，如果一个数组包含 1、2 和 3，但不包含 4，代码应该返回`4`。重复和负数可以忽略。应该返回的最大值是数组的长度加 1，如第二个示例所示，因此解将总是在`1`和`N+1`之间，包括这两个值，其中`N`是给定数组的长度。

我首先想到的是创建一个长度为`N`的新数组，用`false`填充它，如果给定数组中存在索引，则将这些`false`翻转为`true`。但是这个解决方案不是一个固定间距的解决方案，因为它需要一个长度为`N`的数组。(尽管它在时间上是线性的，因为我们只需要遍历原始数组一次。)我们能做什么来代替呢？

示例数组没有排序，所以我们不能假设它们是通用的。在研究一个恒定空间的线性时间排序算法时，我发现[这就是答案](https://stackoverflow.com/a/3979434/2925434)，它规定了符合这些要求的以下算法:

1.  从第一个数组元素开始。
2.  如果它的数组索引与其值匹配，则转到下一个。
3.  如果不是，就用数组索引处与其值相对应的值替换它。
4.  重复步骤 3，直到不再需要交换。
5.  如果不在数组末尾，则转到下一个数组元素，否则转到步骤 7
6.  转到步骤 2。
7.  完成的

注意，这对于我们的目的来说是有问题的——我们的数组可以包含重复项和负数。假设索引`0`处的整数是`7`，索引`7`处的整数也是`7`——我们将陷入无限循环！所以这个方案也出来了。

这是一个棘手的问题！

提示说我们可以在适当的位置修改输入数组，对我来说，这似乎是我们保持这个算法恒定空间复杂度的唯一方法。(除了制作一个`true` / `false`数组，如上所述，长度等于`Integer.MAX_VALUE`。)所以我觉得我们需要做一些和上面类似，但又不完全一样的事情。

经过进一步的研究，我找到了这个问题的解决方案，也就是简单地忽略负值。我想知道这是否可行？所以(稍微清晰的)算法应该是:

1.  移动到数组的下一个元素(如果这是第一次遇到此步骤，则从数组的开头开始):
2.  如果这是数组的最后一个元素，请转到步骤 5。
3.  如果这个元素的数组索引与它的值匹配，或者如果它的值为零或负值，则返回到步骤 1。否则，继续步骤 4。
4.  将该元素的值与该元素的值所表示的索引中包含的值交换，并返回到步骤 3。
5.  再次从头开始遍历数组，并将数组索引(从 1 开始)与其包含的值进行比较。如果索引和值不匹配，则该索引是不包含在数组中的最小正整数值。

对于提示中给出的示例数组，此过程类似于(所有从 1 开始的索引):

```
[ 3,  4, -1,  1] -- original array
[-1,  4,  3,  1] -- after swapping 1st and 3rd elements
[-1,  1,  3,  4] -- after swapping 2nd and 4th elements
[ 1, -1,  3,  4] -- after swapping 2nd and 1st elements 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以第二次遍历数组，找到其值与其索引不匹配的第一个元素。走数组两次需要`N` + `N`时间，或者`2N`(又名。线性)时间。因为我们在适当的位置修改数组，所以这也是一个恒定空间的解决方案。让我们考虑另一个例子:

```
[ -1, -1,  8,  1,  2,  2] 
```

Enter fullscreen mode Exit fullscreen mode

该算法应该为该数组返回`3`。但是当我们到达第三个元素(`8`)时，我们遇到了一个问题:数组的元素少于 8 个。我们需要在算法的步骤`3`中添加另一个条件:

> 如果这个元素的数组索引与它的值匹配，或者如果它的值是零、负或大于数组的长度，则返回到步骤 1。否则，继续步骤 4。

浏览第二个例子:

```
[ -1, -1,  8,  1,  2,  2] -- original array
[  1, -1,  8, -1,  2,  2] -- after swapping 4th and 1st elements
[  1,  2,  8, -1, -1,  2] -- after swapping 5th and 2nd elements
[  1,  2,  8, -1, -1,  2] -- ... 
```

Enter fullscreen mode Exit fullscreen mode

这里我们遇到了另一个问题——重复。如果我们继续尝试交换上面例子中的第六和第二个元素，我们将会有一个无限循环。所以我们应该检查“目标”和“源”的值是否相同，如果相同，就移动到数组的下一个元素:

> 将该元素的值与该元素的值所表示的索引中包含的值进行交换，除非这两个值相同，在这种情况下，请返回到步骤 1。

所以最终的算法看起来像:

1.  移动到数组的下一个元素(如果这是第一次遇到此步骤，则从数组的开头开始):
2.  如果这是数组的最后一个元素，请转到步骤 5。
3.  如果这个元素的数组索引与它的值匹配，或者如果它的值是零、负或大于数组的长度，则返回到步骤 1。否则，继续步骤 4。
4.  将该元素的值与该元素的值所表示的索引中包含的值进行交换，除非这两个值相同，在这种情况下，请返回到步骤 1。
5.  再次从头开始遍历数组，并将数组索引(从 1 开始)与其包含的值进行比较。如果索引和值不匹配，则该索引是不包含在数组中的最小正整数值。

...我觉得这个应该可以。我们试着编码一下吧！

### 代码

所以我做的第一件事是建立一个 shell 类。我们需要一个方法来找到第一个丢失的整数，可能还需要一个`main`来运行提示中的两个例子:

```
public class DCP004 {

  public static void main (String[] args) {
    findSmallestMissing(new int[]{  3,  4, -1,  1 });
    findSmallestMissing(new int[]{  1,  2,  0 });
  }

  public static int findSmallestMissing (int[] array) {
    // to be implemented
    return -1;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，在`findSmallestMissing()`中，我们将想要实现我们上面概述的算法。首先，让我们建立一个循环来遍历数组:

```
 public static int findSmallestMissing (int[] array) {

    // if array is null or empty, 1 is the smallest missing number
    if (array == null || array.length < 1) return 1;

    // get the length of the array
    int len = array.length;

    // assume 1 is smallest missing number until proven otherwise
    int smallestMissing = 1;

    // loop over input array (steps 1 and 5)
    for (int ii = 0; ii < len; ++ii) {
      // implement steps 2-4 in here
    }

    // to be implemented
    return -1;
  } 
```

Enter fullscreen mode Exit fullscreen mode

该算法的大部分工作都发生在这个循环中:

```
 // loop over input array (steps 1 and 5)
    for (int ii = 0; ii < len; ++ii) {

      // step 3 (make sure to use 1-based indexing)
      if (array[ii] == (ii+1) || array[ii] < 1 || array[ii] > len)
        continue;

      // step 4
      while (array[ii] != ii) {

        // index of the element to swap with
        int swap = array[ii]-1;

        // value of the element to swap with
        int temp = array[swap];

        // swap the values
        array[swap] = array[ii];
        array[ii] = temp;

        // if the new value is < 1 or > len, move to next one
        if (temp < 1 || temp > len) break;
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经适当地修改了`array`，我们需要做的最后一件事就是遍历它并找到第一个值不匹配其(base-1)索引的元素:

```
 // loop over modified array
    for (int ii = 0; ii < len; ++ii) {
      if (array[ii] != ii+1) return ii+1;
    }

    return len+1; 
```

Enter fullscreen mode Exit fullscreen mode

我已经在我的 markdown 编辑器中编写了这段代码，但没有对它进行测试，所以让我们把它们粘贴在一起，看看它是否能工作！

### 调试

```
jshell> /open DCP004.java

jshell> DCP004.main(new String[]{})

jshell> DCP004.findSmallestMissing(new int[]{1, 2, 0})
$3 ==> 3

jshell> DCP004.findSmallestMissing(new int[]{3, 4, -1, 1})
$4 ==> 1 
```

Enter fullscreen mode Exit fullscreen mode

...好了，我们有一些小问题。首先，在`main`中，不打印任何内容。让我们在`main` :
中的那些函数调用周围添加`System.out.println`语句

```
 public static void main (String[] args) {
    System.out.println(findSmallestMissing(new int[]{  3,  4, -1,  1 }));
    System.out.println(findSmallestMissing(new int[]{  1,  2,  0 }));
  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，看起来第一个数组返回了正确的结果(`3`)，但是第二个数组有问题。它应该返回`2`的时候却返回了`1`。这仅仅是一个偶然的错误吗？让我们试试另一个数组:

```
jshell> DCP004.findSmallestMissing(new int[]{2, 4, -1, 1})
$7 ==> 
```

Enter fullscreen mode Exit fullscreen mode

...这没有返回值，因为我必须杀死它——我认为这是一个无限循环！我至少发现了一个问题。下面一行:

```
 while (array[ii] != ii) { 
```

Enter fullscreen mode Exit fullscreen mode

...应该改为

```
 while (array[ii] != (ii+1)) { 
```

Enter fullscreen mode Exit fullscreen mode

这似乎已经解决了问题！

```
jshell> DCP004.findSmallestMissing(new int[]{2, 4, -1, 1})
$9 ==> 3

jshell> DCP004.findSmallestMissing(new int[]{3, 4, -1, 1})
$10 ==> 2 
```

Enter fullscreen mode Exit fullscreen mode

不过，还有一个问题。这个数组也给出了一个无限循环:

```
jshell> DCP004.findSmallestMissing(new int[]{1, 1, 2, 2, 3, 5})
$11 ==> 
```

Enter fullscreen mode Exit fullscreen mode

...这次我们错过了什么？为了调试，让我们在`while`循环中每次交换之前打印出数组:

```
 // step 4
      while (array[ii] != (ii+1)) {

        // DEBUG: print array
        System.out.println(Arrays.toString(array));

        // index of the element to swap with
        int swap = array[ii]-1; 
```

Enter fullscreen mode Exit fullscreen mode

最后一个例子的输出类似于:

```
[1, 1, 2, 2, 3, 5]
[1, 1, 2, 2, 3, 5]
[1, 1, 2, 2, 3, 5]
[1, 1, 2, 2, 3, 5]
[1, 1, 2, 2, 3, 5]
[1, 1, 2, 2, 3, 5]
... 
```

Enter fullscreen mode Exit fullscreen mode

看起来什么都没有被交换过！(或者，开头的两个`1`一遍又一遍的调换。)看起来，当两个交换的值相同时，我们忘记了断开`while`。让我们添加一个陷阱:

```
 // if the new value is < 1 or > len, move to next one
        if (temp < 1 || temp > len) break;

        // if new value equals old value, move to next one
        if (temp == array[ii]) break; 
```

Enter fullscreen mode Exit fullscreen mode

...这解决了问题吗？

```
jshell> DCP004.findSmallestMissing(new int[]{1, 1, 2, 2, 3, 5})
[1, 1, 2, 2, 3, 5]
[1, 1, 2, 2, 3, 5]
[1, 2, 1, 2, 3, 5]
[1, 2, 1, 2, 3, 5]
[1, 2, 3, 2, 1, 5]
$15 ==> 4

jshell> DCP004.findSmallestMissing(new int[]{-1, -1, 8, 1, 2, 4})
[-1, -1, 8, 1, 2, 4]
[1, -1, 8, -1, 2, 4]
[1, 2, 8, -1, -1, 4]
$16 ==> 3 
```

Enter fullscreen mode Exit fullscreen mode

...看起来是这样！

### 讨论

我们走吧！在恒定时间和线性空间中的一种解决方案，它还在每次交换之前打印修改后的数组以用于诊断目的。它在示例数组和我扔给它的几个附加数组上运行良好。

这个问题需要一点思考来坚持约束，但是很有趣！有人能找到破坏我代码的数组吗？

* * *

我的日常编码问题解决方案的所有代码都可以在[github.com/awwsmm/daily](https://github.com/awwsmm/daily)获得。

建议？请在评论中告诉我。