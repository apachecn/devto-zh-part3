# 天啊，我又开始写博客了

> 原文：<https://dev.to/captainsafia/oh-gosh-im-blogging-again-3n53>

*掸掉肩上的灰尘*

所以我已经有一段时间没这么做了。

没错，我又在*写博客*了。准备好你的口粮和大量的水，因为一场灾难即将来临！

说真的，我不会开太多玩笑，说我又回到了以前的博客时代。

因为今天是正式的 12 月 1 日，这一直让我害怕，因为我没想到它会来得这么快，代码的[来临已经开始了。](https://adventofcode.com/)

我想今年我会一起玩。我以前没有做过，但我对解决一两个奇怪的编程难题并不陌生。我想我应该开始写博客，实时记录我解决这些问题的经历。我也一直在寻找学习一些新编程语言的方法。所以，我决定每天都用不同的编程语言进行挑战。

今天是第一天，所以挑战赛的文本已经发布了。你可以在这里阅读[第一天挑战](https://adventofcode.com/2018/day/1)。在继续阅读这篇博文的其余部分之前，请务必阅读这篇文章。

好吧。准备好了吗？我们走吧。

这里的基本问题是，给定一个换行符分隔的文件，该文件包含一个带有“+”或“-”的数字列表，我们希望计算出所有数字从 0 开始相加或相减后的结果数字。

我将数据保存到一个名为`frequencies.txt`的文件中，我将在整个代码中使用它。对于第一个挑战，我将从一门我熟悉的语言开始:Python。这是我十几岁时学习的第一门编程语言(除了 HTML 和 CSS)。用在这里感觉很合适。

我从实现一个非常简单的解决方案开始。我将文件读入一个列表数据结构，遍历字符串，检查以“-”或“+”开头的字符串是否执行了下面的计算。这是它的代码。

```
with open("frequencies.txt") as frequencies:
    result = 0
    numbers = frequencies.read().splitlines()
    for number in numbers:
        if number.startswith("-"):
            number_as_int = int(number[1:])
            result -= number_as_int
        elif number.startswith("+"):
            number_as_int = int(number[1:])
            result += number_as_int
        else:
            raise Exception("{} is not in the valid format.".format(number))
    print(result) 
```

这段代码是正确的，我根据代码页的出现验证了我得到的结果。我们有办法让它变得更好吗？

现在，这通常是这些博客帖子中的一部分，有人想出一些聪明的代码优化，或者使用一些特殊的语言功能来减少代码行。作为一名开源开发者和狂热的代码阅读者，我总是更喜欢保持代码的可读性。我对代码所做的任何更改都必须保持代码的可读性。

事实证明，我可以删除代码库中的`.startswith`逻辑。Python 的`int`函数可以处理带有任何符号的数字字符串的转换。所以它会适当地处理“+2”和“-3”的转换。了解了这一点，就可以将代码精简为下面的代码。

```
with open("frequencies.txt") as frequencies:
    result = 0
    numbers = frequencies.read().splitlines()
    for number in numbers:
        result += int(number)
    print(result) 
```

嗯，这不是很好吗！它还是相当可读的。快速浏览一下代码，你不会错过它的要点。我喜欢这样。

我可以利用 Python 的另一个特性 list comprehension，使代码更加简洁。这是它看起来的样子。

```
with open("frequencies.txt") as frequencies:
    result = sum([int(number) for number in frequencies.read().splitlines()])
    print(result) 
```

现在，这是我个人的底线。这段代码能完成工作吗？是的。好看又短吗？是的。通过快速浏览，很容易了解这里发生了什么吗？啊啊啊啊啊。

如果让我选择要实现的迭代，我可能会选择第二个。它很容易平衡空白和变量命名，并且不会过度使用特定于语言的特性。

以前从未读过 Python 但有编程经验的人可以很容易地进行第二次迭代，第二次就不那么容易了。我认为这是好代码最重要的一点:只要有人理解编程的基本概念，它就应该易于阅读和理解。

好的。一天的说教到此为止。希望你喜欢读这篇文章。如果你想看到解决方案并亲自尝试，你可以查看这个 GitHub repo 。

明天见！