# 合并还是改变基础:这是一个问题

> 原文：<https://dev.to/runtime-revolution/to-merge-or-to-rebase-that-is-the-question-1jai>

<figure>[![](img/410fae53305c9f941ff5213530e149ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v4TtskRS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AB8N2nz2sDy_-AM2k) 

<figcaption>摄影:巴勃罗·加西亚·萨尔达尼亚上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

这是引发所有开发人员讨论的永恒问题之一，其重要性不亚于:

*   制表符与空格
*   静态类型与动态类型
*   韩索罗 vs 格里多
*   雅各布 vs 爱德华

为了使事情更简单，我将使用一个类比。让我们暂时远离代码和编程。

### 整合一个论文研究团队

1.  你最近整合了一群正在写一篇大文章或论文的人。你可以想象，你不会做所有的工作，你团队的其他人也不会。这项工作将被分成相当均等的部分，这样团队的每个成员都可以专注于他们的工作分支。
2.  你团队中的某个成员是首席研究员。这意味着他们的决定和评论更有分量；万一打成平手，就是他们说了算。因此，我们可以说，你的首席研究员的工作是你的真理之源。
3.  你被分配了一个新的研究章节。你在谷歌上搜索一些关于这个主题的现有论文(基于主要的研究文件)，找到一些图片，绘制一些图表，得出一些结论，等等。几天后，首席研究员说其他同事完成了他们那部分的工作，那部分已经合并到主文档中了(正如我前面提到的，这是你的真实来源)。

<figure>[![](img/57578d1c33921f79440379caffa8cf9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_MNqlUho--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Aj1P7vX5MnDq89jQa) 

<figcaption>照片由[乔恩·泰森](https://unsplash.com/@jontyson?utm_source=medium&utm_medium=referral)上传 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

从现在开始你应该如何继续你的工作？到目前为止，你将如何把最新的东西融入到你所做的研究中？如果它与你目前拥有的相冲突怎么办？因为主要文件是你的事实来源，你知道它是由你的同事和首席研究员批准的，所以你必须从那里开始工作！

### 添加新信息/获取更新

你认为它会如何发生:

[![](img/e392dc6c124a8b4f0ec1dadea8ad9851.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_oLgUiXO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/320/1%2AYD5uNhM9JRtwXNnFiy0oQw.gif)

<figcaption>【zipper gif @[【https://giphy . com/gif/CGI-fs2 giqcngt 292】](https://giphy.com/gifs/cgi-FS2GiqcNGT292)</figcaption>

最有可能发生的情况(希望伤亡更少):

<figure>[![](img/8b5129e3f3c020646d69ed37302c7039.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kmlh8ZD3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/480/1%2AkHykMUvIrwn2w8pXGdYPfg.gif) 

<figcaption>Git 合并 GIF @[https://tenor.com/view/git-merge-gif-5920259](https://tenor.com/view/git-merge-gif-5920259)</figcaption>

</figure>

在*你是一个重基础纯粹主义者*的指责开始涌入之前，让我说一下 git merge 有它的优势(当然有！)，同样 git rebasehas 也有它的缺点。经历过的人都知道，当你使用 git merge 时，解决合并冲突更容易，也不那么麻烦。

但是，我要关注的不是哪个更好，而是哪个应该用在哪些场合。

回到研究论文的类比，一旦你得到了主文档的更新版本，*重新调整*你的工作将会产生一个更加连贯的工作。如果您将所有内容合并在一起，可能会导致定义冲突和页面混乱(如果您从第 20 页开始工作，而最新的文档有 23 页，那么您的工作不属于第 20 页——与您的提交将在主分支的提交之间拼接的方式相同)。

此外，当检查日志时，我们将看到两端(主分支和我们的分支)的变化。如果我们将我们的更改放在最近的提交之上，将会有一条直线详细描述每件事情的完成顺序，因此我们最终会有一个更连贯和更清晰的日志历史(并且我们也不必通读所有合并分支开发到 dl/feat-123/example 提交消息)。

### 将你的研究合并到主文档中

现在你已经整理好了你的工作，是时候在*将*合并到主文档之前进行最后的检查了。通过这样做，你的工作被添加到主要研究中的那一刻将会很突出，因为每个人都会知道你的工作是什么时候被介绍的。

### 回码

我认为我之前解释的大部分内容是不言自明的。不管怎样，这里有一个快速的代码回顾:

1.  你被添加到一个回购
2.  有人给你分配了一个特性(假设主分支是开发)
3.  有人更新开发，所以你应该重新基地你的分支
4.  完成后，打开一个 pull 请求，检查您的代码，并将其合并到 develop 中

### 总结一切

如果将来你对是否应该合并或重定基数有任何疑问，请记住以下几点:

*   总是基于父分支:这样你的工作将总是在最新版本的工作之上(并且你避免了多次合并提交，保持你的分支历史更清晰)
*   总是合并到父分支:当您合并您的分支时，您将知道何时完成，因为将有一个合并提交来声明它！

无论是前端、后端、移动还是 DevOps，在 Runtime Revolution 中，我们都有适合您的位置！

* * *