# Web 组件是一个东西吗？

> 原文：<https://dev.to/granze/are-web-components-a-thing-3ae7>

在与不太喜欢 Web 组件的人交谈时，我发现他们觉得 Web 组件不会有任何发展。这可能是因为他们有一个巨大的炒作，因为，比方说，一年前，但没有在过去几个月。

对于那些关注的人来说，在过去的几个月里，有两条非常重要的消息。首先，微软宣布他们开始致力于自定义元素和 Shadow DOM 的实现(他们正在切换到 Chromium)。火狐浏览器的支持终于在 10 月发布的 63 版中登陆了！在我看来，未来看起来非常光明。

尽管 Web 组件规范已经存在很多年了，但是似乎很多开发人员都没有掌握这个标准的基础。围绕这个规范有很多误解和神话。让我们试着澄清一些方面。

## 不能将对象作为属性传递(对还是错？)

> ![MICHAEL JACKSON profile image](img/09075695d749d696087604c65c209fb5.png)迈克尔杰克逊[@迈克尔杰克逊](https://dev.to/mjackson)![twitter logo](img/ca5dfdb28b446702d18cbfa9843f8167.png)🎯亚当把它钉在这里。这是 web 组件可以从 React 中学到的一件大事，它将把它们的可用性提高 100 倍。[twitter.com/AdamRackis/sta…](https://t.co/veDQlZgWVC)2018 年 10 月 12 日下午 13:34亚当·拉克斯@亚当·拉克斯[@mjackson](https://dev.to/mjackson) 关于国家管理的 wc 故事很野。"比如，我如何将一个对象或函数作为道具传递给 wc？"“抱歉，html 规范将属性限制为字符串”😐😐😐如果我们不能，在平台上添加狗屎有什么意义.....给平台加屎？[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1050741567659495424)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1050741567659495424)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1050741567659495424)11

我看了这条推文后感到很困惑，因为我意识到即使是经验开发者也不明白自定义元素是如何工作的。我并不是说他们必须喜欢它们，但是你不能把一个对象作为属性来传递，这是完全错误的说法。那么，我如何将一个对象作为属性传递呢？简单回答:通过属性！:)
看看下面这张图，你会有更好的想法。

<figure>

[![Codepen example](img/ef22b86bb637c907df515cc2f5a365df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7DuT-YmX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mqeyxsbyqo1jog4wrrlj.png)

<figcaption>Property values are managed with getters and setters</figcaption>

</figure>

在上面的例子中，我们定义了一个包含名为 myObj 的属性的自定义元素。要将对象传递给属性，首先需要选择元素

`let el = document.querySelector('my-el')`

然后使用点符号赋值，在本例中是一个标准对象

`el.myObj = {a: 'test'}`

就是这样。

<figure>

[https://codepen.io/granze/embed/VEzmNZ?height=600&default-tab=result&embed-version=2](https://codepen.io/granze/embed/VEzmNZ?height=600&default-tab=result&embed-version=2)

<figcaption>You can test it by yourself</figcaption>

</figure>

## 为什么人们仍然对此感到困惑？

问题是大多数开发人员谈论“属性”，而他们实际上指的是“属性”。这两者可以相互关联，例如，我可以将属性反射到属性，但这并不总是需要的。

对于属性，您只能传递字符串或将其用作布尔值。在后一种情况下，如果属性存在，则返回 true，否则返回 false。
为了清楚起见，在下面的例子中 **att === true** 。

`<my-component att="false"></my-component>`

是误导还是不符合人体工程学？你可以对此有自己的看法，但这就是 HTML 的工作方式。在我看来，这是*一致*。

## 自定义元素的 API 不好吗？

有些人抱怨自定义元素 API 的冗长，他们认为这是完全无用的，设计糟糕。
此处赘言部分属实。以 Codepen 为例，假设您有十个属性，而不是一个。你最终会得到 10 个 getter 和 10 个 setter，在大多数情况下，它们只是在属性名称上有所不同，但是它们为每个属性做了相同的事情。
这个不方便肯定，另一方面我也不认为这代表 API 不好。
这是一个低级 API，直接使用它并不总是很方便，但是你可以在它的基础上构建。你不需要开发一个完整的框架或者库，你只需要写一些抽象就可以了。如果你不想自己做抽象，试试[超 HTML 元素](https://github.com/WebReflection/hyperHTML-Element)或 [lit 元素](https://lit-element.polymer-project.org/)它们很棒！

也就是说，如果你仍然将 Web 组件与 React、Vue、Polymer 或其他框架进行比较，你就没有抓住要点，说实话，这是不公平的。

## Web 组件随处工作

不幸的是，这种说法不太准确。这确实是 Web 组件的一大承诺，但是事情并不总是无缝地工作。如果你想了解更多关于 Web 组件、库和框架之间的互操作性，我强烈建议你去看看 Rob Dodson 的 [Custom Element Everywhere](https://custom-elements-everywhere.com/) 。你会发现它们在哪里工作，在哪里不工作，以及潜在的解决方法。

## 那么，Web 组件是一个东西吗？

在我看来，是的，绝对是。也许它们不适合你，但这并不意味着它们不好或没用。我认为这是开始使用自定义元素和阴影 DOM 的好时机。深入了解 Web 组件可能会有助于改变你的想法，以防你仍然对这个标准持怀疑态度。

如果你对这个话题感兴趣，并且想保持联系，请在 Twitter 上关注我。

* * *

最初发表于[medium.com/@granze](https://medium.com/google-developer-experts/are-web-components-a-thing-5a116b1da7e4)