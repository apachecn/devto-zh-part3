# 面向开发人员的高效 Git

> 原文：<https://dev.to/juristr/productive-git-for-developers-3237>

关于版本控制系统，在开始我的第一份真正的工作之前，我首先在很短的时间内使用了 CVS，然后是 SVN，公司一直在使用 TFS (Team Foundation Server)。TFS 与 SVN 类似，有可能“阻止”对文件的访问。一种避免合并冲突的方法。这是非常同步的，只要你是一个人或者团队中有两个人，它就能工作，但是它绝对不能扩展(更不用说如果有人去度假了，忘记签入会发生什么)。

最终，checkout => lock => checkin 工作流不再工作了。此外，分支/合并是一种痛苦。那是 Git 变得更加流行的时候，我开始仔细观察它😃).所以我得到的任务是更深入地研究 git，并最终让我们的团队(是的，多个团队)转向使用 Git。

如你所知 **Git 确实很强大，但是强大的能力也意味着巨大的责任**。当您从 TFS 开始时，情况就更是如此了，因为那里的文件实际上是被锁定的。你只需要工作流，如何使用 git，何时分支，如何将它们合并回来等等。否则你会落得一塌糊涂。当时已经有不同的工作流可用，Git 流方法，后来 GitHub 流和 GitLab 流出现了。我总是从一个更加基于[主干的开发](https://trunkbaseddevelopment.com)场景中获得灵感。在那里你有一条主线(不管它是主干还是主干，或者你想叫它什么)，在那里人们快速而频繁地融合在一起。保留长寿的分支几乎总是会导致大量的工作和混乱的合并。

无论如何，得出一个结论。这些年来，我与许多不同的团队合作过，我经常看到人们与 Git 合并或使用奇怪的工作流(比如手动复制文件夹进行备份)进行斗争。许多人不知道非常简单的命令可以对你的日常工作流程产生巨大的影响。所以几年后，我终于决定坐下来录制一系列视频，根据我每天使用的 Git 命令展示一些真实世界的食谱&。没有什么太花哨，但只是几个简单的命令，将有助于您完成这项工作。

## Egghead:开发者的高效工具

[![](img/42e0bc8ab0f0f728f3fd81f161185584.png)](https://egghead.io/courses/productive-git-for-developers)

通过这个课程，我瞄准了一个不同的目标。您将经历一系列场景，这些场景是您作为开发人员在日常工作中最常遇到的。我在说:

*   用主数据库中的最新更改更新特征分支
*   润色你的 git 历史，为同行评审做好准备
*   将一组提交移动到另一个分支
*   撤销意外提交

我们的目标不是涵盖所有内容，而是那些将会极大地提高您使用 Git 的日常工作效率的任务。

如果你感兴趣，可以在 Egghead.io 上查看一下[。](https://egghead.io/courses/productive-git-for-developers)

以下是本课程的当前课程(随着时间的推移，还会有新的课程更新):

*   [仅将我当前修改的一些文件提交到存储库](https://egghead.io/lessons/git-only-commit-some-of-my-currently-modified-files-into-the-repository)
*   [让我的 git 日志看起来漂亮易读](https://egghead.io/lessons/git-make-my-git-log-look-pretty-and-readable)
*   [将一些提交移动到一个单独的分支，我不小心将它提交给了 master](https://egghead.io/lessons/git-move-some-commits-to-a-separate-branch-that-i-have-accidentally-committed-to-master)
*   [用主模块的最新变化更新我的特征分支](https://egghead.io/lessons/git-update-my-feature-branch-with-the-latest-changes-from-master)
*   [通过`--force-with-lease`](https://egghead.io/lessons/git-push-a-rebased-local-branch-by-using-force-with-lease) 推送重定基数的地方分行
*   [在合并或提交评审之前润色我的 git 特性分支](https://egghead.io/lessons/git-polish-my-git-feature-branch-before-merging-or-submitting-for-review)
*   [用 Git Autosquash 自动清理我的特性分支](https://egghead.io/lessons/git-automate-the-cleanup-of-my-feature-branch-with-git-autosquash)
*   [将我所有的提交压缩成一个并合并到主文件中](https://egghead.io/lessons/git-squash-all-of-my-commits-into-a-single-one-and-merge-into-master)
*   [更改我上次提交的提交消息](https://egghead.io/lessons/git-change-the-commit-message-of-my-last-commit)
*   [将我忘记添加的文件添加到我上次提交的文件中](https://egghead.io/lessons/git-add-a-file-i-ve-forgotten-to-add-to-my-last-commit)
*   [撤销我的最后一次提交，把它分成两个独立的部分](https://egghead.io/lessons/git-undo-my-last-commit-and-split-it-into-two-separate-ones)
*   [从我的本地分支擦除提交](https://egghead.io/lessons/git-wipe-a-commit-from-my-local-branch)
*   [撤销已经推送到远程存储库的提交](https://egghead.io/lessons/git-undo-a-commit-that-has-already-been-pushed-to-the-remote-repository)
*   临时存储一些正在进行的工作，因为我必须跳到另一个分支

让我知道你的想法😃