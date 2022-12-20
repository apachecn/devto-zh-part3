# 算法问题解决策略

> 原文：<https://dev.to/moresaltmorelemon/algorithm-problem-solving-strategies-21cp>

不幸的是，算法难题是在申请过程中淘汰候选人的常见方式。然而，当你不为找工作感到压力时，它们实际上很有趣，就像程序员的填字游戏。

解决这些问题会带来独特的挑战，这是你在开发另一个 Crud 应用程序时不会遇到的，并且会让你接触到一些你可能还不熟悉的概念。练习算法挑战将提高你更广泛的问题解决能力，以及巩固一个更普遍有用的问题解决过程。

就像其他类型的谜题一样，有一些策略可以让你提前找到问题的立足点，并把它分解成更小、更容易理解的部分。对于一个实际的拼图，你可以将这些碎片分类成具有相似颜色或特征的连贯的组，寻找明显的匹配，然后向外扩展。使用扫雷，你可能会从随机点击开始，然后在暴露的边缘周围移动，标出明显的地雷并暴露明显的空白区域，只有当你用尽所有可能性时才再次随机点击。

虽然有接近类似算法的策略，但我建议你从一个更广泛的问题解决策略开始，把它作为一个普遍的习惯，而不仅仅是当你把 LeetCode 当作面试准备时。

# 要有战略眼光，先想清楚

与其一头扎进去，不如分阶段解决问题:

思考:

1.  分析问题
2.  重述问题
3.  写出输入和输出的例子
4.  把问题分解成各个组成部分
5.  用伪代码概述解决方案
6.  用伪代码遍历您的示例数据

执行

1.  编码吧
2.  对照您的示例测试您的解决方案
3.  重构

(如果您熟悉这种方法，请跳到算法模式)

## 分析问题

当你第一次看到这个问题时，你可能会有一瞬间的洞察力。这通常是你的头脑与一些先前的经历相联系。坚持你的洞察力！然而，你仍然应该花一些时间来看问题，特别是你的洞察力与实际问题不同的地方。

任何一个写得好的谜题都有需要用一两句话来回答问题的成分。然而，仅仅因为你阅读了问题并不意味着你理解了问题。如果你不理解这个问题，你将会漫无目的地磕磕绊绊，或者解决你认为的问题。

寻找决定挑战形式的关键词。

识别输入。
确定期望的输出。
识别关键关键词和短语。

