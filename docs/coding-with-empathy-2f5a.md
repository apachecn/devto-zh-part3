# 移情编码

> 原文：<https://dev.to/benjamminj/coding-with-empathy-2f5a>

我是 April Wensel 和她关于“慈悲守则”的演讲的忠实粉丝。我喜欢她让我明白软件工程从根本上讲是与他人合作的观点。是的，我们拥有技术专长非常重要，但我们也需要成为协作专家。

为了有效地合作，我们需要成为拥有高度情商的工程师。虽然这肯定意味着减少工作场所的毒性，但情商也延伸到我们编写的代码。这就是我们将在这篇文章中探索的——用换位思考的方式编写代码。

## 但是代码怎么能感同身受呢？

这是一个很好的问题。毕竟，我们通常认为共情是与我们和其他人的互动相关的东西，我们认为代码是我们变得“纯粹技术化”的时候。

然而，作为我们编写的代码的作者，我们有机会为我们代码的未来维护者实践同理心。当我们编写代码时，考虑到未来的维护者阅读时的感受，我们以不同的方式对待我们的工作。突然之间，它变得不仅仅是让事情运转起来或者推出一个又一个功能。相反，我们开始关心可维护性、可读性和简单性。

对于维护者来说，从[代码中有一个熟悉的编程引用强调了移情的这一方面(还有一点情节剧)。](http://wiki.c2.com/?CodeForTheMaintainer)

> 编写代码的时候，要把最终维护你的代码的人想象成一个知道你住在哪里的暴力精神病患者。

我喜欢这句话的原因是，它挖掘了我们的生存本能，提醒我们为未来的人着想是很重要的，他们将维护我们今天编写的代码。有时候那个未来的维护者是你自己，有时候是你从未见过的人。虽然未来的维护者很少会带着杀人的意图跟踪你，但他们仍然是人。至关重要的是，我们编写的代码要让他们的工作充满乐趣，而不是活受罪。

## 那么我们如何编写共情代码呢？

许多软件行业都喜欢这种代码“干净”的想法，这是由 Robert Martin 在他的书“[干净的代码](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)”中首次提出的。虽然我喜欢这些干净的编码原则，但我认为如果我们只关注“最佳实践”或一系列规则来使我们的代码“干净”或“不干净”，我们会错过很多。

相反，我认为重要的是，我们要从换位思考的角度来看待这些软件“最佳实践”，以及它们如何影响消费我们代码的人(可能是使用内部模块 API 的其他开发人员)或更新我们代码的人。这样，它就不再是我们“必须做”的事情的清单，而是成为我们做事的方式。

让我们从换位思考的角度来快速浏览一下这些软件实践:

### 自动化测试

测试是为代码模块提供长期安全性的好方法。通过自动验证代码是否有效，它们成为抵御代价高昂的错误和回归的第一道防线。当我第一次打开一个文件，并看到它有一些全面的测试覆盖时，我感到完全自由地做出我需要的任何改变——而不用担心破坏东西！相比之下，不经过任何测试就打开一个文件可能会非常可怕。当我们编写测试时，我们不仅为我们的业务节省了时间和金钱，我们还确保了我们代码的未来维护者可以放心地修改它。

### 一致的造型

拥有一致的代码风格对未来的维护者来说是一件非常容易的事情。语法和格式问题往往是唾手可得的事情，通常可以用类似于 [ESLint](https://eslint.org/) 和[pretty](https://prettier.io/)这样的工具来实现自动化(如果你正在编写 JavaScript，对于其他语言，你可能需要不同的工具)。

拥有一致的代码风格是有同理心的，因为它可以让你的队友更快地理解代码。不强迫他们花费额外的大脑周期来阅读不一致的格式代码可以让他们专注于真正重要的事情:他们试图解决的问题。

### 选择好变量名

《干净的代码》中还有一句话，大意是“你应该像命名你的第一个孩子一样小心地命名每一个变量”。

虽然这个比喻可能有点夸张，但重点仍然是:你如何命名很重要。通过预先花费额外的(通常很小的)努力来选择一个描述性的名称，您可以帮助您的团队(以及您未来的自己)获得关于代码的上下文。现在我们知道了关于*为什么*我们的代码是这样的额外上下文，所以这是选择一个描述性变量名来提醒未来维护者这个上下文的最佳时机。

### 添加注释

如果您不能在一个变量名中包含所有的上下文，请在您选择的名称旁边留下一个有用的注释！

你可能听过一些人说“代码应该是自文档化的，所以注释是不必要的”。虽然我们确实应该努力使我们的*代码*尽可能清晰，但是一个恰当放置的注释可能会包含一大堆额外的上下文，而这些上下文实际上是无法放入变量中的。

我非常喜欢用注释来解释代码背后的*为什么*。我们真的不需要注释一个`for`循环遍历列表，但是如果它是为了解决一个特定的边缘情况，知道这个边缘情况是什么是有帮助的。当一些未来的开发人员偶然发现该代码并试图找出它在代码库中的确切原因时，添加注释会非常有价值。这样他们就知道删除代码是否安全。

### 文档

衡量代码库同理心的一个很好的方法是观察当一个新的团队成员加入时会发生什么。他们需要多长时间才能让应用程序在本地运行？他们问的是什么类型的问题？在他们最初的几次公关中，他们“做错了”什么类型的事情。

如果有一段时间没有新的团队成员加入，把自己放在初级开发人员的位置上，开始他们在公司的第一天。关于你的代码库，什么类型的东西会让人困惑或不清楚？

对抗不清晰的最好方法之一是通过一套好的文档。拥有清晰、编写良好的文档来让应用程序运行或执行某些任务，可以大大有助于使代码库友好和吸引人，而不是模糊和令人生畏。

然而，写文档时也必须考虑到同理心！确保文档足够广泛以提供清晰性，同时又不被轻视，这是一个很好的平衡。我试图评估文档清晰度的一个方法是“优化复制粘贴”。假设阅读你的文档的人会将你的例子复制粘贴到他们的代码中，因为他们试图在时间紧迫的情况下解决一个复杂的问题。以这种方式思考文档会迫使你写出完全充实的例子。

### 还有更多！

这绝对不是使我们的代码具有同情心的方法的详尽列表。事实上，任何“最佳实践”都可以通过换位思考来看待。举几个你感兴趣的最佳实践，想想它们如何积极地影响未来的维护者，你就会明白我在说什么了！

## 结论

归根结底，工程是关于交流的——我们告诉计算机要执行什么操作，我们告诉我们的队友代码应该做什么。虽然计算机不关心我们的代码有多共情，但我们的队友和未来的维护者会关心。通过在我们编写的代码中关注同理心，我们将会生产出更高质量的软件作为副产品。