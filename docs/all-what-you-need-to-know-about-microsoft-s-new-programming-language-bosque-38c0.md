# 关于微软的新编程语言:Bosque，你需要知道的一切

> 原文：<https://dev.to/yaser/all-what-you-need-to-know-about-microsoft-s-new-programming-language-bosque-38c0>

## 什么是博斯克？

来自 [Bosque GitHub repo](https://github.com/Microsoft/BosqueLanguage) :

> Bosque 编程语言是微软的一个研究项目，旨在研究为人类和机器编写简单、明显且易于推理的代码的语言设计

### 让我添加几个点

*   Bosque 是有类型和功能的。
*   深受 JavaScript、TypeScript 和 ML 的影响。
*   与其说是有利可图的产品，不如说是研究产品。
*   一个在麻省理工学院许可下的开源项目(**非常非微软风格**😁).

### 是什么时候发明的？

[GitHub 回购](https://github.com/Microsoft/BosqueLanguage)创建于 2019 年 3 月 3 日，[发布于 2019 年 4 月 15 日 Microsoft.com](https://www.microsoft.com/en-us/research/project/bosque-programming-language/)。

### 语言的实现是什么？

目前，该语言在 Node.js 上运行的 **TypeScript** 上实现。

## 那么，它带来了什么新的入驻呢？

### 也许是编程的新时代

[![new-era](img/e53246dba9b119b967d098a77d1795c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OBgAntvM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media2.giphy.com/media/xT8qBsqre4ULlw5c7C/giphy.gif%3Fcid%3D790b76115cbba9e131414549493bed9d)

Bosque 的作者 Mark Marron 介绍了一种新的编程模型，叫做**正则化编程**。

在他的研究论文中，他提到了[结构化编程](https://en.wikipedia.org/wiki/Structured_programming)(我们从 1970 年就知道了)可能会导致“意外的复杂性”！

> 我们相信，正如结构化编程多年前所做的那样，这种规范化的编程模型将极大地提高开发人员的生产率，提高软件质量，并开启编译器和开发人员工具开发的第二个黄金时代。

### 正则化编程与结构化编程有何不同

你可以简单的这么说:“正则化编程= **结构化编程 v2.0** ”。

这就是 Marron 在他的白皮书中告诉我们的，正则化编程是建立在结构化编程之上的...但是，它的目标是**消除复杂性的来源**。

换句话说，代码中的错误更少了！

他提到的复杂性的来源是:

*   循环、递归和不变量:我假设你已经知道了这些😆
*   可变状态和框架:可变对象和不可变对象总是令人困惑。
*   不确定的行为，比如:未初始化的变量。
*   数据不变性违规:使用数组中的索引等来访问/修改数据。
*   相等和别名:通过值，通过引用变量传递。

### 代码示例

而不是在 C 中进行通常的迭代循环，例如:

将所有数组元素乘以 2。

```
int a[5] = {1, 2, 3, 4, 5};
int b[5];
for(int i=0; i<5; i++)
    b[i] = a[i]*2; 
```

Enter fullscreen mode Exit fullscreen mode

Bosque **通过做
避免了直接从内存**循环访问数组的想法

```
var a = List[Int]@{1, 2, 3, 4, 5};
var b = a.map[Int](fn(x) => x*2); 
```

Enter fullscreen mode Exit fullscreen mode

### 现在一切都是理论上的

现在，不要急于在你的公司或工作场所使用它😆

因为，整个事情是学术性的，是为了下一代编程语言的研发&和。

引用自项目报告:

> 我们不建议在任何生产工作中使用 Bosque 语言，而是鼓励在此时只对小型/实验性项目进行实验。

### 有趣的事实

人们已经开始质疑这种编程语言的异步模型😂

[![async-model](img/ee9d024d2d83665c8f77bac4621d63fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tW04PBAE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w0vqomqsprfrzz6l7oz0.png)

### 奖金趣事

> 在接下来的几周内，很可能会有工作机会要求你在这种新的编程语言上有几年的经验。
> 
> 回复:已经有 5 年工作经验的人申请了

[![minions](img/da1b44686b8dff34c1cf8c6ea4ebb514.png)](https://i.giphy.com/media/ZqlvCTNHpqrio/giphy.gif)

### 你怎么看

我们真的需要一种全新范式的新编程语言吗？拥有博斯克真的有用吗？