# 通过探索。git 目录

> 原文：<https://dev.to/daolf/git-series-13-understanding-git-for-real-by-exploring-the-git-director--5bd0>

> # "Wow, I just read this quick tuto about git. Gosh, it's so cool. I think it's super comfortable to use now, and I'm not afraid of falling things at all. " -said no one ever.

作为初学者使用 git 就像对于一个不会读/说当地语言的人来说去一个新的国家一样。一旦知道自己在哪里，要去哪里，一切都好，但是迷路的那一刻，大麻烦就开始了(#badMetaphor)。

有很多关于学习 git 基本命令的帖子，这不是其中之一。我要尝试的是一种不同的方法。

<figure>

[![[xkcd](https://xkcd.com/1597/)](img/94d362f4ceb3573c6944aa1024c98531.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mhvsP1bC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A0o9GZUzXiNnI4poEvxvy8g.png%23center)

<figcaption>[XKCD](https://xkcd.com/1597/)</figcaption>

</figure>

新用户通常害怕 git，真的，很难不害怕。这无疑是一个强大的工具，但它并不真正用户友好。许多新概念和命令做着完全不同的事情，不管文件是否作为参数传递，隐含的反馈…

我认为克服第一个困难的一个方法是做一些比 git commit/push 更多的事情，我认为如果我们花时间真正理解 git 是由什么组成的，它可以帮你省去很多麻烦。

### 进入。饭桶

那么，我们开始吧。当您使用 git init 创建 git repo 时，git 会创建一个很棒的目录:. git。这个文件夹包含 git 工作所需的所有信息。明确地说，如果您想从项目中删除 git，但保留项目文件，只需删除。git 文件夹。但是拜托，你为什么要这么做？

```
 ├── HEAD
    ├── branches
    ├── config
    ├── description
    ├── hooks
    │ ├── pre-commit.sample
    │ ├── pre-push.sample
    │ └── ...
    ├── info
    │ └── exclude
    ├── objects
    │ ├── info
    │ └── pack
    └── refs
     ├── heads
     └── tags 
```

Enter fullscreen mode Exit fullscreen mode

这是你的。在第一次提交之前，git 看起来像:

*   头

我们稍后会谈到这个

*   配置

这个文件包含了你的存储库的设置，将会写下远程的 url 例如，你的邮件，用户名等等。每次你在控制台中使用‘git config…’时，它都在这里结束。

*   描述

git web(github 的前身)使用它来显示回购的描述。

*   钩住

这里有一个有趣的特点。Git 附带了一组脚本，您可以在每个有意义的 git 阶段自动运行这些脚本。这些脚本称为挂钩，可以在提交/重置/拉取之前/之后运行…脚本的名称决定了何时执行它。一个有用的预推挂钩的例子是测试所有的样式规则是否被遵守，以保持远程(远程存储库)的一致性。

*   信息—排除

所以您可以将不希望 git 处理的文件放在您的。gitignore 文件。嗯，排除文件是一样的，只是它不会被共享。如果你不想跟踪你的自定义 IDE 相关的配置文件，即使大多数时候。gitignore 就够了(如果真的用这个请在评论里告诉我)。

### 提交里面是什么？

每当你创建一个文件，并跟踪它，git 压缩它，并将其存储到自己的数据结构中。压缩后的对象将有一个唯一的名称，即一个散列，并将存储在对象目录下。

在探索对象目录之前，我们必须问自己什么是提交。所以提交有点像你的工作目录的快照，但是它比这多一点。

事实上，当您提交时，git 只做两件事来创建工作目录的快照:

1.  如果文件没有改变，git 只是将压缩文件的名称(散列)添加到快照中。
2.  如果文件发生了变化，git 会对其进行压缩，并将压缩文件存储在 object 文件夹中。最后，它将这个压缩文件的名称(散列)添加到快照中。

这是一个简化，整个过程有点复杂，将是未来帖子的一部分。

一旦创建了快照，它也将被压缩并以哈希命名，那么所有这些压缩对象的最终位置在哪里呢？在对象文件夹中。

```
├── 4c
│ └── f44f1e3fe4fb7f8aa42138c324f63f5ac85828 // hash
├── 86
│ └── 550c31847e518e1927f95991c949fc14efc711 // hash
├── e6
│ └── 9de29bb2d1d6434b8b29ae775ad8c2e48c5391 // hash
├── info // let's ignore that
└── pack // let's ignore that too 
```

Enter fullscreen mode Exit fullscreen mode

这是在我创建了一个空文件 file_1.txt 并提交它之后对象目录的样子。请注意，如果您的文件的散列是“4 cf 44 f1 e……”，git 会将该文件存储在“4c”子目录下，然后将该文件命名为“f44 f1……”。这个小技巧将/objects 目录的大小减少了 255。

你看 3 哈希右。所以一个是我的 file_1.txt，另一个是我提交时创建的快照。第三个是什么？因为提交本身就是一个对象，所以它也被压缩并存储在对象文件夹中。

您需要记住的是，提交由 4 件事情组成:

1.  工作目录快照的名称(哈希)

2.  一个评论

3.  委员会信息

4.  父提交的哈希

就是这样，自己看看如果我们解压缩提交文件会发生什么:

```
// by looking at the history you can easily find your commit hash
// you also don't have to paste the whole hash, only enough 
// characters to make the hash unique

git cat-file -p 4cf44f1e3fe4fb7f8aa42138c324f63f5ac85828 
```

Enter fullscreen mode Exit fullscreen mode

这是我得到的

```
tree 86550c31847e518e1927f95991c949fc14efc711
author Pierre De Wulf <test[@gmail.com](mailto:pierredewulf31@gmail.com)> 1455775173 -0500
committer Pierre De Wulf <[test@gmail.com](mailto:pierredewulf31@gmail.com)> 1455775173 -0500

commit A 
```

Enter fullscreen mode Exit fullscreen mode

您看，我们得到了预期的结果，快照散列、作者和我的提交消息。这里有两件事很重要:

1.  正如所料，快照哈希“86550…”也是一个对象，可以在 object 文件夹中找到。

2.  因为这是我第一次提交，所以没有父级。

我的快照里到底有什么？

```
git cat-file -p 86550c31847e518e1927f95991c949fc14efc711

100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 file_1.txt 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们找到了之前在我们的对象存储中的最后一个对象，也是快照中唯一的对象。这是一个斑点，但这是另一个故事。

### 分支，标记，头都一样

所以现在您明白了，git 中的所有内容都可以通过正确的散列来实现。现在让我们来看看头部。那脑袋里是什么？

```
cat HEAD
ref: refs/heads/master 
```

Enter fullscreen mode Exit fullscreen mode

好的，这不是一个散列，它是有意义的，因为头部可以被认为是一个指针，指向你正在处理的分支的顶端。现在，如果我们看看 refs/heads/master 中的内容，我们会看到:

```
cat refs/heads/master
4cf44f1e3fe4fb7f8aa42138c324f63f5ac85828 
```

Enter fullscreen mode Exit fullscreen mode

这看起来眼熟吗？是的，这和我们第一次提交的哈希完全一样。这向您展示了分支和标记只不过是指向提交的指针。这意味着你可以删除所有你想要的分支，所有你想要的标签，它们指向的提交仍然会在这里。只有更难接近的。如果你想了解更多这方面的知识，去查一下 git 书籍。

### 最后一件事

所以到现在为止，您应该明白 git 在提交时所做的只是“压缩”您当前的工作目录，并将它和一堆其他信息一起存储到 objects 文件夹中。但是，如果您对该工具足够熟悉，您将会完全控制哪些文件应该包括在提交中，哪些文件不应该。

我的意思是提交并不是你的工作目录的快照，它是你想要提交的文件的快照。在进行实际的提交之前，git 在哪里存储您想要提交的文件呢？它将它们存储在索引文件中。我们现在不打算深究，同时，如果你真的很好奇，你可以看看这个。

## 感谢阅读:

我希望你能从这篇文章中学到一些有价值的东西，并且它能让你更容易地使用 git。

你可以在这里阅读第二部[。](https://www.daolf.com/posts/git-series-part-2)

如果你喜欢 JS，我刚刚发表了一些你可能会喜欢的东西:

[![daolf](img/279327c39e65b127ba43b78117e6e2f0.png)](/daolf) [## 冒泡和捕获 JS 事件

### 皮埃尔 3 月 1 日 195 分钟阅读

#javascript #webdev #beginners #tutorial](/daolf/things-you-should-know-about-js-events-4k2l)

请在评论中告诉我你最近的困惑，不要害羞🙂如果你喜欢这篇文章，别忘了给我的时事通讯订阅,还有更多(你也可以免费得到我下一本电子书的第一章)😎).