例如， [LeetCode #26](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

> 给定一个[排序的][数组的] nums，[删除重复的][就地]，这样每个元素只出现一次[返回新的长度]。
> 
> 不要为另一个数组分配额外的空间，*你必须通过用[O(1)额外的内存]就地修改输入数组***来做到这一点。**

 **输入:

*   一个数组。所以我们知道我们可能会做一些迭代。
*   一组数字。这更多的是隐含的，而不是明确声明的，而且这并不重要，因为我们可以使用相同的条件集。

返回:

*   变更数组的长度
*   副作用:修改后的数组

关键单词和短语:

*   已排序:重复的元素将彼此相邻
*   去除...复制
*   就地:数组本身必须被破坏性地修改。这个约束决定了我们可以使用什么样的数组方法。
*   O(1)额外内存:我们受限于 O(1)空间复杂度，允许我们定义变量，但不能创建数组的副本。

## 重述问题

现在，用我们自己的话，重新表述它，让它对我们自己有意义。如果你在面试的情况下，你可以向面试官复述一遍，这样既能让你自己记住，又能确保你正确地听到了并理解了他们。

重申:

> 给定一个按引用传递的数字排序数组，通过移除重复项就地破坏性地修改原始数组，以便每个值只出现一次。返回修改后数组的长度。

## 写出示例输入和预期输出

我们所做的只是将输入映射到输出。挑战在于找出如何从 A 到达 B，然而首先我们需要确定 A 和 B *是什么*。即使给了你测试用例，也要自己写。看着某样东西远不如自己动手去理解它。

这也是一个很好的时间去探索你对问题的理解，并寻找可能会绊倒一个天真的解决方案的怪癖。这包括边缘情况，如空输入、充满相同值副本的数组、大规模数据集等。我们不需要担心问题约束之外的任何事情

写出至少 3 个例子:

```
[] -> [], return 0
[1] -> [1], return 1
[1, 1, 2, 3, 4, 4, 4, 5] -> [1, 2, 3, 4, 5], return 5
[1, 1, 1, 1, 1] -> [1], return 1 
```

给定输入，您有足够的信息来映射到结果吗？如果你没有，后退一步，继续检查问题，然后再继续。如果你正在面试，请随时要求澄清。

寻找一个简单和一致的过程，你可以应用它，而不考虑达到结果的价值。如果你以一系列复杂的步骤和异常结束，你可能走得太远，错过了一个简单的解决方案。

## 把问题分解成小部分

从最简单的例子开始，简单地将问题归结为一个[基本难题](https://fs.blog/2012/04/feynman-technique/)，并在此基础上进行构建。在本例中，这是一个由三个元素组成的数组，其中两个元素是重复的，例如`[1, 1, 2]`。把问题简化成这样一个小案例会让它变得更容易接近，并明确你需要采取的第一步。你的任务是开发一个程序来解决这个简单的问题*和*适用于问题集中的所有其他情况。

所以我们知道我们需要做几件事:

*   遍历数组
*   跟踪我们在阵列中的位置
*   检查相邻值是否相等
*   在第一次出现后破坏性地移除任何重复值
*   获取最终的数组长度并返回它

这是一个相对简单的示例问题，然而有一个*陷阱*潜伏在其中:当你在数组中迭代时，许多迭代方法不能很好地从数组*中移除元素，因为索引值改变了。您可能会跳过一个副本，因为指针在它上面递增。*

这个问题表明我们想要使用一种方法，这种方法给予我们对迭代的显式控制。

在一个更复杂的问题中，我们可能会将这些组件中的一些或全部考虑到帮助函数中，从而允许我们编写一个清晰简洁的解决方案，并分别测试每个子部分的有效性。

## 伪代码解决方案

如果我们已经清楚地抓住了问题，确定了核心任务，并且很有希望地发现了我们自己的假设中的缺陷和任何问题，我们就写出了我们的方法将会是什么的一个人类易读的描述。希望一旦我们做到了这一点，我们可以干净地将其转换成工作代码。

伪代码怎么写由你自己决定。你的符号不需要拼写和语法完全正确。它可以是代码和单词的手势组合，意在传达意思。您的伪代码将提供一个有意义的路线图，如果您发现自己迷失在实现的细节中，您可以回头参考，所以请确保您记录了足够多的内容以供以后使用。

如果你正在面试，这是一个让面试官了解你的意图的好机会。如果你快没时间了，你至少可以在黑板上展示你解决问题的方法。

建议:

*   以函数签名开始:`removeDuplicates :: (Array) -> number`
*   如果是白板，留下足够的空间来写你的实际代码
*   如果使用 IDE，请编写注释，并将它们与代码分开，以便以后引用。
*   把它写成一系列的步骤，并使用要点

因为我们在寻找重复的，这意味着我们需要进行比较。我们可以向前看我们在数组中的当前位置，或者向后看。

```
// removeDuplicates :: (Array) -> number
// if array is empty or has 1 element, return array length and exit
// iterate through array
//     compare each element to the next element
//
//     repeat until false:
//        if the next element is the same as the current element
//        remove the next element
//
//     move on to the next element in the array
//     stop once the second to last element has been reached
// return array length 
```

如果数组的大小只有 0 或 1 个元素，我们首先退出，部分原因是这些情况满足问题的条件:不可能有重复，部分原因是如果我们尝试将第一个值与不存在的第二个值进行比较，它们会破坏我们的代码。

我们还建立了迭代退出条件，因为我们将使用前瞻，所以我们确保在到达最后一个元素之前停止*。*

因为我们在处理完任何重复项后的*才移动指针位置，所以我们应该清楚移动索引的问题。*

## 遍历样本数据

花一点时间，在脑海中通过我们的伪代码运行一些样本数据:

```
[] -> [], return 0
[1] -> [1], return 1
[1, 1, 2, 3, 4, 4, 4, 5] -> [1, 2, 3, 4, 5], return 5
[1, 1, 1, 1, 1] -> [1], return 1 
```

我们错过了什么吗？

最后一个例子可能有问题:`[1, 1, 1, 1, 1]`。如果我们删除所有重复的元素，然后尝试移动到数组中的下一个元素，而不检查是否有重复的元素，会发生什么？

我们要确保我们的结束条件捕捉到数组长度的任何变化。

## 码吧

橡胶上路的时间到了。这是你所有的假设，你甚至不知道你做了回来困扰你。你计划得越好，问题就越少。

```
function removeDuplicates(arr) {
    if (arr.length < 2) return arr.length

    return arr.length
} 
```

就我个人而言，我喜欢先输入我的返回值。这样我就清楚了我的目标是什么，并且我也捕捉到了空数组或单元素数组的第一种情况。

```
function removeDuplicates(arr) {
    if (arr.length < 2) return arr.length

    for(let i = 0; i < arr.length; arr++) {}

    return arr.length
} 
```

是的，我们要用一个标准的 for 循环。如果有更合适或更清晰的语法，我宁愿不使用它们，但是对于这个特殊的问题，我们需要控制迭代的能力。

```
function removeDuplicates(arr) {
    if (arr.length < 2) return arr.length

    for(let i = 0; i < arr.length; i++) {
        while (arr[i + 1] && arr[i] === arr[i + 1]) arr.splice(i + 1, 1)
    }

    return arr.length
} 
```

这是现成的，除了:

```
removeDuplicates([0,0,1,1,1,2,2,3,3,4])    //> 6, should be 5 
```

结果是，如果数组值为`0`，我在 while 循环中偷偷进行的存在检查将解析为 falsy。感谢 JavaScript！因此，让我们快速地重新工作，向后看而不是向前看，这样实际上也能稍微清理一下代码:

```
function removeDuplicates(arr) {
    if (arr.length < 2) return arr.length

    for(let i = 0; i < arr.length; i++) {
        while (arr[i] === arr[i - 1]) arr.splice(i, 1)
    }

    return arr.length
} 
```

一切都过去了。这是一个内存高效的解决方案，除了数组引用，我们只定义了 1 个变量。它的速度一般，我们可以改进。

但主要是一个简单的过程:

1.  分析
2.  重申
3.  写例子
4.  分成小问题
5.  伪代码中的大纲
6.  通过示例逐步执行伪代码
7.  密码
8.  试验
9.  重构

# 算法模式

除了具有已知和相当标准化的方法的特定数据结构和算法之外，算法挑战倾向于落入建议类似解决方法的类别中。学习这些方法会给你一个解决问题的立足点。

## 多个指针

当我们第一次学习遍历一个集合(通常是一个数组)时，我们使用一个指针，这个指针的索引从最低值到最高值。这适用于某些操作，并且易于考虑和编码。然而，对于涉及比较多个元素的问题，特别是那些它们在集合中的位置很重要的问题，用单个指针查找对应的值需要对每个值至少遍历一次数组，这是一个 O(n <sup>2)</sup> 操作。

相反，如果我们使用多个点，我们可以潜在地将计算减少到 O(n)运算。

有两种常见的策略:双指针和滑动窗口

### 两个指针

为什么不从两端开始，然后一点一点地进入呢？或者从一个值或一对值开始向外扩展。这是寻找集合中最大序列的一个很好的方法。

因为您正在处理两个点，所以您需要定义一个规则来确保它们不会相互交叉。

```
// Time complexity O(n)
// Space complexity O(1)
function sumZero(arr) {
  let left = 0;
  let right = array.length - 1;
  while (left < right) {
    let sum = arr[left] + arr[right];
    if (sum === 0) return [arr[left], arr[right]];
    else if (sum > 0) right--;
    else left++;
  }
} 
```

### 滑动窗口

我们可以在数组中依次并行移动两个指针，而不是在外部边界放置两个点。我们的窗口宽度可能会根据问题集而增长或收缩，但它会继续在整个集合中前进，捕捉最符合预期结果的序列的快照。

```
function maxSubarraySum(array, n) {
  if (array.length < n) n = array.length;
  let sum = 0;

  for (let i = 0; i < n; i++) {
    sum = sum + array[i];
  }

  let maxSum = sum;

  // shift window across array
  for (let i = n; i < array.length; i++) {
    sum = sum + array[i] - array[i - n];
    if (sum > maxSum) maxSum = sum;
  }
  return maxSum;
} 
```

## 各个击破

分而治之通常涉及递归方法:应用相同的规则来划分集合，直到您将它分解成最小的组件并找出答案。

二分搜索法和合并排序都是递归细分导致解决方案的优秀例子。

## O(1)查找:对象/字典/哈希

散列键:值存储，根据你的编码语言称为对象、字典或散列，在计算频率、检查重复或补充答案时，是非常有用的信息存储工具。就在那里

您可以存储该值，也可以存储您正在寻找的值。例如，当在数组中寻找零和对时，我们可以保存补码而不是值本身。

## 资源:

[算法解题基础(视频)](https://www.youtube.com/watch?v=PAWyuludGU0)
[面向程序员的算法解题](https://www.thecodingdelight.com/algorithmic-problem-solving-programmers/)
[面试问题中的 10 大算法](https://www.geeksforgeeks.org/top-10-algorithms-in-interview-questions/)
[提高你的算法和数据结构技能](https://medium.com/coderbyte/how-to-get-good-at-algorithms-data-structures-d33d5163353f)**