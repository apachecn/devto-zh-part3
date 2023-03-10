# 终极代码评审博客文章系列

> 原文：<https://dev.to/mgreiler/the-ultimate-code-review-blog-post-series-3k2>

<figure>[![Two kids learning together as a symbol for code reviews](img/5cee7de43f7e60f8c378328f4e99e984.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v-dxtbLj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.michaelagreiler.com/wp-content/uploads/2019/03/2.png%3Ffit%3D800%252C400%26ssl%3D1) 

<figcaption>代码回顾:在微软工作的经验教训
图片由 [NESA 制作](https://unsplash.com/photos/YgOCJz9uGMk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/code-review?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
</figcaption>

</figure>

帖子[终极代码评审博客帖子系列](https://www.michaelagreiler.com/code-review-blog-post-series/)首先出现在[医生麦克凯拉](https://www.michaelagreiler.com)上。

* * *

在这个代码评审博客系列中，我分享了我在代码评审方面的经验和教训。首先，我向您展示了代码评审的最佳实践，以提高您的代码质量。但是，您也学习了在执行代码审查时应该避免哪些陷阱。

这些代码评审博客文章的基础是我在微软分析和改进代码评审实践和工具的经验。我与数百名工程师合作，分析了数千份代码审查。您可以将这个代码评审博客文章系列看作是学习代码评审最佳实践的速成课程，或者是反思您自己的评审实践的机会。

<figure>[![](img/8e0bab11b598d59acee921558768e654.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---CAx9_XS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.michaelagreiler.com/wp-content/uploads/2019/05/E-Book-Preview.jpg%3Fw%3D800%26ssl%3D1) 

<figcaption>
[独家代码评论最佳实践电子书](https://www.michaelagreiler.com/code-review-e-book/)给我的订阅者。</figcaption>

T9】</figure>

我为我的电子邮件订阅者准备了一本独家的[代码评审电子书](https://www.michaelagreiler.com/code-review-e-book/)，帮助你记住代码评审的最佳实践。我还添加了其他关于代码审查的深刻见解和总结。立即获取 12 页的代码评审洞察。还不是订户？报名就好。

## 代码评审概述

代码审查是一种广泛采用和适应的工程实践。它的主要目的是提高软件质量。此外，团队成员之间共享知识是代码评审的另一个重要好处。

尽管代码审查提供了一些其他的好处，但是您必须意识到要遵循最佳实践。否则，你可能会陷入几个陷阱。

## 微软的代码审查流程

我作为一名软件工程师和研究员在微软工作了几年。我的主要焦点是分析和改进工程实践和工具。我关注的一个关键领域是代码审查。

在这个博客系列中，我将分享关于微软代码审查的关键见解和经验教训。所有这些都有研究的支持，并基于高性能工程团队的实际经验和知识。在微软，我进行了几项大规模的研究，涉及数千名工程师和数百万条代码审查意见。在这个代码评审博客文章系列中介绍的经验教训来自于那些经验研究。

<figure>[![People working on computers in a group](img/50d8fc9836e923cf91a74da832fe681c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PZWe_TTf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.michaelagreiler.com/wp-content/uploads/2019/03/code_review_nesa-by-makers-737053-unsplash.jpg%3Fw%3D800%26ssl%3D1) 

<figcaption>借助工具进行同行代码评审，或者也可以“越过肩膀”
拍照通过[拉结](https://unsplash.com/photos/o3tIY5pIork?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[退溅](https://unsplash.com/search/photos/learning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

## 博文系列

该系列由 9 篇博客文章组成。你可以单独阅读它们中的每一个。尽管如此，你可能会从连续阅读中得到最大的收获。

在接下来的几周，我将发布以下内容:

1.  **[微软的代码评审](https://www.michaelagreiler.com/code-reviews-at-microsoft-how-to-code-review-at-a-large-software-company/)** 在这篇文章中，我解释了微软的一个普通评审过程是怎样的，以及微软最常用的代码评审工具是什么。
2.  **[代码审查陷阱:了解哪些问题拖慢了你的团队](https://www.michaelagreiler.com/code-review-pitfalls-slow-down/)** 在这篇文章中，我深入探讨了团队在实践代码审查时面临的主要代码审查陷阱。
3.  [**行之有效的代码评审最佳实践**](https://www.michaelagreiler.com/code-review-best-practices/) 了解哪些代码评审最佳实践使您的团队富有成效，并提高代码评审反馈的价值。
4.  **[如何给出好的反馈](https://www.michaelagreiler.com/great-code-review-feedback/)** 代码评审的好处随着反馈的质量和价值而起伏。在这篇文章中，我向你展示了一些行之有效的方法来提高你的代码评审反馈。
5.  谷歌的 **[代码审查](https://www.michaelagreiler.com/code-reviews-at-google/)** 谷歌的代码审查是轻量级的，快速的。实际上，代码审查不到 5 个小时就能完成。这比大多数其他大型软件公司要快得多。在这篇文章中，我会告诉你为什么。
6.  代码评审清单 如果你想同时提高你的生产力和严谨性，代码评审清单就是你所追求的。这个也是可以下载的。
7.  **如何给出有用的代码评审意见？不是每个评论都是有用的。应该避免无用的评论。在这篇文章中，我解释了什么是真正有用的注释。**
8.  即使你知道所有的最佳实践，你也必须记住一些权衡。比如你是偏向复习速度还是复习严谨？在本文中，我将深入探讨这个话题。
9.  **总结帖**

* * *

**不要错过由 **[订阅](https://www.michaelagreiler.com/subscribe/)** 到我的邮件列表的这个代码评审博客系列的任何帖子**。

作为订阅者，除了我关于软件工程的令人敬畏的电子邮件之外，你还可以从我为你准备的独家款待中获益，比如我的[代码评审电子书](https://www.michaelagreiler.com/code-review-e-book/)。

我在推特上很活跃。让我们在那里连接。

* * *

帖子[终极代码评审博客帖子系列](https://www.michaelagreiler.com/code-review-blog-post-series/)首先出现在[医生麦克凯拉](https://www.michaelagreiler.com)上。