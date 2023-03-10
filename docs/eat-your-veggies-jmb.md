# 吃你的蔬菜

> 原文：<https://dev.to/ashwinv11/eat-your-veggies-jmb>

###### 照片由[南希尼·库马尔](https://unsplash.com/photos/C0fnsAxztjc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/broccoli?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

作为开发人员，我们面对的是软件工程的浩瀚。这一点，以及该领域的变化速度，导致我们跳过对我们的职业生涯至关重要的关键知识。我们倾向于跳到下一个热门技术，而不是回溯并解决这些明显的知识缺口。也许不是今天，但有一天你会醒来，意识到你应该听妈妈的话，多吃些花椰菜。

## 没有人喜欢花椰菜🤢

每个工程师都有一个他们不自信的领域。对你来说，这可能是动态编程(豆芽)，或者贪婪算法(卷心菜)，甚至是汇编(茄子)。对我来说，递归就是我的西兰花。我不知道为什么会这样，但自从我开始编程生涯以来，我一直讨厌递归。我就是无法理解。每当我不得不在代码库中使用它时，我会拖延到最后一刻，我会急于让一些东西工作——通常都是由于堆栈溢出。

我对递归的厌恶变得如此糟糕，以至于每当我知道我必须实现递归解决方案时，我都会感到身体上的焦虑！在多年逃避西兰花之后，我被安排到一个我再也无法逃避的工作项目中。我有这个巨大的未知数据结构，它可以有无限的嵌套层次，以及在这些层次上可能存在的多种排列。我需要浏览这些数据，并允许以可预测、可回放的方式进行更改，同时不丢失我所在位置的上下文。

我第一次实现这个“步行者”是相当随意地放在一起。我引入了一个外部工具来为我完成这一步，因为我希望是最好的，并对从这一抽象中引入的 bug 贴上了创可贴。然而，不知何故，我能够交付半个工作代码，让我的团队跨越了最后期限。

但是我不开心。我深知我的代码不稳定。我知道我走了一条容易的捷径，但这条捷径会让我自食其果。这就是发生的事情。

## 无论西兰花长什么样，都要尝一尝🤔

几个星期后，这款步行机已经过实际使用测试。被归档的 bug 数量越来越多，我开始觉得越来越少。最后，我决定**让**重写这篇文章。只是这一次，我至少会尽力做好。

我仍然使用外部工具作为起点，只是这一次我深入到源代码并试图理解它实际上是如何工作的。然后，我拿出一支笔和一张纸，准确地写下我希望在每一个新的水平上达到的目标。这让我重放了 walker 如何处理真实数据——我知道这在过去对我非常有效。在最终理解了每一步我想要什么之后，我开始实现我的算法。

经过几天在可能的边缘情况和排列上的挣扎，我终于能够发布我的新递归 walker 了！这一次，我确切地知道发生了什么，这使得处理更多的边缘情况和突变相关的错误变得更加容易。

## (和西兰花)谈恋爱需要时间🙃

我们意识到改变原始数据会引入更多的错误，并且比仅仅跟踪数据的变化更难维护。这是我又一次尝试西兰花的机会，只是这次我不那么紧张了。我很自豪地说，我能够以一种更加“智能”的方式在不到前一次迭代三分之一时间的**内重写 walker！**

什么都不知道也没关系。我肯定还不是一个西兰花爱好者，但至少现在我知道我可以忍受它。但是我知道，在我尝试了每一道新的西兰花菜之后，我会离成功更近一步。

又及:我其实是一个超级美食家，喜欢所有的蔬菜。抱歉花椰菜。🥦