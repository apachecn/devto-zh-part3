# 无监督学习:随机化优化

> 原文：<https://dev.to/swyx/unsupervised-learning-randomized-optimization-d2j>

*这是我参加[佐治亚理工学院/优达城机器学习课程](https://www.udacity.com/course/machine-learning--ud262)的一系列课堂笔记中的第 12 篇。课堂教材是汤姆·米切尔的[机器学习。](https://www.cs.ubbcluj.ro/~gabis/ml/ml-books/McGrawHill%20-%20Machine%20Learning%20-Tom%20Mitchell.pdf)*

> 这标志着一个关于无监督学习的新迷你系列的开始，[机器学习](https://dev.to/swyx/machine-learning-an-overview-216n)中 3 个子学科的第二个。

## 我们的目标:优化

给定一个输入空间`X`，和一个目标(也称为适应度)函数`f(x)`，从`X`中找出`x`，使得`f(x)`是最大可能值。

这类似于我们在之前的公式中使用的 [argmax](https://en.wikipedia.org/wiki/Arg_max) 函数，但现在我们实际上正在探索实现它的方法。

## 当传统方法失效时

给定一个小的`X`，我们可以简单地运行一个`for`循环[，就像这个](https://repl.it/@swyx/ML-optimization-practice)一样，并跟踪最大值。

给定一个无限的`X`但是有一个可解的`df(x)`，我们可以使用微积分来帮助我们找到最优解。

如果`df(x)`无法求解，我们可以用[牛顿拉夫森法](https://en.wikipedia.org/wiki/Newton%27s_method)迭代越来越接近最优。然而，它会陷入局部最优。

但是当我们没有衍生工具时会发生什么呢？(比如你连 f(x)是什么都不知道，你只知道输出而不知道它的理论最大值)可能还有很多局部最优？

## 方法一:随机重启爬山

简单的解决方法是，猜测几个起点然后猜测一个方向，尝试做一些[随机重启爬山](https://en.wikipedia.org/wiki/Hill_climbing)。然而，要做好这件事，你必须进行多次重启，结果证明这并不比遍历整个空间好多少，尤其是对于具有狭窄全局最优区域的空间。

我们能做得更好吗？

## 方法二:模拟退火

随机重启爬山将**探索**(随机重启)和**利用**(爬山)结合在一起。也许我们可以把它分开。

模拟退火得名于冶金学，反复加热和冷却一把剑会使它比以前更坚固。

[![http://2.bp.blogspot.com/--kOlrodykkg/UbfVZ0_l5HI/AAAAAAAAAJ4/0rQ98g6tDDA/s1600/annealingAtoms.png](img/29f2d75002653c2d9fbfc11dd6bc8793.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eYP2kvrG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://2.bp.blogspot.com/--kOlrodykkg/UbfVZ0_l5HI/AAAAAAAAAJ4/0rQ98g6tDDA/s1600/annealingAtoms.png)

这个的伪代码看起来像:

```
Start with a temperature T
For a finite set of iterations:

- sample new point x+
- jump to a new point with probability given by an acceptance probability function P(x, x+, T)
- decrease temperature T

where 

P(x, x+, T) = 1 if f(x+) >= f(x) else e^((f(x+) - f(x))/T) 
```

因此，高 T 使算法表现得像一个随机采样器，当它冷却时，它开始表现得更像一个爬山者。

[![https://slideplayer.com/slide/8038378/img/6/Convergence+of+simulated+annealing.jpg](img/fa194154ce0e33a4a96d2fc4ee4309d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XiS4Bhg7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://slideplayer.com/slide/8038378/img/6/Convergence%2Bof%2Bsimulated%2Bannealing.jpg)

关于 SA 的一个显著的分析结果是，它在 X 处结束的概率等于`e ^ (f(x) / T) / Z(T)` ( [一个玻尔兹曼分布](https://en.wikipedia.org/wiki/Boltzmann_distribution))，这使得它的结束点的可能性与其结束点的适合度直接相关。

## 方法三:遗传算法

进化是一个非常好的优化器，我们可以通过“繁殖”个体点来产生更好的后代，并通过局部搜索来“变异”它们。经过多个“世代”，我们的希望是朝着全局最优的结果进化。一个很有帮助的特征是，如果个体维度/属性可以相加地组合，那么繁殖两个父母可以定期产生更好的后代。

[![https://image.slidesharecdn.com/gasbytheseatofyourgenes-160629032611/95/genetic-algorithms-programming-by-the-seat-of-your-genes-6-638.jpg?cb=1467171105](img/72575236d449c0396074d14c0578ca6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ETDI-247--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.slidesharecdn.com/gasbytheseatofyourgenes-160629032611/95/genetic-algorithms-programming-by-the-seat-of-your-genes-6-638.jpg%3Fcb%3D1467171105)

与其他方法相比，遗传算法就像并行完成的随机重启(因为群体中的每个个体都像一个线程，每一代都像一次重启)，只是“繁殖”发生时的信息“交叉”有助于将进化的总方向导向更好的地方。

伪代码看起来像:

```
Start with an initial population of size K
Repeat until converge:
  - compute fitness of all population
  - select "most fit" individuals
  - pair up individuals, replacing "least fit" via crossover/mutation 
```

正如你所想象的，秘制酱汁正在酝酿中。保存个体的两种方法是:

*   截断选择——例如，只保留人口的前一半
*   轮盘赌轮选择——采取一种概率方法，根据每个人的适合度来保留他们

交叉/变异也有细微差别。决定哪些属性可以被视为一个组涉及领域知识和隐含的假设，这些假设可能反映也可能不反映现实。

## 更多随机化优化

正如你所看到的，有很多方法可以不用微积分来解决优化问题，但是所有的方法都涉及到某种随机抽样和搜索。

我们探索过的那些算法在记忆或实际学习函数空间的结构或分布方面没有太多的能力，但是还有更多算法探索这些能力:

*   [禁忌搜索](https://en.wikipedia.org/wiki/Tabu_search)为搜索增加记忆(远离“禁忌”区域)[https://www . cc . gatech . edu/~ is bell/tutorials/mimic-tutorial . pdf](https://www.cc.gatech.edu/%7Eisbell/tutorials/mimic-tutorial.pdf)
*   [模拟:通过估计概率密度找到最优值](https://www.cc.gatech.edu/~isbell/papers/isbell-mimic-nips-1997.pdf)估计概率分布，代替遗传算法隐喻。

## 模仿

MIMIC 的核心有一个简单的想法——定义一个适应度超过阈值`theta`的点的均匀概率分布:

[![https://i.ytimg.com/vi/49Y2C_mTWD0/maxresdefault.jpg](img/44d1457def6ac20f175dc8e9d62da18c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9tBIL3ur--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ytimg.com/vi/49Y2C_mTWD0/maxresdefault.jpg)

当θ最小时，这意味着我们在整个空间内均匀采样。

当θ处于最大值时，这意味着我们收敛于一个或多个全局最优值。

```
at every iteration:

- Generate samples from population consistent with the distribution so far
- set `theta` to the nth percentile
- retain only those samples where f(x) > `theta`
- estimate the new distribution based on these samples,
- repeat 
```

因此，在 MIMIC 下，我们的任务是不断提高`theta`,直到我们只剩下 optima 周围的小区域。在这样做的过程中，我们在我们的最优值周围推断出一个(均匀的)概率分布。因为我们使用`nth`最佳百分位数作为我们的截止因子，这类似于遗传算法，但是不再有育种或基因的概念。相反，我们仍然把重点放在性能上，而不是把适应度作为我们的一个衡量标准。

与遗传算法的比较不是偶然的 MIMIC 的最佳版本通过假设空间中固有的条件分布符合“依赖树”来估计分布，依赖树是[贝叶斯网络](https://dev.to/swyx/supervised-learning-bayesian-inference-4l72)，其中每个节点(除了一个)都有一个父节点。寻找这些树超出了范围，但是[检查](https://www.youtube.com/watch?v=R-Mf9-tKC5o)[视频](https://www.youtube.com/watch?v=rCxGiKQoe4w)进行数学计算，但是这种方法的理论基础植根于最大化[互信息](https://en.wikipedia.org/wiki/Mutual_information)(在信息论术语中)和构建[最大生成树](http://shodhganga.inflibnet.ac.in/bitstream/10603/33822/4/chapter4.pdf)。虽然不是估计下一个概率分布的唯一方法，但是依赖树允许在没有指数成本的情况下捕获父子结构(最大生成树在成本上只是多项式)。

好处:

*   MIMIC 的“上升 theta”方法可以用于任何潜在的概率分布，这一事实非常酷——这里有一个简单的应用示例。
*   能够通过分布假设学习结构有助于概化
*   经验测试表明，与模拟退火和其他替代方案相比，MIMIC 使用少 2-3 个数量级的迭代来收敛，但是，由于需要估计和生成概率分布，每次迭代花费的时间要长得多，这抵消了这一点。因此，就花费的总时间而言，当评估`f(x)`的成本较高时(例如，火箭模拟、天线设计、人体测试对象)，MIMIC 工作得更好

## 我们系列的下一个

关于此主题的更多说明:

*   [维基百科上的随机化优化](https://en.wikipedia.org/wiki/Random_optimization)
*   [原始模拟纸](https://www.cc.gatech.edu/~isbell/papers/isbell-mimic-nips-1997.pdf)

希望这是对随机优化的一个很好的介绍。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

*   [概述](https://dev.to/swyx/machine-learning-an-overview-216n)
*   监督学习
    *   [决策树](https://dev.to/swyx/machine-learning-classification-learning--decision-trees-1mbh)
    *   [回归](https://dev.to/swyx/supervised-learning-regression-4d17)
    *   [神经网络](https://dev.to/swyx/supervised-learning-neural-networks-mpo)
    *   [基于实例的学习(K 最近邻)](https://dev.to/swyx/supervised-learning-instance-based-learning-and-k-nearest-neighbors-kge)
    *   [集成学习(AdaBoost)](https://dev.to/swyx/supervised-learning-ensemble-learning-lim)
    *   [内核方法&支持向量机](https://dev.to/swyx/supervised-learning-support-vector-machines-3mgk)
    *   [计算学习理论](https://dev.to/swyx/supervised-learning-computational-learning-theory-160h)
    *   [VC 尺寸](https://dev.to/swyx/supervised-learning-vc-dimensions-10b)
    *   [贝叶斯学习](https://dev.to/swyx/supervised-learning-bayesian-learning-403l)
    *   [贝叶斯推理](https://dev.to/swyx/supervised-learning-bayesian-inference-4l72)
*   无监督学习
    *   [随机优化](https://dev.to/swyx/unsupervised-learning-randomized-optimization-4c1i)
    *   [信息论](https://dev.to/swyx/unsupervised-learning-information-theory-recap-4iem)
    *   聚类-2 月 25 日这一周
    *   功能选择-3 月 4 日开始的一周
    *   功能转变-3 月 11 日开始的一周
*   强化学习
    *   马尔可夫决策过程-3 月 25 日的一周
    *   “真实”RL-4 月 1 日开始的一周
    *   博弈论-4 月 15 日的一周