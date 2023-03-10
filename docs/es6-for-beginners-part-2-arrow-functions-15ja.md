# 面向初学者的 ES6 第 2 部分(箭头功能)

> 原文：<https://dev.to/2nit/es6-for-beginners-part-2-arrow-functions-15ja>

我想向你展示由我们的开发人员 Bartosz 撰写的博客文章系列“面向初学者的 ES6”的第二部分。你可以在这里找到之前的部分[。](https://www.2n.pl/blog/es6-part-1)

我们都写了很多函数，但是让我们从一个简单的增加两个参数的函数开始。
[![](img/3de7ff8ba92326cd62bdaad4182dd781.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--fjExcrul--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zgb9ifsj29b6wx2vi3tw.png)

当然，一切都是正确的，符合 ES5 标准。首先，我还将指出，如果有人喜欢 JavaScript 中的函数是如何创建的，他们不必改变一切。ES5 的方式和以前一样正确，但是 ES6 有一些设施可以让生活变得更简单。

现在是一个代码示例，告诉我们如何按照 ES6 标准编写它。
[![](img/c133bac766be9dd9006d354fdcdd0f79.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--DUrcl7Xa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gemqkg3moixxqxh0ph8t.png)

如您所见，有两件事发生了变化。首先我们去掉了关键字*函数*，其次我们在参数后面加上了*箭头函数(= > )* 。也被一些人称为*胖箭功能*。这表明箭头是使用*等号*创建的。一个有趣的事实是，在 CoffeeScript 中还有一个所谓的*细箭头*。它的不同之处在于，它不是出现在*等号*上，而是出现在你可能猜到的*破折号*上。
我们可以到此结束，我会尽力鼓励大家改用新的 ES6 版本。然而，如果有人想知道一个相当重要的区别，我鼓励你多读几行。

我们可以尝试用更奇特的方式
[![](img/bbf47deedca5ad6c0fd376f484aae459.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e9xBJskv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xe1p9vzc7vc7myhfovv5.png)

好了，让我使用我在之前的系列文章中已经展示过的函数( [JavaScript Part 16](https://www.2n.pl/blog/javascript-part-16) )。
T3![](img/54cdc9cf65cd9b9cbcd1240fe795b490.png)T5】

[![](img/688d2d100efcc0c825ca8fb5a6d29b58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pOZTDnHV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ajt8iq36r0qwz8xjf92m.png)

您可能还记得(或者不记得),为了让我们的最后一个 console.log 第二次返回一个已更改的名称，我们必须使用如下所示的技巧。
[![](img/d7713908caa75a078b5f37b4485cacc3.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--oYpusvBz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vrp7ru54rauoioob0nzn.png)

另一种方法是使用 bind 函数，这是我在上一系列文章的最后一部分中提到的。它看起来会像这样。
[![](img/725edb449f5e76a7deff5375e58bcae4.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--LxU_qrE4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w1hqul3e1frl2way1yy5.png)

在这两种情况下，我们都会得到理论上我们想要的东西，以及理论上应该得到的东西。

[![](img/2b6a599ce934c8b894ee01dc40ba678e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3DniZYSi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z90x46rwaqzei5xpd7br.png)

在我向您展示获得想要的效果是多么简单和容易之前( *this* 指向函数所在的对象)，让我们稍微修改一下主要代码。
T3![](img/cdf951497c013605e5b11ab73ff07715.png)T5】

我知道，我知道这有很多控制台日志，但我发现这是了解特定时刻发生了什么的最佳方式。我们走吧。调用 doSomeNameMagic 方法(myObject.doSomeNameMagic())后的第一个 console.log)将为我们记录 myName 的新值。由于 doSomeNameMagic 函数被附加到对象上(与 myName 的方式相同)，*这个*变量将指向它所附加的对象。在我们的例子中-我的目标。

[![](img/84555c1b1197e95c7834179b138d4d87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CoIc5V6H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3wrq81wcao6ddaoo275i.png)

在 doSomeNameMagic 函数中，我们做了更多的事情。创建 changeName 函数，并使用一个参数调用它——“我又一次更改了我的名字！”。因为我们正在调用它，所以让我们检查其中 3 个 console.logs 的结果。

[![](img/88a0df5f54464968a7efed94e4f8e1bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1DzAdxEw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23vjxzy69918g7gfftfv.png)

首先引人注目的(第一行)是值 *this* 指向一个全局对象，而不是 myObject。这就是所谓的独立函数的行为方式。在我们的例子中，它将是一个浏览器窗口对象。由于这表示窗口对象中没有关于变量 myName 的信息，显然我们得到的值是未定义的。但是，最后一个 console.log 只是返回传递给 changeName 的参数值。

接下来的 console.log(在 life 中)也会因为和上面一样的原因指向全局对象——standalone function，简单来说就是所谓的 life(下面第一张图)。因为在函数的 changeName 中没有关于变量 myName 的信息，所以更改失败，最后一个 console.log 将简单地返回旧值(如下图所示)。

[![](img/88a0df5f54464968a7efed94e4f8e1bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1DzAdxEw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23vjxzy69918g7gfftfv.png)

虽然过一会儿我们将讨论箭头函数，但在介绍箭头函数之前，有必要记住它在 javascript 中是如何工作的。通常你会想要使用旧的语法(例如 Vue.js，但是关于这一点也许下次再说)。

第三种也是最准时的方法是只使用*箭头功能*。
T3![](img/8e6ad50ee0edd93ef58ba5d69f315401.png)T5】

当我们决定使用 ES6 解决它时，有必要了解这个特殊变量是如何工作的。嗯，很简单，Arrow 函数没有自己的这个值。该值采用 Arrow 函数所在的值。例如，如果我们写一个 doSomeNameMagic 函数作为箭头函数，它将指向...你猜-窗口对象。Arrow 函数恰恰在那些需要绑定到上下文而不是函数本身的时候大放异彩。

在下一篇文章中，我们将讨论 ES6 中的*类*。