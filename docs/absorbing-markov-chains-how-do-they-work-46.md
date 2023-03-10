# 吸收马尔可夫链，它们是如何工作的？

> 原文：<https://dev.to/legolord208/absorbing-markov-chains-how-do-they-work-46>

我相信很多人听说过吸收马尔可夫链主要是因为 Google Foobar。其中一个层次是，给定一组状态的输入，以及在不同状态之间转换的机会，找出在无限数量的步骤中从一个状态转换到“终止”状态的百分比。当我回答这个问题时，我没有时间学习这项技术。我很不好意思承认这一点，因为我无法释怀...我通过对网上的答案进行硬编码来应对挑战。我希望某个地方有一个按钮可以重做它——我现在不能检查，因为它正在维护。

我想我至少可以做些什么来弥补我的错误，那就是制作我希望拥有的指南，这样即使像我这样的人也能理解它们。我们开始吧:

# 为什么要用吸收马尔可夫链？

吸收马尔可夫链可以计算从一个状态到另一个状态的百分比，即使这样的计算永远循环。

[![Visual example](img/53ece94449507ab32a63d0362db4bc91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vgfMrOjA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/wQWtuzz.png)

它们可以简单也可以复杂，随你喜欢。

# 理解

在我继续之前，我想指出，下面的一些图像已经被重新制作成 SVG，所以你可以无限缩放它们而不损失质量。因为 dev.to 的 CDN 只是把它们转换成 png，你可以在这里找到它们全部[。](https://gist.github.com/jD91mZM2/152598bc7cdd25281e38ed0b98358ef7)

## 1。趋同；聚集

最初对我来说最大的问题是:怎么会有一个有限的答案？不会有无限多种组合吗？想象一个由现有材料一半大小的材料制成的盒子。既然可以无限除，真的有有限的答案吗？

[![The box I spoke about](img/7988351598964dffc90e215fc1762c46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---odbIost--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/28Tn0Yi.png)

...嗯！由于盒子在慢慢地向 1 无限靠近，我们可以只把 1 算作有限答案。
同样，

```
1/2 + 1/4 + 1/8 + 1/16 + ... = 0.9999999... ≈ 1 
```

请注意，我在这里使用了“几乎等于”符号。但严格来说，我根本不需要。因为在数学中，有证据表明 0.99999999...是 1！秘密在于无穷大有一些古怪的规则。在这里，我给你看一个朋友最近给我看的东西:

```
// As you know:
9x = 10x - x
// Now do this on 0.99999...
x = 0.99999...
0.99999... * 10 = 9.99999...
9.99999... - 0.99999... = 9
// Therefore...
0.99999... * 9 = 9
0.99999... = 1 
```

这很有效，因为通常当你乘以 10 时，你会在小数点前多加一个数字，在小数点后去掉一个数字。但是由于从无穷大中减去一仍然是无穷大，小数点后仍然有相同数量的数字，这意味着它们相互抵消。

## 2。矩阵速成班

在我们继续之前，我需要给大家上几堂速成课。如果你已经对这些科目感到满意，你可以跳过它们。我会尽我最大的努力保持简短和中肯，同时仍然清楚！

矩阵就像编程中的二维数组。对两个矩阵进行加法和减法运算会创建一个新矩阵，其中第一个矩阵中的每个值都与另一个矩阵中的相应值相加/相减。

[![Visual example](img/c2c3d805086f3d83834a69eedfa8ad3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tHwAHcpC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/GHaFGiY.png)

但是，将两个矩阵相乘要困难得多:每个行/列组合都会在结果矩阵中创建一个新数字，该数字是行中每个元素与列中相应元素相乘的总和。这意味着第一个矩阵中的列数必须与第二个矩阵中的行数相同。结果将具有与第一矩阵相同的行数**和与第二矩阵**相同的列数**。
是的，这很令人困惑。来，让我给你看看:**

[![Visual example](img/7b1613f54de7d9897c4fd9127af444e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--my7nwCBJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/shz3f6f.png)

然后，在上面的图像中，它会对每一行做同样的事情。这意味着不同于普通数，两个矩阵相乘可能会根据阶数给出不同的结果。

用一个自然数乘以`1`得到它自己。替代它的矩阵叫做“单位矩阵”，它是对角线上的一串数字。

## 3。矩阵求逆速成班

现在，你可能知道，数学中的求逆被写成取负一的幂，`^-1`。对于“正常”的数字，这通常是`1/n`。但是逆矩阵也存在，并且是满足以下要求的另一个矩阵:

*   原矩阵乘以逆矩阵一定是单位矩阵
*   与原矩阵相乘的逆矩阵必须是单位矩阵

不是所有矩阵都有逆矩阵，求逆矩阵有点棘手！2x2 和 3x3 矩阵有特殊的规则，但是因为我们是程序员，所以我们需要一个适用于所有大小的方法！

求逆矩阵的一种方法是高斯-乔丹消去法。这是一种很酷的数学技术，并不专门用于矩阵求逆。它的工作原理是对每一行应用各种“行操作”，以便将它转换成单位矩阵，但同时也对结果应用相同的操作。在这种情况下，结果是单位矩阵。

行操作是相对的操作，例如乘以行，加上/减去另一行的任意倍数，或者交换两行。

最简单的方法是将工作矩阵和结果矩阵放入一个大矩阵中，但是从技术上来说，没有什么可以阻止你将它们分开，这样会更有效率。

[![Visual example](img/d837a0bf217c63ffb909d1afaafda2e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--StZVkOpx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mW51gjH.png)

## 创建用于吸收马尔可夫链的输入矩阵

让我们创建一个非常*非常*基本的例子，这样我们不仅可以学习如何使用它来解决问题，还可以尝试在我们做的时候看到到底发生了什么。

[![Visual example of what I'll describe below](img/0288a81d7f323ad7c14e1b23306c8279.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1oDCSTuK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/uDVRscX.png)

如你所见，我们有一个吸收的马尔可夫链，它有 90%的几率无处可去，有 10%的几率进入吸收状态。我们也把吸收态画成有 100%的几率到达自身，但老实说，这在我见过的任何公式中都无关紧要。

我们之前讨论过收敛，因为达到最终(吸收)状态的几率是无限增加的，所以我们应该期望达到状态 2 的几率是 100%。我还会具体地说，我们应该得到达到状态 2 的 10 个步骤的估计数，因为 1/0.1 是 10，0.1 (10%)在达到 1 (100%)之前重复了多少次。

为了计算这个结果，我们需要把它写成一个矩阵。每一行将是一个州，而该行中的每一列将是进入与该列具有相同行号的州的机会。我建议将概率指定为一个分数，*特别是*如果你正在做计算这个的东西，因为分数不会像浮点数那样失去精度。
像往常一样，如果我告诉你会更容易:

[![Visual example of an input matrix](img/c86cc31022ea8c7f9f2f7bb06491e8be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gbS1KQJS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/N3JO8eH.png)

### 创建基础矩阵

我们需要做一些事情，这些事情适用于您可能想要做的所有操作。

首先，我们需要将矩阵中的非吸收态和吸收态分开。对所有的状态进行排序，使吸收状态在最后(或者开始，如果你真的真的想的话。但是对于本文，我将假设 end)。这将允许您将输入矩阵分成 4 部分(每个维度 2 部分):

[![Sliced matrix](img/f2a96e447f4cc8d6f918025fed78e35c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LLwV-leO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vnRIuNW.png)

其次，我们需要一个“基础矩阵”。这将允许我们从链中获取属性。基础矩阵中的每个条目将包含从具有与行号相同的索引的状态开始的对具有与列相同的索引的状态的平均访问次数。换句话说，位置`(x, y)`处的每个条目必须包含从状态 *y* 开始对状态 *x* 的平均访问。

关于马尔可夫链的一个很酷的事情是，你可以得到一个矩阵，其中位置`(x, y)`处的每一项都包含从状态 *y* 转移到状态 *x* 的概率，正好是 *k* 步，简单地通过取`Q^k`(是的，这是一个指数，那些也适用于矩阵，Q*Q*Q*Q 等等)。

为了得到基本矩阵，我们想把从 0 到无穷大的每一步的概率加起来。幸运的是，已经证明使用公式`N = (I - Q)^-1`可以得到完全相同的结果。

[![Wikipedia image of the math I just described](img/2d7a2455f40e7c8b0b9593dfc05f2811.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HF1gMGmD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikimedia.org/api/rest_v1/media/math/render/svg/703d6eddb5e051bfa37772992a2e19e8dbaab5e6)

*^Image ^stolen ^from ^ [维基百科](https://en.wikipedia.org/wiki/Absorbing_Markov_chain#Fundamental_matrix)T3】*

于是遵循方程`N = (I - Q)^-1`，其中 N 是基础矩阵，Q 是我们刚刚建立的子矩阵，I 是与 Q 大小相同的单位矩阵(永远是正方形)，就可以计算出基础矩阵。

[![Calculating the fundamental matrix](img/47bf84a070510ca86d7bd063108e89d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2htmRU68--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/aQkJ2dQ.png)

我们的基本矩阵现在是`[10]`。

### 获取达到吸收状态的平均步数

由于基础矩阵中的每一行都有列，每一列都给出访问具有与列相同索引的状态的平均次数，从具有该行索引的状态开始，我们可以通过将每一行相加得到要完成的平均步骤数，从而得到从该处开始的平均访问总数。

在数学中，对 NxN 矩阵中的每一行求和的技巧是将其乘以 1xN (1 列，N 行)矩阵，其中每一行都是 1。
公式为`t = N1`，其中`t`为每个起始状态到吸收状态的平均步数矩阵，`1`为前面提到的 1xN 矩阵。

显然，因为我们的示例矩阵是 1x1，所以相乘非常简单...

[![Multiplying 10 by 1](img/cc994587587cda7db7f8d196d2172832.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GMH8DAV3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/YEW9Lab.png)

正如你所看到的，达到吸收状态的平均步骤数是 10，正如我们所猜测的！

### 获得达到每个吸收状态的百分比几率

所以，这可能是你一直想读的一步。毕竟，获得每个状态的百分比听起来比达到任何(吸收)状态所需的平均步骤数更有趣。

这个的公式是`B = NR`。还记得我们之前切的子矩阵里的`R`吗？将包含对每个非吸收态的平均访问次数的矩阵与包含在一步中转变为吸收态的概率的矩阵相乘，我们得到在任意步数之后进入吸收态的概率。

[![Multiplying 10 by 1/10](img/69bd9f120c54ccc2430e68c5aed96399.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OexTiqs6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/kp1WFWE.png)

最终结果是一个矩阵，其中每一列都是具有该索引的状态必须到达该行的索引的机会。在这种情况下，非吸收状态 1 有 1 (100%)的机会达到吸收状态 1(实际上是状态 2)。似乎是正确的！

[![Another example](img/f840ddc40f58ba6908e08c7b14c71db6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mPlmaxx8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/CJUmrjw.png)

^( *示例输出这里是[https://en . Wikipedia . org/wiki/absorbing _ Markov _ chain # string _ generation](https://en.wikipedia.org/wiki/Absorbing_Markov_chain#String_generation)*的输出)

# 期末笔记

有一句名言:

> 如果不能简单的解释，说明你理解的不够好。

遗憾的是，^(找不到是谁说的，但它经常被误认为是爱因斯坦说的。)

我承认我确实不太理解这种巫术。但是我希望我的文章仍然能让您有更多的了解。维基百科和我发现的其他来源似乎只是陈述事实，但不要试图为 5 岁的孩子解释它们。我可能也解释不清楚，但至少我可以试一试。

我在这里实现了这个:[数学类型](https://gitlab.com/jD91mZM2/math-types)

*   [矩阵乘法](https://gitlab.com/jD91mZM2/math-types/blob/b6cf5ac29d714532069cb2091cc5a4399ff7975e/src/matrix.rs#L265-283)
*   [矩阵求逆](https://gitlab.com/jD91mZM2/math-types/blob/b6cf5ac29d714532069cb2091cc5a4399ff7975e/src/matrix.rs#L138-211)
*   [分数数据类型(精度可选)](https://gitlab.com/jD91mZM2/math-types/blob/b6cf5ac29d714532069cb2091cc5a4399ff7975e/src/fraction.rs)
*   [程序示例](https://gitlab.com/jD91mZM2/math-types/blob/b6cf5ac29d714532069cb2091cc5a4399ff7975e/examples/absorbing-markov-chains.rs)