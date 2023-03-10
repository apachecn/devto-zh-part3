# 利用主定理求解递归

> 原文：<https://dev.to/downey/using-the-master-theorem-to-solve-recurrences-4pdb>

*注:*这将是我第一次(希望)发表许多与佐治亚理工学院 [CS6515 研究生算法](https://www.omscs.gatech.edu/cs-8803-ga-graduate-algorithms)课程相关的帖子。我的目标有两个:

1.  用我自己的话解释这些材料有助于加强我对这门学科的理解(帮助我为考试做准备)😬).
2.  提供了这方面的另一个资源。用不同的方式解释可能会帮助未来的学生更好地理解它！

话虽如此，以下是我对如何将[主定理](https://en.wikipedia.org/wiki/Master_theorem_(analysis_of_algorithms))用于分治[递推关系](https://en.wikipedia.org/wiki/Recurrence_relation)的[渐近分析](https://en.wikipedia.org/wiki/Asymptotic_analysis)的不太形式化的解释。

这句话本身就很拗口，所以让我们把它再分解一下，并定义其中的一些术语。

*   **渐近分析-** 对函数极限的分析。为了这篇文章，我们将使用[大 O 符号](https://en.wikipedia.org/wiki/Big_O_notation)来描述我们递归的上限。
*   **分治算法-** 一类[算法](https://www.khanacademy.org/computing/computer-science/algorithms/merge-sort/a/divide-and-conquer-algorithms)，通过递归地将一个问题分解成更小的子问题并组合结果来工作。[合并排序](https://en.wikipedia.org/wiki/Merge_sort)是分治算法的一个很好的例子。
*   **递归关系-** 用自身递归表达一个序列的方程。例如，[斐波那契数列](https://www.mathsisfun.com/numbers/fibonacci-sequence.html)的递归是`F(n) = F(n-1) + F(n-2)`，合并排序的递归是`T(n) = 2T(n/2) + n`。

换句话说，如果我们有一个递归关系，比如对于合并排序这样的分治算法的`T(n) = 2T(n/2) + n`，我们可以使用主定理来计算它的大 O 复杂度！

## 掌握定理基础知识

主定理让我们解决以下形式的递归，其中 **a > 0** 和 **b > 1** :

**T(n)= aT(n/b)+*f*(n)**

让我们定义其中的一些变量，并以合并排序的递归为例: **T(n) = 2T(n/2) + n** 。

*   **n -** 问题的大小。例如，对于合并排序， **n** 将是被排序的列表的长度。
*   **a -** 每个递归步骤中子问题的数量。因此，在我们的合并排序示例中，由于我们将数组分成两半，并向下递归每一半， **a = 2** 。
*   我们减少子问题的数量。对于合并排序 **b = 2** ，因为我们将数组的一半(长度 **n** )传递给每个子问题。 **n/b** 是每个子问题的总大小。
*   ***f* (n) -** 要在 **n** 递归步骤之外做的工作。对于合并排序，这表示递归结果的合并步骤。

## 掌握定理案例

可以通过主定理解决的递归关系分为三种情况，描述了递归的大部分时间复杂度成本。这些案例是:

1.  在子问题中执行的工作(**(n/b)**部分)对总时间复杂度影响最大。
2.  在子问题中执行的工作与在划分/组合步骤中执行的工作( ***f* (n)** 部分)具有大约相同的影响级别
3.  在子问题中执行的工作比在划分/组合步骤中执行的工作对总时间复杂性的影响更小。

为了找出哪种情况适用，你需要比较子问题的大小(即 **aT(n/b)** )和外部执行的工作大小(***f*【n】**)。这留给我们以下比较:

**n <sup>日志<sub>b</sub>(a)</sup><=>*f*(n)**

因此，让我们从这些角度重新思考我们的三个案例:

1.  **n<sup>log<sub>b</sub>(a)-ε</sup>>*f*(n)**；然后**T(n)=*O*(n<sup>log<sub>b</sub>(a)</sup>)**
2.  **n<sup>log<sub>b</sub>(a)</sup>= =*f*(n)**；然后**T(n)=*O*(n<sup>log<sub>b</sub>(a)</sup>* log(n))**
3.  **n<sup>log<sub>b</sub>(a)+ε</sup><T5】f(n)**；然后**T(n)=*O*(*f*(n))**

注意:ε(ε)只是一个常数，你可以选择ε >为 0。

现在，我对上面的情况使用了比较符号，因为这使我更容易进行推理，但是更准确的是根据 ***f* (n)** 的复杂性的上限和下限来考虑这些情况。让我们重写它们:

1.  ***O*(n<sup>log<sub>b</sub>(a)-ε</sup>)=*f*(n)**；然后**T(n)=*O*(n<sup>log<sub>b</sub>(a)</sup>)**
2.  ***θ*(n<sup>log<sub>b</sub>(a)</sup>)=*f*(n)**；然后**T(n)=*O*(n<sup>log<sub>b</sub>(a)</sup>* log(n))**
3.  ***ω*(n<sup>log<sub>b</sub>(a)+ε</sup>)=*f*(n)**；然后**T(n)=*O*(*f*(n))**

大的 *O* 表示一个**紧上界**，大的*ω*(omega)表示一个**紧下界**，大的*θ*(theta)表示 f(n)复杂度上的一个**紧上下界**。

换句话说，如果 *f* (n)的成本的上界是子问题的工作成本，那么算法由在子问题上工作所花费的时间支配(**案例 1** )。

如果 *f* (n)的成本的下限是子问题的工作成本，那么算法由花费在递归子问题之外的划分/组合步骤上的时间支配(**情况 3** )。

当两个上/下限相同时，那么 *f* (n)和子问题的工作成本大约相等(**情况 2** )。

现在让我们使用合并排序递归来完成一个具体的例子。

## 主定理归并排序示例

递推关系:
**T(n)= 2T(n/2)+*O*(n)**

变量:
**a = 2**
**b = 2**
***f*(n)=*O*(n)**

对比:
**n<sup>log<sub>b</sub>(a)</sup><=>O(n)**
**n<sup>1</sup>= = O(n)**

这里我们看到 *f* (n)的代价和子问题是一样的，所以这是**案例二**:
T5】T(n)=*O*(nlogn)

## 附加资源

如果事情仍然有点模糊，或者你想看更多的例子，我发现以下资源很有帮助:

*   [维基百科关于主定理的文章](https://en.wikipedia.org/wiki/Master_theorem_(analysis_of_algorithms))
*   [掌握方法视频教程](https://www.youtube.com/watch?v=6CX7s7JnXs0)
*   [DPV 算法书](http://cseweb.ucsd.edu/~dasgupta/book/index.html) -第二章，第 2.2 节