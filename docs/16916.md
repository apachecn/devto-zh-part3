# 使用 Git 进行部分提交

> 原文：<https://dev.to/jessekphillips/partial-commits-with-git-3g9d>

我成功地为我们的回购带来了更好的承诺。

我偶然发现一个像这样的帖子。

[![sublimegeek image](img/fda6a4eb3547e12cc3c7555c6d294d8d.png)](/sublimegeek) [## 带有表情符号的语义提交消息

### 乔纳森·欧文 8 月 1 日 185 分钟阅读

#git #engineering #emoji #ui](/sublimegeek/semantic-commit-messages-with-emojis-3p8h)

我不太喜欢表情符号，人们可能会过分使用表情符号，但其他人确实喜欢，所以当你给他们一个像 [gitmoji](https://gitmoji.carloscuesta.me) 这样的仪表板，并告诉他们将表情符号添加到他们的提交消息中时，你开始谈论消息中的所有表情符号，你的提交应该只需要一个...

这让我想到了部分提交。参见 git 有一个与工作树完全分离的登台区。许多人知道您可以暂存文件，而不提交其他文件。

使用正确的工具(git gui ),您可以只准备一行代码。当我第一次提出拆分提交时，人们的反应是害怕，因为隐藏和编辑它们是如此痛苦。当 ui 没有使这个特性变得明显并且在 git 培训中没有提到它时，他们为什么期望能够控制行呢？