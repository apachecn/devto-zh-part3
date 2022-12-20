# Git 保存快照，其实不是

> 原文：<https://dev.to/jessekphillips/git-saves-snapshots-well-not-really--2bkc>

我看过几篇文章，试图将 git 解释为一个快照系统，而不是变化系统。我真的不确定这个解释对使用 git 有什么帮助。特别是因为 git 并不总是保存整个 blobs，而是使用各种部分文件差异来节省空间。

Git 还非常擅长将更改从一个地方移动到另一个地方，而不仅仅是 blobs。

所以也许有人可以解释为什么我会想用快照来解释 git。