# 什么是循环？

> 原文：<https://dev.to/codetips/what-are-loops-1aaa>

> 执行设定的一项或多项任务，直到满足给定的条件。

[![What are loops?](img/21fac3f1f7ae773b9bd8e31466c92a35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tSwhjakC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1465779171454-aa85ccf23be6%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

如果你从一开始就一直关注这些文章，你会记得在[变量](https://www.codetips.co.uk/beginner/what-is-a-variable/)文章中，我们给许多人发了一封信。

我们通过在程序的顶部创建一个变量来控制我们的信件的收信人，从而使这个过程变得更加容易。在那篇文章中讨论了这种技术的优点，但是我们没有讨论它的缺点。

如果你决定把这封信发给你的朋友和同事，总共几百个人，会怎么样？

只使用变量技术，你必须重复更新变量和运行程序的过程数百次。如果你发现任何错误，你就得从头再做一遍。正如我们在 [variables](https://codetips.co.uk/beginner/what-is-a-variable/) 文章中讨论的那样，这比保存同一封信的数百份副本要好得多，但是难道没有更简单的方法吗？

是的。循环。下面是我们在[变量](https://codetips.co.uk/beginner/what-is-a-variable/)文章中运行的代码的提示。

```
name = cfixe18918zt9o7g4nxl

Dear $name

.... body of text goes here that has a reference to the persons name 10 times .... 
```

从概念上来说，让这个过程变得简单一点，把我们要发送这封信的人数减少到 10 人，并恢复他们的名字，而不是随机的字母和数字序列。

让我们定义一下我们的人员列表:

```
recipients = James, John, Jimmy, Jane, Jake, Jemma, Joanne, Juliet, Jessica, Josh 
```

接下来，我们可以创建一个循环来逐个遍历这个列表，生成信件并像前面一样打印出来:

```
for $name in recipients {
    Dear $name

    .... body of letter ....
} 
```

在这个场景中，我们一个接一个地“循环”列表，我们将变量(`$name`)设置为当前人(首先是 James，然后是 John 等...)并且我们使用`$name`变量来构造我们的信件，就像我们之前做的那样，但是该变量现在将为每个人更新，并且为他们生成一封信。

使用这种技术，你只需要运行你的程序一次。如果你在你的信中发现一个错误，你修改它一次，然后你再次运行这个程序，它将为你的收件人列表中的每个人生成所有的信。

你现在已经减少了所有的重复，使它更容易维护。