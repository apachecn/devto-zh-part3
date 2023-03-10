# 解释“损坏的句子”动态规划问题

> 原文：<https://dev.to/downey/explaining-the-corrupted-sentence-dynamic-programming-problem-1hl5>

*封面照片由[罗曼·维涅斯](https://unsplash.com/@rvignes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com)T5 拍摄*

这是我们 [CS6515 班](https://gt-algorithms.com/)未评分的练习题。有[现有的解决方案](http://www.cs.rpi.edu/~goldsd/docs/fall2013-csci2300/sample-final-exam-solutions.pdf)，但我发现它们有点不直观，所以这是我尝试用不同的方式解释这个问题。

## 问题总结

给你一个字符串 *s* 包含 *n* 字符*s*1... *n* 。字符串已损坏，所有标点都已被删除。例如，字符串“你是一个大胆的。”会显示为“youareaboldone”。我们还给出了一个字典方法， *dict(w)* ，它能够确定 *s* 的子串 *w* 是否是有效单词。换句话说， *dict("bold")* 将返回 *true* ，而 *dict("aboldo")* 将返回 *false* 。您的任务是完成以下工作:

1.  给出一个动态规划算法，可以判断一个字符串 *s* 是否包含一句有效词。这个算法最多需要 *O(n <sup>2</sup> )* 时间。
2.  如果字符串有效，输出单词序列。

参考教科书的第 178 页，获得完整的、形式化的问题描述。

## 解

我将着手解决这个问题，类似于我的[背包问题解释](https://dev.to/downey/solving-the-knapsack-problem-with-dynamic-programming-4hce)，并做以下事情:

1.  陈述子问题
2.  定义重复
3.  描述记忆表结构
4.  快速编写一些伪代码
5.  对算法进行一些快速的大 O 分析

### 子问题

动态编程就是识别更小的子问题，解决它们，并使用它们来构建最终的解决方案。对于这个问题，我们可以从长度为 *i* 的**的 *s* 的更小前缀**开始构建，其中**0≤*I*≤*n*T11】。**

让我们把我们的子问题定义为:

**V( *i* ) =是否(*真* / *假*)字符串 *s* 只包含字符串*s<sub>1</sub>T12】中存在于字典中的有效单词... *s <sub>i</sub>****

### 重复出现

长度为 0 的子字符串(空字符串)被认为是有效的单词，所以我们的基本情况是从句子有效开始。

*   **基本情况:** **V(0) = true**

因为我们需要检查 ***s <sub>1</sub>* 中子串的状态... *s <sub>i</sub>*** 在我们的字典中我们将需要引入另一个变量 *j* 使得**0≤*I*≤*I***。我们的循环将如下所示:

*   **递归:****V(*I*)=*any*(for(*j*in*1*...i ) { *dict(s[j...i])* 和 V( *j* - 1) } )**

这是什么意思？基本上，对于从 1 到 T2 的所有字符，我们将使用 T4 j T5 循环遍历所有可能的子串。如果从 *j* 到 *i* 的 *s* 的子串是字典**和**中的有效单词，我们通过 V( *j* - 1)检查前一个子串的结尾来记录前一个单词是有效的。

### 记忆化表格结构

假设我们的子问题定义只接受一个参数， *i* ，我们应该能够得到一个长度为 1 维的记忆表来包含长度为 0 到 *n* 的字符串的部分值。

### 伪代码实现

我决定用 Python 而不是伪代码来实现这个。您应该能够在 Repl 上运行下面的代码。它使用他们的 Python 3 解释器。

#### 第 1 部分——句子有效吗？

```
# Base case t[0] = True

# Recurrence for i in range(1, n):
  t[i] = False
  for j in range(1, i+1):
    # i and j correspond to our memoization array t
    # which contains an extra element to represent the
    # empty substring. We must adjust them to work with
    # the s character array.
    si = i - 1
    sj = j - 1

    substr = "".join(s[sj:si+1])

    if d[substr] and t[j-1]:
      t[i] = True
      next_word_idx[j-1] = i

print("The string s contains a valid sentence:")
print(t[n-1]) 
```

Enter fullscreen mode Exit fullscreen mode

#### 第二部分-输出单词

```
# Extract the words back out prev = 0
words = []
for i in next_word_idx:
  if i != 0:
    word = s[prev:i]
    words.append("".join(word))
    prev = i

print("")
print("The words in the sentence are:")
print(words) 
```

Enter fullscreen mode Exit fullscreen mode

### 可运行代码

您可以在下面的 Python repl 中看到运行中的代码:

[https://repl.it/@tcdowney/dpv-6-4?lite=true](https://repl.it/@tcdowney/dpv-6-4?lite=true)

### 大求分析

正如您在上面的 Python 伪代码中看到的，大部分工作发生在第 1 部分。这里我们有两个嵌套循环，外层是从 1 到 *n* 的 *i* ，内层是从 1 到 *i* 的 *j* 。由于 *i* 最多为 *n* ，所以这部分最终为 *O(n <sup>2</sup> )* 。在第 2 部分中，当我们将单词提取出来时，这是一个从 0 到 *n* 的简单循环，最终得到 *O(n)* 。第 1 部分占优，**整体复杂度为 *O(n <sup>2</sup> )*** 。