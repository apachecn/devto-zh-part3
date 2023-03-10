# 我的 markdown 解析器的用户故事(MDL 日志#2)

> 原文：<https://dev.to/mortoray/a-user-story-for-my-markdown-parser-mdl-log-2-3o61>

是时候创建第一个用户故事了。理解我为什么要做解析器以及需要什么特性是很重要的。我将把这些用户故事呈现给除我之外的其他人，因为把我自己作为一个用户来谈论听起来很奇怪。另外，它会帮助我包容其他人的欲望。虽然我是最初的市场，但最终这个工具不仅仅是为我准备的。

## 用户:Keziah

[![Woman writing on a computer](img/82d4e7121992c9dbaa7be5aa0057d744.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XF4nBtqb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u7vn82cizun3tu3cnni3.jpg)

这是 Keziah，她 32 岁，住在一个大城市。她是一家软件开发公司的作家。她的工作范围从用户文档到案例研究，再到客户推销。她与编程团队以及业务开发团队密切合作。

尽管她所有的文章都有相似的重文本格式，但她最终对每个目标使用了不同的工具。不得不跳来跳去妨碍了她的工作，这开始让她恼火。她想要一个简化的工作流程——所有的普通工作都用同一个工具来完成。

> 如果你想了解更多关于理解用户的知识，请关注我的 Skillshare 。月底我会开一门课，“写完美的用户故事”。

现在我们对 Keziah 有了一些了解，什么类型的特性可能会吸引她呢？让我们在她的领域里表达一些东西:

"一个用户会用 MDL 格式化一个文档，因为他们希望在多个地方发布."

其中 MDL 是这个项目中的文档格式。这个用户故事对 Keziah 有意义吗？根据她的技术经验，我们假设她可以使用基于文本的文档格式。如果她只用词，这个假设可能是无效的。

我们认为在多个地方出版可以简化她的工作流程，这是她的愿望。在这里更详细一点是有意义的。她不打算在所有地方发表，而是在特定的地方。这些可能是什么:假设它们是电子书格式，pdf 格式，论坛中的降价，以及一些文档的 HTML 格式。

这个用户画像和第一个故事给了我的项目动力。我需要创建这个通用的 MDL 格式。许多功能不能被称为用户故事，但是记住用户是有价值的。我不想被那些不能为最终用户提供价值的高度技术化的特性所牵制。我想专注于提供即时价值的特性——使产品尽早可用。

## 解析器更新

我没有像我希望的那样有足够的时间做这个项目。但是我觉得在发布这个更新之前，我需要一个良好的开端。我想我有。

本文由解析器*处理。它读取 markdown 并将其格式化回 markdown。嗯，从技术上讲，它读 MDL，但大部分语法与现在的 Markdown 相同。唯一可以看到的不同是在文章结尾的简介中。我在源文本中只使用了`@Blurb`,它被翻译成水平线和斜体部分。

> *图像的包含和前置内容的制作尚不在处理器的能力范围之内。它们是在出口后手工添加的。

代码仍然很乱，这对于现阶段的工具来说是很典型的。我正在做一个概念验证，部分是最小可行的产品。在我了解整个系统如何工作之前，我不想构建可扩展性特性。我不相信架构可以被预先设计出来——相反，我认为进化会产生更多有用的结果。

* * *

*这是 [MDL 文档处理器项目](https://github.com/mortoray/mdl/)的开发日志条目。*