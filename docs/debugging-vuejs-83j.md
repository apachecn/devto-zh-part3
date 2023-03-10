# 调试 VueJS

> 原文：<https://dev.to/dealeron/debugging-vuejs-83j>

我喜欢 JavaScript。这是一种混乱的，动态的，有时令人厌倦的爱，但它仍然是爱。JavaScript 允许您以自己的方式构建项目——您可以轻装上阵，快速改变范例。这允许快速开发，但也允许许多错误潜入您的代码，知道如何消除这些错误对于成为一名成功的 JavaScript 开发人员至关重要。

对于本文，我将重点介绍使用 [VueJS](https://vuejs.org/) 前端库进行调试，这是我们在 DealerOn 进行前端开发时使用的库，但是我用来调试代码的过程可以应用于普通的 JavaScript 和许多其他框架。我还将使用我个人项目中的代码作为例子，同时介绍我的调试方法。最后，在我们开始之前，请记住，这只是我个人的方法，并不意味着被视为唯一的方法。如果你找到了一个适合你的个人调试过程，那就欣然接受它，走这条路吧！

# 我的过程

1.  检查控制台
2.  手动跟踪
3.  视图 Dev 工具
4.  Console.log("老忠实")
5.  还有单元测试！

* * *

## 1)检查控制台

<figure>[![](img/a7b30e92a17a9811e10d7511cc3dd4e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hdLI_wZa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/peotxy2ood0x8k8e6hke.png) 

<figcaption>Vue 控制台错误</figcaption>

</figure>

这是你应该做的第一件事。Vue 在控制台中给你非常有用的警告和错误，告诉你什么时候什么地方出了问题。这是你会遇到的比较容易的问题之一。如果错误对你大喊你弄坏了什么东西，那么你是幸运的，因为通常很容易找到并修复它。Vue 非常擅长在问题出现之前就警告你(以及问题出在哪个组件上)。然后，如果您的代码中断，控制台会提供非常有用的信息，告诉您发生了什么。正如你在这里看到的，我在访问一个未定义对象的属性。我只需要找到我在哪里访问那个属性，并找到为什么它是未定义的。轻松点。

## 2)手动描摹

哦，不，但是特蕾西！我在代码中找到了我的错误所在，但我仍然不知道是什么导致了它，“你很沮丧地说。在使用一些有用的工具之前。让我们先花些时间浏览一下您的代码。这是对我作为开发人员的成长最有益的一步。看看你的代码，遵循逻辑。拿起纸和笔，或者在脑子里做，但是不要运行你自己的代码。

这不仅让你非常熟悉你的代码，而且迫使你思考和重新考虑为什么你做了一些选择。如果你正在跟踪你的代码，你发现它非常复杂，很难理解，你的代码可以更整洁吗？逻辑能以更简单的方式重做吗？哪些部分可以改变，使这更容易遵循？JavaScript 的快节奏本质会导致草率的、不必要的复杂代码。回答这些问题将迫使你作为开发人员的技能提高，并使你的代码更好，将来更不容易出错。但是，正如 JavaScript 经常出现的情况一样，您会发现这只是一个打字错误。

<figure>[![](img/76ccc9516cf82ed7e207fcf003eacbf3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xZ9cfQFw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bl3i5esiy6ujoedpgv6o.gif) 

<figcaption>45 分钟的调试就为了这个！</figcaption>

</figure>

## 3)视图 Dev 工具

[![](img/577fd3670a92c7a5b52160d0a1a05bfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UZ3ky6oJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2m5l95hezmfeosa7zdhp.gif)

有时你的错误不像打字错误那么简单。有时候你的代码是“工作的”，但是它并没有做你期望它做的事情(或者根本没有做任何事情)。Vue 开发工具在这里非常有用。它们可以作为 chrome 扩展下载，并允许您将元素作为 Vue 组件进行检查。这为您提供了一个组件在任一给定点的状态的更加详细的视图。它列出了所有的属性、计算字段、数据和触发的事件。这是非常有用的信息。

例如，假设您希望组件的数据在事件触发后发生变化。dev 工具允许您实时检查组件，以确认数据正在按照您期望的方式变化。Vue Dev Tools 还将允许您访问您在控制台中突出显示为 *$vm0* 的任何组件，然后您可以使用它来检查字段并调用方法以进行进一步的测试！

<figure>[![](img/60c68cdc86de3285f702912cfa8bdd2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kKMeRnFN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jhusm6vl5blg3le1y8f1.png) 

<figcaption>谢谢 Vue Dev 工具！</figcaption>

</figure>

## 4) Console.log(《老忠实》)

当所有其他方法都失败了，事情看起来很糟糕的时候，你最好的朋友总是 console.log。有时 Vue Dev 工具中的 props 提供的信息是不够的，你需要深入研究方法，知道某个变量在某个时刻的状态，或者某个代码块是否被命中。在开发 Vue 项目时，我发现在开发过程中间歇地在整个项目中放置 console.logs 非常有用。例如，

```
setTimeFormat () {
  if (this.e1 === ‘AM’) {
    this.ok = true
    this.collapsed = null
    this.updateSlots()
    console.log(‘SetTimeFormat AM: ’, this.e1)
    this.e1 = null
  } 
  else if (this.e1 === ‘PM’) {
    this.collapsed = true
    this.ok = null
    this.updateSlots()
    console.log(‘SetTimeFormat PM: ’, this.e1)
    this.e1 = null
  }
} 
```

现在，当这段代码运行时，您可以确认它在每次被调用时都工作正常，因为您可以让记录器告诉您值来自哪里，以及它在那个时刻的值是多少。在开发过程中放置有用且信息丰富的 console.logs 就像用许多其他语言创建单元测试一样。这在当时看起来并不总是必要的，但它可以让你省去很多麻烦。

## 5)有单元测试！

Vue Cli 实际上可以让您使用 Jest 或 Mocha 直接构建带有单元测试的项目！这些 JavaScript 测试框架允许您使用围绕它们构建的单元测试来开发组件，以确保它们输出您期望的值。我怎么强调这一点都不为过，因为许多新老开发人员都不知道你可以直接从一开始就用 Vue 进行测试！Vue 有一些关于如何用单元测试来创建组件的令人惊讶的文档，他们解释了如何比我做得更好，所以这里有一个到那个信息的[链接。](https://vuejs.org/v2/cookbook/unit-testing-vue-components.html)

# 结论

我从刚接触 JavaScript 的人那里收到的最大的抱怨之一是调试是多么的困难和乏味，但它并不一定如此！JavaScript(尤其是 Vue)有大量的工具可以让捕捉这些讨厌的 bug 变得毫无痛苦。我希望这篇文章能给你一些启示，告诉你将来遇到自己的问题时，可以采取什么步骤和使用什么工具。编码快乐！

* * *