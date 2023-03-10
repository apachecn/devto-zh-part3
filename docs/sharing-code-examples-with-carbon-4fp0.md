# 与 Carbon 分享代码示例

> 原文：<https://dev.to/daveskull81/sharing-code-examples-with-carbon-4fp0>

我经常发现自己在思考分享代码示例的最佳方式。Gists 似乎工作得很好，特别是对于比由几行组成的函数更复杂的东西。但是，当你有一些只有几行代码的东西时，看起来就像是多余的。
我的下一个想法是给我的文本编辑器截图。这也有缺点，因为无论我把例子放在哪里，都需要多次尝试才能得到正确的东西。

今天我看到了@emmawedekind 发的这条推文。

> ![unknown tweet media content](img/d88e81c7c538eac065666948dca0722f.png)![Emma Wedekind ✨ profile image](img/6bc3baf3fe5d83344e79bf6220016a3d.png)艾玛·韦德金德✨@ emmawedekind![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)需要找出一个字符串中最长的单词吗？可以将 string.split()方法与数组 spread 运算符+ Math.max()一起使用，在 O(n)时间内求解！2019 年 2 月 19 日下午 13:48[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1097855481052303360)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1097855481052303360)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1097855481052303360)

I was reminded that I have seen code examples that look like this before on Twitter. I really like how it looks. The code is clear, has highlighting, and the whole result just looks really polished and professional. This is the kind of resource that communicates clearly and helps to elevate the content by looking nice.
I asked Emma how they created the screenshot and they pointed out the website [Carbon](https://carbon.now.sh/) to me. It is a really neat site built by [Dawn Labs](https://dawnlabs.io/) that is free to use. You can choose from various themes to adjust the colors. There is a setting for the language you are using so that it gets the highlighting right. You can export the image out to PNG or SVG for use online or just tweet the picture directly from there if you want.
The results look really nice.
![Example Javascript code](img/413a96c4ad9a77871ccc3a6750dd8e2a.png)
I really like how it takes the guess work out of creating code example images and leaves you able to focus on the code that you are trying to share. I definitely suggest checking it out and seeing if it can help you.

你以前用过碳吗？我很乐意看到人们如何在自己的工作中使用这一点的例子。请在评论中告诉我！

**更新(2019 年 3 月 2 日)**
许多人在评论中指出了仅使用图像来共享代码的可访问性问题，因为它阻碍了使用屏幕阅读器的人完全阅读内容。屏幕阅读器不会读出图像的内容，而是使用图像的 Alt 文本集。
我们还注意到，当代码在图像中时，不能选择它进行复制和粘贴，以便在文本编辑器中查看。
这两个观点都很有道理。我仍然认为 Carbon 是一个很棒的应用程序，非常有用，但这表明它不是一个完整的解决方案，应该与其他共享代码的方法结合起来，以确保每个人都能得到他们需要的东西。
我还是会说用它来分享 Twitter 上的代码片段，因为此时在一条推文中真的没有更好的方法了。否则你必须链接到一个要点或另一个地方。至于在像文章这样的内容中使用代码图像，我会考虑使用图像作为唯一的标题或表示部分，然后嵌入一个要点或其他东西，允许屏幕阅读器运行，并像一些人建议的那样让代码更容易共享。或者使用图像显示代码，并在解释代码的文本中链接到更容易阅读的版本。如果你打算用图像作为交流代码的主要方式，确保为它设置好 Alt 文本，以帮助任何使用屏幕阅读器的人。
有时候，很多事情很难做对。我在推特上从 Marcy Sutton 那里找到了一些很好的资源，Marcy Sutton 回复了这条推特。

*   [备选决策树](https://www.w3.org/WAI/tutoriaimg/decision-tree/)——帮助决定在`alt`属性中给出多少信息。

*   [屏幕阅读器用户如何访问网络](https://www.smashingmagazine.com/2019/02/accessibility-webinar/)-Smashing Magazine 的网上研讨会展示了盲人在线使用屏幕阅读器的体验。