# 为什么会引入这个 bug？

> 原文：<https://dev.to/jessekphillips/why-was-this-bug-introduced--393d>

在以前的文章中，我建立了在修改代码时可以交流的内容。几周或几年后，我们会有一个问题，为什么要引入这个 bug？

通常这是由 PM 或客户在发现 bug 时发生的，也有一个类似的问题将责任完全归咎于测试人员，为什么你没有发现这一点？但是让我们谈谈为什么作为一个开发者你会问自己第一个问题。

一旦你发现问题所在，这个问题就会被提出来。因为这是 80-90%的战斗，你只有一点时间去阅读代码并调整以避免你所处的情况。

你读过那一行的注释了吗，解释为什么把它放在那里的那个？如果您的更改只是恢复了一个不同 bug 的提交，会怎么样呢？通过提交进行良好的通信，每一行代码都有这个问题的答案，并且使用 git 责备来提取。

Git 责备用添加/修改代码的 commit 注释每一行代码。您可以跳回历史，查看文件/行是如何随时间变化的，最重要的是查看该变化的提交和消息。

对于其他 80%的帮助，有 git 平分。

[https://git-scm.com/book/en/v2/Git-Tools-Debugging-with-Git](https://git-scm.com/book/en/v2/Git-Tools-Debugging-with-Git)

[![eamodio image](img/6dc40487fabecf50b491fa4948720605.png)](/eamodio) [## 这不仅仅是责备的问题

### Eric Amodio 1916 年 1 月 16 日 14 分钟阅读

#vscode #git #github #gitlens](/eamodio/its-not-just-about-theblame-32a2)