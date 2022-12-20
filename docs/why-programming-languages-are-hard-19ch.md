# 为什么编程语言很难

> 原文：<https://dev.to/marek/why-programming-languages-are-hard-19ch>

几年前，当我第一次尝试学习 Python 时，在开始使用它的前五分钟内，我遇到了这些错误消息之一:

```
SyntaxError: invalid syntax 
```

Enter fullscreen mode Exit fullscreen mode

这听起来像 Python 在告诉我:你做了坏事，你应该感到难过。

我确实感觉很糟糕，部分原因是我不知道 SyntaxError 是什么意思，短语“无效语法”根本没有帮助澄清任何事情。

今天，我对编程语言和解析器的工作方式有了更多的了解。但是错误信息仍然是坏的。这是糟糕的 UX，或者更准确地说:糟糕的 DX(开发者体验)。

如果你是第一次学习编程，那么这个错误在两个层面上是不好的体验:

1.  首先，它没有告诉你刚刚发生了什么。我做错什么了吗？我的程序做错了什么吗？还是 Python 做错了？
2.  其次，它没有指出错误的原因是什么，或者我可以尝试做些什么来修复它。

现在，现实是语法错误一直在发生(即使在多年的经验之后),开发人员的大部分时间都花在了修复 bug 和解决各种编码问题上。

但是作为一个初学者，你不知道这些。

当你是一个初学者时，这个错误实际上听起来好像没什么大不了的，更关键的是，**听起来好像你做错了什么**。

## 问题:编程语言是由程序员创造的

编程语言是由程序员为程序员创造的。

当你想让编程更容易理解和学习时，这是一个问题。这是一个目标受众不匹配的问题。

除此之外，编程语言的创建方式也有局限性。我们最终得到类似“无效语法”的消息，不仅仅是因为有人写了这样的错误消息，还因为语言语法和解析器的工作方式。也许解析器实际上无法访问导致语法错误的确切语法规则，因为它是这样编码的。因此，由于技术原因，错误消息不会有更大的帮助。

很有可能这并不是 Guido 在编写第一版 Python 解析器时优先考虑的事情之一。他是为自己和其他程序员写的。

毕竟，当 Python 在 20 世纪 90 年代初首次发布时，它已经比当时的大多数其他语言简单易学得多了。(我认为这在今天仍然是正确的。)

## 第二个问题:被设计成易于学习的编程语言往往是无用的

有许多编程语言被设计成更容易学习，对初学者来说也更容易理解。他们中很少人是成功的或受欢迎的，问题是你不能和他们一起做任何严肃的工作。

他们可能会实现让人们更容易开始编程的目标。但是很多时候，他们没有说明编程可以解决什么问题，以及它如何适应一个更大的图景。

所以在某种程度上，他们实际上是在完成目标之前破坏了自己。

如果你想向人们展示为什么编程是值得的，并帮助他们理解如何使用它，你需要向他们展示如何解决一个现实世界的问题。否则，大多数时候，它最终看起来就像一个玩具。

## HyperCard 和 Excel

HyperCard 是 20 世纪 80 年代后期出现的对大量计算机用户产生影响的应用程序之一，我敢打赌，它激励了许多人成为程序员。

HyperCard 内部可用的编程语言 HyperTalk 并不是特别好。但它是针对非程序员的，同时又有能力解决实际问题。

> 许多认为自己永远无法编写计算机程序的人开始使用 HyperCard 来完成许多自动化和原型任务，这甚至让它的创造者都感到惊讶。(维基百科)

Excel 是另一个将编程带给非程序员的巨大应用。

仅仅基于公式和单元格，Excel 的模型设法做到一半是代码，一半是图形。它足够通用，可以解决一系列的问题，但它仍然坚持一个最佳点:它专门针对可以用电子表格表示的问题。这就是 Excel 如此强大的原因:它不会尝试做所有的事情。这是一个电子表格。

## 使一个工具可定制，直到它变成一种设计糟糕的编程语言

在软件中，我们总是面临着在编写解决特定解决方案的代码和编写允许用户有足够的可定制性来解决各种问题的代码之间的权衡。

这是工具特异性问题。

Excel 和 HyperCard 是工具，不是编程语言。但是他们都被拉向远离特殊性的方向，试图为每个人解决所有问题。

一个软件最容易定制的形式是什么？它本身就是一种编程语言。因此，如果你将这些工具的可定制性发挥到极致，它们就会变成设计糟糕的编程语言。

为什么 HyperCard 今天死了？我认为这是因为它太普通了，不适合任何特定的问题。

虽然它确实给了非程序员很大的权力，但最终证明软件的经济性是这样的，为特定的问题开发特定的工具通常更有价值。

## 程序员解决自己的问题

如果你让程序员自己使用他们的设备，这是很正常的期望，他们会制作工具来使他们自己的工作更容易。

有时程序员犯了某种精英主义的错误，他们设置了进入的障碍，而不是让新人更容易。

一旦你克服了最初的障碍，学会了如何写代码，你就会开始忘记刚开始时的感觉。

当你试图学习你的第一门编程语言的基本语法时，你很容易忘记自己是多么无助，一切似乎都与你背道而驰。

每个人都是从初学者开始。

## 那么，我们该怎么做才能让事情变得更好呢？

抛开咆哮不谈，这里有一些可行的建议。

*   如果你是一个有经验的程序员，分享你第一次接触编程的故事，以表明我们在掌握它之前都是无知和胆怯的。
*   如果你是一个初学者，试着原谅编程语言的创建者那些含糊的和无用的错误信息。创建一门编程语言是一项繁重的工作，如何使它对初学者来说简单，同时又能对解决实际问题有用，这并不总是显而易见的。
*   同样，如果你是初学者，你可以通过分享你的经验和反馈来帮助编程语言社区。很多时候，有经验的程序员需要一个提醒，新手看事情的方式和他们不一样，他们有时需要回到现实。
*   原谅初学者友好的编程语言，如果你不能用它们做出任何有用的东西。它们仍然是有价值的学习工具，如果它们能让人们兴奋地学习更多关于编程的知识，那么这就是我们所能期望的最好结果。

让我以最后一件事来结束。

当我说这些语言毫无用处时，我太苛刻了，但是我是指现实世界中所谓的“商业”问题。

如果你能使用一个工具来制作酷的或有创意的东西，然后展示这些东西，那么这个工具对人类是 100%有用和有价值的。让我们多做一些。