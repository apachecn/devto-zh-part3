# 了解二叉查找树

> 原文：<https://dev.to/trilemaestro92/understanding-binary-search-trees--227p>

问候程序员伙伴。

在这一期中，我将分解二叉树数据结构...你将学习基本原理，它们是如何工作的，如何遍历/走过一棵树，并提出问题...他们被高估了吗？剧透...他们不是。

## 什么是二叉树？

首先，它是用于有效地将键映射到值的许多数据结构之一，以便进行有效的搜索/检索、插入和/或删除。

特别地，二叉树具有一个根，该根具有两个子节点，这两个子节点各具有两个子节点，并且最终到达没有任何子节点的最后节点的叶子。如果这没有多大意义，看看下面的二叉树的例子图片。

[![Alt text of image](img/0e57bb6d7c67e3b8d26dcd16f66d2df1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eGBOcKkL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/x8d4D.png)

1.  8 是根(应该只有一个根)
2.  4 是左侧子节点，12 是右侧子节点
3.  4 和 8 都有两个子节点，依此类推。
4.  1，3，5，7，9，11，13，15 都是叶子。
5.  它被称为二叉树，因为每个节点最多只能有 2 个子节点。(可能有一个子节点，这仅仅意味着另一个子节点为空)

## 怎么用？

上面的二叉树也是一个二叉查找树的完美例子，它规定根左边的每个子节点必须小于根节点，而根右边的每个子节点必须大于根节点。

这种排序属性使我们能够快速搜索整个树。让我们看看另一棵树，这次让*在*中搜索数字 83

[![Alt text of image](img/969d3c9d6bbea3f98ad67de0d6645fd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7fb_LcIr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kq4t9bceurqg3pir8hzp.JPG)

步骤:

1.  我们看到根(84)大于 83，因此我们可以从右边切掉所有的子节点。
2.  这样我们就剩下根的左侧子节点 54，从那里我们看到 83 高于 54，因此我们将转到它的右侧子节点 73。
3.  73 左子为 null，但它的右子是 83！我们要搜索的号码。

现在假设我们想要*插入*数字 90。这些步骤类似于搜索。

步骤:

1.  90 比根号高，所以我们向右到 87。
2.  90 仍然高于 87，因此我们将从节点 87 向右移动到 94。
3.  90 小于 94，所以我们将向左，在这里它是空的。
4.  因为它是空的，所以我们将在这里插入 90。查看以下结果

[![Alt text of image](img/4091024ad6275717c94dd72a9972de6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0UNk__FC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ycsap2xonuthof1yub4k.JPG)

移除树中的节点稍微复杂一点。让我展示给你看。

让我们来看看这个新的二叉查找树。

[![Alt text of image](img/cb6f7531e9cbc4e758b540b4ad7e4227.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wb5ZyBhU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b0wm2h2251ounujpdapo.JPG)

假设我想删除 13。

步骤:

1.  从小于 13 的根(89)开始，所以我们移动到左边的子节点 13。
2.  从这里开始，函数将询问 13 是否是一片叶子，它不是。
3.  所以它会转到右边，在 34 处，询问 34 是否是一片叶子，而它不是。
4.  由于 13 小于 34，我们现在将移动到 34，21 的左侧子节点。
5.  它会问 21 是不是一片叶子，但它不是。
6.  由于 13 小于 21，我们将在 20 处再次移动到左边的子节点。
7.  同样在 20，我们知道它不是一个叶子，所以我们将移动到左边的子节点到 14
8.  在 14 处，他们将询问该节点是否是叶子，答案是肯定的。
9.  现在，该函数将用它的“后继者”14 替换 13。结果如下

[![Alt text of image](img/1cfa59f32dea0d0b90c835604841fef3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--42KyKZgb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1qn0r7kgdvcrzrssxojy.JPG)

## 让我们穿越吧！

我们刚刚学习了如何在二叉树中搜索、插入和删除节点。现在让我们看看如何遍历一个。

遍历二叉树有三种方法:

1.  简单来说，我们将从左节点到根节点，而不是右节点。

2.  **前序**
    简单来说，我们将从根开始，到左节点，再到右节点。

3.  **后序**
    简单来说我们会从左节点，到右节点，再到根节点。

*通常 Inorder 遍历是最常见的，因为它允许按顺序打印节点。*

让我们以这棵树为例:
[![Alt text of image](img/d5bc4a7e3a0e03264c1b87dceabdb81d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q0_MaHBA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5gjr8lhomzvl0j9ukyd0.JPG)

顺序遍历的步骤是:

A.从根(75)开始，向左侧节点 3 移动，然后我们可以问 3 是否有子左侧节点？因为 3 没有子左节点。我们将记录 3，然后移动到它的右子节点 7。

**(3)**
b . 7 有子左节点吗？不，我们现在将记录 7，并移动到它的右子节点 24。

**(3，7)**
c . 24 有左子节点吗？是的，是 8 号。现在我们问 8 是否有左子节点？不，我们将记录 8。

**(3，7，8)**
D .我们将移回 24 并记录 24，因为它的子节点 8 已经被记录，然后移动到它的右子节点 59。

**(3，7，8，24)**
e . 59 有左子节点吗？没有，所以我们现在将记录 59。然后遍历回根。

**(3，7，8，24，59)**

F.因为我们已经注册了根的所有左子节点。我们现在可以记录 75。

**(3，7，8，24，59，75)**

G.现在我们移动到根节点的右边，到 95。并且问它有没有左子节点？是的，现在来看 91。91 有左子节点吗？不，我们现在可以记录 91。

**(3，7，8，24，59，75，91)**

H.因为我们已经注册了 95 的所有左子节点。我们现在可以记录 95。

**(3，7，8，24，59，75，91，95)**

I .然后我们可以继续到 95 右子节点 99。并且问，99 有左子节点吗？不，我们现在可以记录 99。

**(3，7，8，24，59，75，91，95，99)**

通过使用 Inorder 遍历这个二叉树，可以看到节点是按顺序打印的。我不会在这篇文章中过多地讨论预订单或后订单，但是你可以使用这两种方法查看下面这些来自 Michael Sambol 的易于理解的视频。

预订:[https://youtu.be/1WxLM2hwL-U](https://youtu.be/1WxLM2hwL-U)
预订:[https://www.youtube.com/watch?v=4zVdfkpcT6U](https://www.youtube.com/watch?v=4zVdfkpcT6U)

你为什么需要了解二叉查找树？

二分搜索法树是能够有效地以排序的顺序维护动态变化的数据集的集合。拥有一个有序的数组对于很多任务都是有用的，因为它使得二分搜索法能够被用来有效地定位元素。

按照大 O 表示法，搜索、插入和删除数据的平均时间是θ(log(n))。

谁知道呢，你可能会在面试中被问到关于二叉查找树和如何继续前进的问题。

希望这篇文章是有帮助的。

感谢你阅读我！